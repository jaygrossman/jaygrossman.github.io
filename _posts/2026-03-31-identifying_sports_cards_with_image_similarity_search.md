---
layout: post
date: 2026-03-31
title:  "Identifying Sports Cards with Image Similarity Search"
author: jay
tags: [ sports cards, machine learning, python, collectz ]
image: assets/images/headers/identify_cards_with_similarity_search.png
description: "Building a visual similarity search pipeline to identify sports cards from photos using SigLIP embeddings and FAISS"
featured: false
hidden: false
comments: false
---

<p>I have been collecting sports cards for over 40 years and have amassed a pretty big collection. A while back I built <a href="http://collectz.com/" target="_blank">Collectz</a> to help me manage my collection and use data to find arbitrage opportunities in the collectibles market. A foundational component is a large catalog of cards with images — and once you have images, you can start doing interesting things with them.</p>

<p>One thing that comes up constantly when sorting through boxes of cards is the question: <strong>what card am I actually looking at?</strong>  If you have a card in your hand and want to look it up, you typically need to know the player, year, brand, and card number. Sometimes that's easy. But for newer or unfamiliar sets, it can be a real pain to figure out.</p>

<p>I have seen other sites and apps offer the ability to take a photo of a card and have it identified. <a href="https://www.ximilar.com/blog/get-an-ai-powered-trading-card-price-checker-via-api/" target="_blank">Ximilar.com</a> and <a href="https://cardsight.ai/" target="_blank">Cardsight.ai</a> offer paid commercial APIs for this.  I thought it would be cool to build something like that myself using modern AI tools.</p>

<h2>The Challenge:</h2>

<h4><span style="color: #ff0000;"><em><strong>Given a photo of a sports card, I want to identify the card by finding the most visually similar cards in a catalog of 16.4 million cards.</strong></em></span></h4>

<hr>
<h2>My Solution: Visual Similarity Search Pipeline</h2>

<p>A traditional approach to this problem (that I tried in the past) involves training an image classification model or an object detection pipeline. This includes labeling thousands of cards, training a model to recognize specific sets or players, and retraining whenever new cards are added to the catalog. That's a LOT of up front and ongoing work.</p>

<p>I thought using vector embeddings could be a cool alternative. Instead of training a classifier, you just run every card image through a pre-trained vision model to get a numerical representation (an embedding) of what the card looks like. Then to identify a new card, you embed it the same way and find the closest matches in your catalog. No custom training required — and when new cards are added, you just embed them and they're immediately searchable.</p>

<p><b>TLDR</b> — I built a pipeline that takes a photo of a card, converts it into a vector embedding using a vision model, then searches a FAISS index of 3.4 million pre-computed card image embeddings to find the closest matches. The whole thing runs locally on my M1 MacBook.</p>

<p>Here is the high-level architecture:</p>

<div style="display: flex; align-items: stretch; justify-content: center; flex-wrap: nowrap; margin: 30px 0; overflow: hidden;">
  <div style="background: #3498db; color: #fff; padding: 8px 4px; border-radius: 8px; font-weight: bold; text-align: center; font-size: 0.75rem; width: 90px; min-width: 90px; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; padding-top: 12px;">
    <i class="fas fa-camera" style="margin-bottom: 4px;"></i>Photo<br>&nbsp;
  </div>
  <div style="color: #555; font-size: 1rem; display: flex; align-items: center; padding: 0 4px; flex-shrink: 0;"><i class="fas fa-arrow-right"></i></div>
  <div style="background: #9b59b6; color: #fff; padding: 8px 4px; border-radius: 8px; font-weight: bold; text-align: center; font-size: 0.75rem; width: 90px; min-width: 90px; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; padding-top: 12px;">
    <i class="fas fa-microchip" style="margin-bottom: 4px;"></i>SigLIP<br>Embedding
  </div>
  <div style="color: #555; font-size: 1rem; display: flex; align-items: center; padding: 0 4px; flex-shrink: 0;"><i class="fas fa-arrow-right"></i></div>
  <div style="background: #e67e22; color: #fff; padding: 8px 4px; border-radius: 8px; font-weight: bold; text-align: center; font-size: 0.75rem; width: 90px; min-width: 90px; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; padding-top: 12px;">
    <i class="fas fa-search" style="margin-bottom: 4px;"></i>FAISS<br>Search
  </div>
  <div style="color: #555; font-size: 1rem; display: flex; align-items: center; padding: 0 4px; flex-shrink: 0;"><i class="fas fa-arrow-right"></i></div>
  <div style="background: #e74c3c; color: #fff; padding: 8px 4px; border-radius: 8px; font-weight: bold; text-align: center; font-size: 0.75rem; width: 90px; min-width: 90px; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; padding-top: 12px;">
    <i class="fas fa-database" style="margin-bottom: 4px;"></i>DuckDB<br>Lookup
  </div>
  <div style="color: #555; font-size: 1rem; display: flex; align-items: center; padding: 0 4px; flex-shrink: 0;"><i class="fas fa-arrow-right"></i></div>
  <div style="background: #00ab6b; color: #fff; padding: 8px 4px; border-radius: 8px; font-weight: bold; text-align: center; font-size: 0.75rem; width: 90px; min-width: 90px; display: flex; flex-direction: column; align-items: center; justify-content: flex-start; padding-top: 12px;">
    <i class="fas fa-check-circle" style="margin-bottom: 4px;"></i>JSON<br>Results
  </div>
</div>

<p>The key components:</p>

<table class="table table-striped table-bordered">
<thead>
<tr>
  <th>Component</th>
  <th>Role</th>
</tr>
</thead>
<tbody>
<tr>
  <td><a href="https://huggingface.co/timm/ViT-B-16-SigLIP-i18n-256" target="_blank"><strong>SigLIP</strong></a> (ViT-B-16-SigLIP)</td>
  <td>Vision model that converts card images into 768-dimensional vector embeddings</td>
</tr>
<tr>
  <td><a href="https://github.com/facebookresearch/faiss" target="_blank"><strong>FAISS</strong></a> (IVF Flat, Inner Product)</td>
  <td><b>F</b>acebook <b>AI</b> <b>S</b>imilarity <b>S</b>earch - library for fast approximate nearest neighbor search across millions of vectors</td>
</tr>
<tr>
  <td><a href="https://duckdb.org/" target="_blank"><strong>DuckDB</strong></a></td>
  <td>Stores the card catalog metadata (16.4M cards) and was used during the embedding process</td>
</tr>
</tbody>
</table>

<hr>
<h2>The Data</h2>

<p>The foundation of this project is the card catalog I had already built for <a href="http://collectz.com/" target="_blank">Collectz</a>. The <code>collectz.duckdb</code> database contains a <code>card_catalog</code> table with 16.4 million cards. Each card record includes metadata like player name, set name, year, card number, and a path to a stock image.</p>

<p>Of those 16.4M cards, about 3.45 million have real stock images (not just default placeholders). Those are the ones I could embed and search against.</p>

<hr>
<h2>Embedding 3.4 Million Card Images</h2>

<p>The first major step was generating a vector embedding for every card image in the catalog. This involved downloading each image from S3, running it through the SigLIP vision model, and storing the resulting 768-dimensional vector.</p>

<p>This process took about <strong>5 days</strong> of cumulative runtime on my M1 MacBook, with frequent breaks to let the machine cool down. Thermal throttling was a real issue — sustained embedding would drop from ~15 cards/sec down to ~2 cards/sec as the laptop heated up.</p>

<h4>Optimizations I applied along the way:</h4>

<ol>
<li><strong>Parallel downloads</strong> (ThreadPoolExecutor) — gave me a ~10x speedup over downloading images one at a time</li>
<li><strong>Producer-consumer pipeline</strong> — downloads happen in a background thread while the GPU processes the previous batch, overlapping I/O and compute</li>
<li><strong>Bulk DB writes</strong> — single <code>UPDATE FROM</code> via temp table instead of individual UPDATEs per row</li>
<li><strong>Larger batch sizes</strong> — bumped from 64 to 128 images per GPU batch</li>
<li><strong>Prefetch queue</strong> — up to 3 batches downloaded ahead so the GPU is never waiting on the network</li>
</ol>

<p>Final sustained rate: <strong>~15 cards/sec</strong> (bottlenecked by MPS GPU encoding on the M1 chip).</p>

<p>For error handling, transient failures (connection timeouts) were simply retried by re-running the script. Permanent errors (404s from S3 — about 3,500 cards) were logged and skipped. Final coverage: <strong>3,429,911 cards embedded</strong> — 99.5% of those with images.</p>

<hr>
<h2>Making Search Fast with FAISS</h2>

<p>My first attempt at search used DuckDB's built-in <code>list_cosine_similarity()</code> function to brute-force compare the query embedding against all 3.4M stored embeddings. It worked, but each query took 5-10 seconds — not great.</p>

<p>The fix was to build a <a href="https://github.com/facebookresearch/faiss" target="_blank">FAISS</a> index. FAISS is Facebook's library for efficient similarity search over large collections of vectors. I built an IVF (Inverted File) index with 1,852 clusters and inner product similarity.</p>

<p>The result:</p>
<ul>
<li>Index file: ~10GB on disk (<code>faiss.index</code>)</li>
<li>Search time: <strong>&lt;1ms</strong> (was 5-10 seconds with DuckDB brute force)</li>
</ul>

<h4>Memory-mapped loading:</h4>
<p>Loading the 10GB FAISS index into memory was slow (~21 seconds at startup). Switching to <code>faiss.IO_FLAG_MMAP</code> (memory-mapped I/O) fixed this — the index gets loaded on-demand from disk, bringing total query time down from ~21s to ~7.7s.</p>

<hr>
<h2>Simplifying the Pipeline</h2>

<p>An earlier version of the pipeline used <a href="https://ollama.com/" target="_blank">Ollama</a> running Gemma3 (a 4B parameter vision-language model) for two extra stages: identifying the player name from the card image, and then confirming whether the top FAISS matches were correct. This added ~12-14 seconds per query and didn't meaningfully improve accuracy for the common case, so I removed those stages from the default pipeline.</p>

<p>The final pipeline is straightforward:</p>
<ol>
<li>Embed the query image with SigLIP</li>
<li>Search the FAISS index for nearest neighbors</li>
<li>Look up card metadata in DuckDB</li>
<li>Return JSON results with similarity scores</li>
</ol>

<hr>
<h2>Performance</h2>

<table class="table table-striped table-bordered">
<tr><td><strong>Catalog size</strong></td><td>16,465,615 cards</td></tr>
<tr><td><strong>Cards embedded</strong></td><td>3,429,911 (99.5% of those with images)</td></tr>
<tr><td><strong>Query time</strong></td><td>~7.7 seconds</td></tr>
<tr><td><strong>Embedding model</strong></td><td>SigLIP ViT-B-16 (768-dim)</td></tr>
<tr><td><strong>Index type</strong></td><td>FAISS IVFFlat, 1,852 clusters</td></tr>
</table>

<p><br/>The ~7.7 second query time breaks down as:</p>
<ul>
<li>~5-6s: Loading the SigLIP model (cold start)</li>
<li>~1-2s: Embedding the query image on MPS</li>
<li>&lt;1ms: FAISS search</li>
<li>&lt;100ms: DuckDB metadata lookup</li>
</ul>

<p>The model cold start dominates. This could be eliminated by running a persistent server (like FastAPI) that keeps the model loaded in memory.</p>

<hr>
<h2>Usage and Results</h2>

<p>To show how this works in practice, I scanned a 1977 Topps Harold Carmichael card in an album page from my collection:</p>

<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/images/card_identifier/1774277243-1_4.png" alt="Harold Carmichael 1977 Topps card photo" width="40%" style="border: 1px solid #000000;" /></p>

<p>Running it through <code>card_search.py</code> with the <code>--top-k</code> flag set to return the top 5 matches:</p>

```bash
python card_search.py 1774277243-1_4.png --top-k 5
```

<p>Returns the top 5 matches — the correct card comes back first with a 0.919 similarity score, followed by other Harold Carmichael cards from neighboring years:</p>

```json
[
    {
      "player": "Harold Carmichael",
      "set_name": "1977 Topps",
      "card_no": "144",
      "team": "Philadelphia Eagles",
      "similarity": 0.919
    },
    {
      "player": "Harold Carmichael",
      "set_name": "1976 Topps",
      "card_no": "425",
      "team": "Philadelphia Eagles",
      "similarity": 0.8678
    },
    {
      "player": "Harold Carmichael",
      "set_name": "1978 Topps",
      "card_no": "379",
      "team": "Philadelphia Eagles",
      "similarity": 0.8675
    },
    {
      "player": "Harold Carmichael",
      "set_name": "1983 Topps",
      "card_no": "137",
      "team": "Philadelphia Eagles",
      "similarity": 0.8413
    },
    {
      "player": "Harold Carmichael",
      "set_name": "1981 Topps",
      "card_no": "35",
      "team": "Philadelphia Eagles",
      "similarity": 0.8317
    }
]
```

<p>The top match nails it — 1977 Topps #144. The remaining results are all Harold Carmichael cards from other years, which makes sense since those cards share similar visual elements (Eagles uniform, similar photography style). The similarity scores drop off gradually, showing the model can distinguish between the exact card and visually related ones.</p>

<hr>
<h2>Integrating into Collectz</h2>

<p>I integrated the card identifier into <a href="http://collectz.com/" target="_blank">Collectz</a> under the Tools menu. You can drag and drop one or more card images into the upload area or click to choose files from your computer:</p>

<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/images/card_identifier/card_identifier_search.png" alt="Card Identifier upload interface on Collectz" style="border: 1px solid #000000;" />
<small><br>The Card Identifier tool on Collectz with drag-and-drop file upload</small></p>

<p>As an example, I uploaded that same photo of a 1977 Topps Harold Carmichael card from above. The tool identifies the card and returns the top matches with similarity scores:</p>

<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/images/card_identifier/card_identifier_results.png" alt="Card Identifier search results on Collectz" style="border: 1px solid #000000;" />
<small><br>The Card Identifier processing the uploaded card image</small></p>

<hr>
<h2>What I Learned</h2>

<p>A few things stood out from this project:</p>

<ol>
<li><strong>Seller photos vs stock images</strong> — Cards photographed by sellers (angled, different lighting, sometimes signed) score lower against the clean stock images in the catalog. The system usually identifies the correct player, but may not rank the exact card variant first.</li>

<li><strong>Variations of cards are hard to distinguish</strong> — Many OPC and Topps cards from the same year share the identical front photo and design. Visual similarity alone can't distinguish them — you'd need text recognition or back-of-card analysis for that.</li>

<li><strong>Thermal throttling is real</strong> — Running sustained GPU workloads on a laptop for days requires patience and cooling breaks. My M1 MacBook would drop from 15 cards/sec to 2 cards/sec when it got hot.</li>

<li><strong>FAISS mmap was the biggest single win</strong> — Switching to memory-mapped index loading eliminated multi-second startup overhead without keeping the full index resident in RAM.</li>
</ol>


