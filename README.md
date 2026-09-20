# The Unofficial Guide

**Kevon Dawkins — Corpus: `city_guides`**

---

# Unit 1

## What This Does

This project is a retrieval-augmented generation system that answers questions
using information from the `city_guides` corpus. The corpus contains regional
travel guides covering transportation, food, accessibility, seasons, towns,
and other practical travel information. The system splits the guides into
chunks, creates embeddings, retrieves relevant chunks for a question, and uses
those retrieved documents to generate a grounded answer with sources.

## Chunking Strategy

**Chunk size:** Variable — one Markdown section per chunk  
**Overlap:** None

I changed the starter's fixed 800-character chunking strategy because the city
guide documents are organized into meaningful Markdown sections such as
transportation, food, accessibility, and seasonal information. When I inspected
the starter chunks, some began or ended in the middle of a sentence because the
starter split documents based only on character count.

My chunker instead splits documents at `##` Markdown section headings so that
each chunk keeps a complete topic together. I did not use overlap because the
sections in these guides are already designed to contain a complete topic, and
repeating text between sections would create unnecessary duplicate information.
Using this strategy produced 98 chunks for the `city_guides` corpus.

## Sample Chunks

**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`

```text
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.
```

**Chunk 2** — source: `guide_corry_vale.md#6` — produced by: `chunker.py::split_documents`

```text
## When to go

May to September. Outside those months the pub in the third village closes, the farm shop reduces its hours, and several footpaths become genuinely boggy rather than merely wet. The road is not gritted above the second village and is impassable in snow.
```

**Chunk 3** — source: `guide_givens_mill.md#3` — produced by: `chunker.py::split_documents`

```text
## Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells bread made from the flour ground twenty metres away and is the reason most people come. One pub, food served lunchtimes and Thursday to Saturday evenings.
```

**Chunk 4** — source: `guide_kestrelford.md#6` — produced by: `chunker.py::split_documents`

```text
## When to go

Late spring and early autumn. The Saturday market runs year-round but is much reduced from November to February. August is busy with walkers. The single-track approach road is genuinely difficult in snow and the town can be cut off for a day or two most winters.
```

**Chunk 5** — source: `guide_regional_transport.md#1` — produced by: `chunker.py::split_documents`

```text
## The railway

The line runs along the river valley, connecting Brightwater to the regional
hub in 50 minutes. Eleven services a day on weekdays, six on Sundays. The line
north of Brightwater closed in 1963 and everything beyond it is bus or car.

Tickets are cheaper booked the day before than on the day, and considerably
cheaper than that booked a week ahead. There is no ticket office at
Brightwater station outside weekday mornings; the machine on the platform takes
cards only.
```

## Sample Answer

<!-- Milestone 4: We will replace this after testing retrieval. -->

**Question:**

**Answer:**

```text

```

**My relevance cutoff:**

<!-- Milestone 4: We will add the cutoff and all ten retrieval distances here. -->

| Question | In corpus? | Best distance |
|---|---|---|
|  |  |  |

## How I Used AI

<!-- Milestone 5: We will complete this with two specific examples. -->

**1.**

**2.**

---

# Unit 2

<!-- These sections will be completed during Unit 2. Do not delete Unit 1. -->

## Run Log — Before

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. Sampled chunks contain complete, usable information | 4 of 5 |  |  |  |  |
| 5. Sources actually support the answers | 5 of 5 |  |  |  |  |

## Verdicts

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer |  |  |
| 2 | Every answer names a source |  |  |
| 3 | The relevance gate stops out-of-corpus questions |  |  |
| 4 | Sampled chunks contain complete, usable information |  |  |
| 5 | Sources actually support the answers |  |  |

## Diagnoses

<!-- Unit 2: For each missed criterion, explain which pipeline stage caused
     the problem and how it caused it. -->

## The Improvement

**What I changed:**

**Why I picked it:**

### Run Log — After

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. Sampled chunks contain complete, usable information | 4 of 5 |  |  |  |  |
| 5. Sources actually support the answers | 5 of 5 |  |  |  |  |

**Did it help?**

## What's Still Broken

<!-- Unit 2: Describe anything that still misses the acceptance criteria. -->

## What I'd Do Differently

<!-- Unit 2: Explain which acceptance criteria you would change after seeing
     the results and why. -->