---
description: Generate a blog post matching Jay Grossman's writing style and blog conventions
user-invocable: true
---

# Write a Blog Post for JayGrossman.com

You are writing a blog post for Jay Grossman's Jekyll blog at jaygrossman.github.io. Follow these instructions precisely to match Jay's established writing style and blog conventions.

The user will provide: **$ARGUMENTS**

If no topic is provided, ask the user what they'd like to write about.

---

## Step 1: Gather Information

Before writing, ask the user for:
1. **Topic**: What is the post about?
2. **Category type**: Is this a technical tutorial, personal story, opinion/reflection, or project showcase?
3. **Key points**: What are the main things to cover?
4. **Any code/tools involved?** If technical, what languages/frameworks/tools?

If the user provided enough detail in their initial prompt, skip redundant questions and proceed.

---

## Step 2: Front Matter Format

Every post MUST use this exact front matter structure:

```yaml
---
layout: post
title:  "Title Goes Here"
author: jay
tags: [ tag1, tag2, tag3 ]
image: assets/images/headers/descriptive-name.webp
description: "A brief one-sentence description of the post."
featured: false
hidden: false
comments: false
---
```

**Rules:**
- `layout` is always `post`
- `author` is always `jay`
- Tags: 3-6 lowercase tags in bracket array format. Use existing tags when possible: python, data science, machine learning, data engineering, javascript, sports cards, collectz, ebay, chrome, extension, automation, web development, games, silly, family, sports, wrestling events, dbt, duckdb, snowflake, orchestration, dagster, singer, meltano, workflow, open source, fantasy football
- `image`: path under `assets/images/headers/`, use `.webp`, `.png`, or `.jpg`. Use underscores or hyphens in the filename.
- `description`: quoted, one sentence
- `featured`: always `false` unless user says otherwise
- `hidden`: always `false`
- `comments`: always `false`
- File name format: `YYYY-MM-DD-title_with_underscores.md` saved in `_posts/`

---

## Step 3: Writing Style Guide

### Voice & Tone
- **First person throughout** ("I built", "I noticed", "I wanted to")
- **Conversational but knowledgeable** - you're an experienced engineer sharing what you learned, not lecturing
- **Direct** - get to the point, don't over-explain obvious things
- **Self-aware humor** - occasional self-deprecating jokes, pop culture references, and genuine enthusiasm
- **Personal anecdotes** - weave in real-world context (family, hobbies, work experiences) when relevant
- **Honest about failures** - mention what didn't work and what you learned from it
- Never use emojis

### Avoiding LLM-sounding writing
The output must read like a human wrote it. Watch out for these common tells:
- **Avoid overused LLM phrases**: "a fundamental tension", "a natural next step", "the common thread is", "it's worth noting", "future you will be grateful", "that's a pretty good place to be", "a special kind of", "at the end of the day"
- **Avoid textbook vocabulary**: words like "monolithic", "idempotent", "orchestrate" when simpler words work ("one big script", "repeatable", "coordinate")
- **Don't define vocab words mid-sentence** like "This is called checkpointing." — just show the concept through code or explanation
- **Don't use the `**Bold lead-in.** Explanation follows.` pattern** repeatedly in lists — it's an LLM signature
- **Don't be too comprehensive or balanced** — a real person goes deep on what they struggled with and breezes past the obvious stuff. Not every topic deserves equal coverage.
- **Don't wrap up too neatly** — skip the tidy numbered summary at the end restating everything. End with a thought, not a recap.
- **Vary paragraph and section structure** — don't follow the same "concept statement, explanation, code block, tidy summary" pattern for every section
- **Include rough edges** — mention a specific thing that went wrong, a decision you're unsure about, something you tried that didn't work before landing on the solution

### Structure

**Opening (1-2 paragraphs):**
- Start with context or a personal hook that explains WHY you're writing this
- Connect the topic to a real problem or interest
- No "In this post I will..." preamble - just start naturally

**Body:**
- Place an `<hr style="border: 1px solid #ccc; margin: 40px 0;">` before each major section (H2)
- Use **H2 (`##`)** for major sections, **H3 (`###`)** for subsections
- Sentence case for all headings
- Keep paragraphs short (2-4 sentences)
- Use bullet lists and numbered lists freely for clarity
- For technical posts: Problem → Solution → Implementation → Results → Learnings
- For personal posts: narrative arc with specific details (dates, names, places)

**Images:**
- Use HTML format with centered alignment:
  ```html
  <p style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/images/image-name.png" alt="descriptive alt text" width="600" />
  </p>
  ```
- Add captions with `<small>` tags when helpful

**Code blocks:**
- Always specify the language: ```python, ```sql, ```javascript, ```bash, etc.
- Show complete, runnable examples when possible
- Keep snippets focused (10-50 lines ideal)
- Explain the "why" before showing code, not just the "what"

**Links:**
- Use HTML `<a>` tags with `target="_blank"` for external links:
  ```html
  <a href="https://example.com" target="_blank">link text</a>
  ```

**Note/disclaimer boxes** (when needed):
```html
<table style="border: 2px solid red;">
<tr><td style="padding: 10px;">
<b>Please Note:</b> Important disclaimer text here.
</td></tr>
</table>
```

**Tables:**
- Use HTML `<table>` format (not markdown tables)
- Include borders and padding for readability

**Closing (1-3 paragraphs):**
- "What I learned" or "Thoughts" section for technical posts
- Reflect on the experience - what worked, what surprised you
- Optionally link to a GitHub repo or related resources
- End naturally, no forced call-to-action

### Length Guidelines
- Short post (personal note, simple tutorial): 1,000-2,000 words
- Medium post (project walkthrough, opinion piece): 2,500-4,000 words
- Long post (deep technical dive, detailed story): 4,000-6,000+ words
- Match length to the topic's complexity - don't pad

---

## Step 4: Write the Post

1. Generate the complete post with front matter
2. Save it to `_posts/` with the correct filename format
3. Remind the user they'll need a header image at the path specified in the front matter
4. If the post references images in the body, note which images are needed

---

## Example Patterns from Jay's Posts

**Technical opening:**
> I've been working with [technology] for [context], and I kept running into [problem]. I decided to build [solution] to see if I could make it work better.

**Personal opening:**
> Back in [year], I [personal memory]. That experience stuck with me because [reason].

**Transition to code:**
> Here's what the [component] looks like:

**Reflecting on results:**
> This worked [well/okay/not great] because [reason]. If I were to do it again, I'd probably [improvement].

**Closing pattern:**
> Overall, this was a fun project that taught me [lessons]. The code is available on [GitHub link] if you want to try it yourself.
