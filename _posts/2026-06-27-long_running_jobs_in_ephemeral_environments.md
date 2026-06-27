---
layout: post
title:  "Considerations for Long Running Jobs in Ephemeral Environments"
author: jay
tags: [ data engineering, python, automation ]
image: assets/images/headers/jobs_in_ephemeral_environments.png
description: "Practical patterns for running 10+ hour jobs in environments that can disappear at any time."
featured: false
hidden: false
comments: false
---

I have a pretty ambitious weekly job that calculates demand scores and pricing estimates for 100,000's of items. It uses a combination of ML models and complex transformations — pulling in historical sales data, active sales data, indications of sentiment, and other data sources to spit out updated valuations across different variations and conditions. The whole thing takes somewhere between 10 to 12 hours to complete.

For some time, I was running this on my laptop. That meant keeping it open and active the entire time — no closing the lid, no letting it sleep, no taking it anywhere. I wanted to move all of this off my machine and into an environment where I didn't have to babysit it.

I like the idea of using a cloud hosted runtime environment like AWS as a cost effective solution — you can spin it up whenever you want to run workloads and destroy it when you're done. But this kind of environment is, by nature, temporary. I've been burned by this — I kicked off the job on a Friday night, checked in Saturday morning, and the container had been evicted just before hour 9. Nothing was saved and I was sad. I had to restart the whole thing over and hope I would have better luck for the next run.

That experience forced me to rethink how the job was structured. Here's what I've learned.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## What is an ephemeral environment?

An ephemeral environment is a compute resource that's designed to be temporary. It gets created on demand, runs your workload, and then gets destroyed. Think containers, AWS Step Functions, spot instances, or even AI coding agent sessions. You don't maintain them — you treat them as disposable.

They're cheap and they scale well, but they don't promise your process will run to completion. Kubernetes pods get evicted when nodes run low on resources. Step Functions have state transition limits. Claude Code routines and Cursor automations can lose their session mid-run. If your job is one big script that runs for 12 hours, you're gambling every time you start it.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## Break the work into batches

The single most important thing I did was stop thinking of my job as one giant task and start thinking of it as a bunch of smaller tasks.

Instead of processing all 100,000+ items in a single loop, I break them into batches. Each batch is just a chunk of items — maybe 500 or 1,000 — that can be processed independently.

```python
import pandas as pd
import os

def get_all_item_ids(input_file):
    df = pd.read_csv(input_file)
    return df['item_id'].tolist()

def create_batches(item_ids, batch_size=500):
    return [item_ids[i:i + batch_size] for i in range(0, len(item_ids), batch_size)]

def process_batch(batch, batch_num, output_dir="output/batches"):
    results = []
    for item_id in batch:
        result = calculate_demand_and_pricing(item_id)
        results.append(result)

    # write batch results to a csv
    df = pd.DataFrame(results)
    output_file = os.path.join(output_dir, f"batch_{batch_num:04d}.csv")
    df.to_csv(output_file, index=False)
    return output_file
```

Each batch produces its own output file. If your environment dies after processing 150 of 200 batches, you've still got 150 batch files sitting there with valid results.

Getting the batch size right took some trial and error. I started with 100 items per batch, but the overhead of writing all those files and checking progress was adding up. Bumped it to 2,000 and then a failure wiped out 20 minutes of work instead of 2. I settled on 500 — each batch takes a few minutes, which feels like the right amount of work to lose if something goes wrong.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## Checkpointing and resumability

Batching alone isn't enough. You also need a way to know which batches are done so you can skip them on restart. AKA checkpointing.

Since the job is running in an ephemeral environment, it can't write to my local machine. I store all batch outputs in S3 so they're accessible no matter where the script runs. Any cloud storage would work (GCS, Azure Blob, etc.) — the point is that your storage has to outlive your compute.

My checkpoint logic is simple: if a batch output file exists in S3, that batch is done. When the job starts (or restarts), it lists the bucket to see which batch files are already there and skips them.

```python
import boto3
import io

s3 = boto3.client('s3')
BUCKET = 'my-batch-jobs'
BATCH_PREFIX = 'pricing-job/batches/'

def get_completed_batches():
    completed = set()
    paginator = s3.get_paginator('list_objects_v2')
    for page in paginator.paginate(Bucket=BUCKET, Prefix=BATCH_PREFIX):
        for obj in page.get('Contents', []):
            key = obj['Key']
            filename = key.split('/')[-1]
            if filename.startswith("batch_") and filename.endswith(".csv"):
                batch_num = int(filename.replace("batch_", "").replace(".csv", ""))
                completed.add(batch_num)
    return completed

def run_job(input_file, batch_size=500):
    item_ids = get_all_item_ids(input_file)
    batches = create_batches(item_ids, batch_size)

    completed = get_completed_batches()
    print(f"Found {len(completed)} completed batches, {len(batches) - len(completed)} remaining")

    for i, batch in enumerate(batches):
        if i in completed:
            continue
        print(f"Processing batch {i + 1}/{len(batches)}")
        process_batch(batch, i)

    # combine all batch files into final output
    combine_batch_results()
```

Kill it, restart it on a completely different machine, and it picks up right where it stopped.

The batch gets fully generated on the ephemeral compute first, then uploaded to S3 as a single `put_object` call. Either the upload completes and the checkpoint exists, or it doesn't and there's nothing to skip on the next run.

```python
def process_batch(batch, batch_num):
    results = []
    for item_id in batch:
        result = calculate_demand_and_pricing(item_id)
        results.append(result)

    df = pd.DataFrame(results)
    csv_buffer = io.StringIO()
    df.to_csv(csv_buffer, index=False)

    key = f"{BATCH_PREFIX}batch_{batch_num:04d}.csv"
    s3.put_object(Bucket=BUCKET, Key=key, Body=csv_buffer.getvalue())
```

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## Progress tracking

When a job runs for hours, you want to know where it's at. Since I'm already using S3 for batch outputs, I write a progress file there too:

```python
import time
import json
from datetime import datetime

def update_progress(batch_num, total_batches, start_time):
    elapsed = time.time() - start_time
    completed = batch_num + 1
    rate = completed / elapsed if elapsed > 0 else 0
    remaining = (total_batches - completed) / rate if rate > 0 else 0

    progress = {
        "completed_batches": completed,
        "total_batches": total_batches,
        "percent_complete": round(100 * completed / total_batches, 1),
        "elapsed_seconds": round(elapsed),
        "estimated_remaining_seconds": round(remaining),
        "last_updated": datetime.now().isoformat()
    }

    s3.put_object(
        Bucket=BUCKET,
        Key=f"{BATCH_PREFIX}progress.json",
        Body=json.dumps(progress, indent=2)
    )

    print(f"Progress: {progress['percent_complete']}% | "
          f"ETA: {round(remaining / 60)} minutes remaining")
```

I can check progress from anywhere with a quick `aws s3 cp` or just look at the print output in CloudWatch. When the job restarts after a failure, it's also easy to see how much work is left.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## Parallelizing the work

Once batching and checkpointing are working, you can start running multiple batches at the same time. Since each batch is independent, this is pretty straightforward.

I started with Python's `concurrent.futures` on a single machine, which is the simplest option by far:

```python
from concurrent.futures import ProcessPoolExecutor, as_completed

def run_job_parallel(input_file, batch_size=500, max_workers=4):
    item_ids = get_all_item_ids(input_file)
    batches = create_batches(item_ids, batch_size)
    completed = get_completed_batches()

    pending = [(i, batch) for i, batch in enumerate(batches) if i not in completed]
    print(f"Processing {len(pending)} batches with {max_workers} workers")

    with ProcessPoolExecutor(max_workers=max_workers) as executor:
        futures = {
            executor.submit(process_batch_atomic, batch, i): i
            for i, batch in pending
        }

        for future in as_completed(futures):
            batch_num = futures[future]
            try:
                future.result()
                print(f"Batch {batch_num} complete")
            except Exception as e:
                print(f"Batch {batch_num} failed: {e}")
```

With 4 workers, my 12-hour job dropped to about 3 hours. The checkpoint files mean you don't need to coordinate between workers — each one writes its own output file, and there's no overlap.

If single-machine parallelism isn't enough, you can distribute batches across multiple containers using a queue like SQS. A coordinator pushes batch definitions onto a queue, workers pull from the queue and process them, and a combiner merges the results at the end. The nice thing about a queue is that workers are disposable — if one dies, the message goes back on the queue and another worker picks it up.

For my use case, multiprocessing on one machine has been plenty. I'd only reach for distributed workers if I needed to process everything in under an hour or if individual batches needed more memory than a single machine could offer.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## A few other things worth mentioning

"Hey Jay, aren't you a data engineer? Aren't there robust orchestration tools like Airflow, Dagster, Prefect that are made to handle running jobs on a CRON?" Yes, and they're great at scheduling and managing complex pipelines, but they don't magically make your code fault-tolerant. If your Airflow task runs for 12 hours in a single shot and the worker dies at hour 9, you're in the same boat. You still need batching, checkpointing, and resumability within the task itself. For a single weekly job on a personal project, a cron trigger plus these patterns gets me everything I need without the operational overhead of maintaining an orchestrator.

Retry logic within each batch matters. Network timeouts and transient errors are normal over a 10-hour run. I use a simple retry wrapper with exponential backoff — if a single item fails, retry that item, don't redo the whole batch.

```python
import time

def retry(func, max_attempts=3, backoff_base=2):
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            wait = backoff_base ** attempt
            print(f"Attempt {attempt + 1} failed: {e}. Retrying in {wait}s...")
            time.sleep(wait)
```

Make your operations idempotent too. If a batch gets partially processed and then re-run, the output should replace the old one completely — not append to it. The S3 upload handles this naturally since `put_object` overwrites whatever was there before.

<hr style="border: 1px solid #ccc; margin: 40px 0;">

## Where I ended up

My new setup for this job is a <a href="https://cursor.com/automate" target="_blank">Cursor Automation</a>:

<p align="center">
<img src="/assets/images/headers/cursor_automation.png" alt="Cursor Automation setup" >
</p>

It is connected to a private GitHub repo and runs on a weekly schedule (using the cheapest model available) with a simple prompt:

```bash
run these commands as-is:

pip install -r requirements.txt
python jobs/demand_and_valuation_script.py
```

That's it — no custom infrastructure, no server to maintain. The automation spins up, runs the job, and goes away. If it fails midway through, I kick it off again and pick up from the last completed batch.

 Now that I have this working, I stopped worrying about ephemeral environments entirely. The job doesn't care where it runs, as long as it can talk to S3.
