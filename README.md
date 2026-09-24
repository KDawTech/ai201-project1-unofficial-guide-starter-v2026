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

May to September. Outside those months the pub in the third village closes, the
farm shop reduces its hours, and several footpaths become genuinely boggy rather
than merely wet. The road is not gritted above the second village and is
impassable in snow.
```

**Chunk 3** — source: `guide_givens_mill.md#3` — produced by: `chunker.py::split_documents`

```text
## Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells
bread made from the flour ground twenty metres away and is the reason most
people come. One pub, food served lunchtimes and Thursday to Saturday evenings.
```

**Chunk 4** — source: `guide_kestrelford.md#6` — produced by: `chunker.py::split_documents`

```text
## When to go

Late spring and early autumn. The Saturday market runs year-round but is much
reduced from November to February. August is busy with walkers. The single-track
approach road is genuinely difficult in snow and the town can be cut off for a
day or two most winters.
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

**Question:** How many railway services run to Brightwater on Sundays?

**Answer:**

```text
Six railway services run to Brightwater on Sundays (guide_regional_transport.md).

Sources retrieved: guide_kestrelford.md, guide_marchwood.md,
guide_regional_transport.md, guide_thornby_wells.md
```

**My relevance cutoff:** 0.62

I chose a relevance cutoff of 0.62 after comparing the best retrieval
distances for my five in-corpus questions against five clearly out-of-scope
questions. The in-corpus distances ranged from 0.2576 to 0.4865, while the
out-of-scope distances ranged from 0.7542 to 0.8990. There was a clear gap
between 0.4865 and 0.7542, so I selected 0.62 near the middle of that gap.

| Question | In corpus? | Best distance |
|---|---|---:|
| How often does the bus to Kestrelford run on weekdays? | Yes | 0.2576 |
| What time does Kestrelford's bakery usually sell out? | Yes | 0.4450 |
| Which town is easiest for visitors with limited mobility? | Yes | 0.4865 |
| How many railway services run to Brightwater on Sundays? | Yes | 0.2701 |
| What time should visitors arrive at Halden Bay in August to avoid parking problems? | Yes | 0.2928 |
| What is the capital of Mongolia? | No | 0.7542 |
| How do I change the oil in a diesel engine? | No | 0.8917 |
| Who won the 1994 World Cup? | No | 0.8990 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8459 |
| How do I write a for loop in Rust? | No | 0.8130 |

## How I Used AI

**1.** I used AI while setting up the project when the dependencies failed to
install with Python 3.12. The error showed that `chroma-hnswlib` was trying to
build from source and required Microsoft C++ Build Tools. AI helped me identify
that Python 3.11 was already installed on my computer and suggested recreating
the virtual environment with Python 3.11 instead. I followed that approach,
reinstalled the requirements, and `python test.py` then passed all 10
environment checks.

**2.** I used AI while redesigning the chunking strategy after inspecting the
starter chunks. The starter's fixed-size chunks sometimes began or ended in the
middle of sentences. AI suggested using the Markdown structure of the
`city_guides` documents instead. I changed `split_documents` so that it splits
at `##` section headings and uses no overlap. After rebuilding the index, the
sample chunks contained complete sections and were produced by
`chunker.py::split_documents`.

---

# Unit 2

## Run Log — Before

Evaluation evidence was produced by `run_eval.py::main`.

Retrieval was performed by `store.py::search`, using chunks produced by
`chunker.py::split_documents`.

The complete raw evaluation output is stored in:

`results/run_2026-09-23_2106_before.md`

The evaluation used the `city_guides` corpus, top-k 5, a relevance cutoff of
0.62, and three uncached runs per in-corpus question.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain complete, usable information | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Sources actually support the answers | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

### Evidence for Criterion 1

The retrieved information supported the expected answers for all five test
questions.

Example from `results/run_2026-09-23_2106_before.md`, produced by
`run_eval.py::main`:

```text
Question: How often does the bus to Kestrelford run on weekdays?

Run 1:
The Kestrelford bus service runs hourly on weekdays.

Source: guide_regional_transport.md

Best distance: 0.2576
```

The expected answer was `hourly`, and the retrieved material supported that
answer. The same question was answered correctly in all three runs.

### Evidence for Criterion 2

Every generated answer named at least one source document.

Example from `results/run_2026-09-23_2106_before.md`, produced by
`run_eval.py::main`:

```text
Question: What time does Kestrelford's bakery usually sell out?

Kestrelford's bakery usually sells out by 11am.

Sources: guide_eating.md and guide_kestrelford.md
```

All five questions included source attribution in all three runs.

### Evidence for Criterion 3

The relevance gate was evaluated by `run_eval.py::check_out_of_scope` with the
0.62 cutoff.

```text
refused  (best distance 0.754)  What is the capital of Mongolia?
refused  (best distance 0.892)  How do I change the oil in a diesel engine?
refused  (best distance 0.899)  Who won the 1994 World Cup?
refused  (best distance 0.846)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.813)  How do I write a for loop in Rust?

gate refused 5 of 5
```

The target was at least 4 of 5, so refusing all 5 exceeded the target.

### Evidence for Criterion 4

The sampled chunks were produced by `chunker.py::split_documents`.

One example was:

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

This chunk contains complete sentences and enough context to answer a question
without needing text before or after it. All five sampled chunks met the same
standard.

### Evidence for Criterion 5

The source documents named in the generated responses directly supported their
answers.

Example from `results/run_2026-09-23_2106_before.md`:

```text
Question: How many railway services run to Brightwater on Sundays?

Six railway services run to Brightwater on Sundays
(from guide_regional_transport.md).
```

The `guide_regional_transport.md` source states that there are six railway
services on Sundays, so the cited source directly supports the generated answer.

Another example was:

```text
Question: What time should visitors arrive at Halden Bay in August to avoid
the parking problem?

To avoid the parking problem when visiting Halden Bay in August, you should
arrive before 10am.

Sources: guide_seasons.md and guide_halden_bay.md
```

The named sources contain the information used in that answer.

## Verdicts

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MET | All five test questions produced the expected answer in all three runs, and the retrieved source material contained the required information. |
| 2 | Every answer names a source | MET | Every generated answer across all three runs named at least one source document. |
| 3 | The relevance gate stops out-of-corpus questions | MET | The gate refused all 5 of the 5 out-of-scope questions, exceeding the target of 4 of 5. |
| 4 | Sampled chunks contain complete, usable information | MET | All five sampled chunks were complete enough to understand without missing beginnings or endings of sentences. |
| 5 | Sources actually support the answers | MET | For all five questions, the named source documents contained information that directly supported the generated answers. |

## Diagnoses

None of my five acceptance criteria were missed during the before evaluation.

All five in-corpus questions were answered correctly across all three runs,
every generated answer included a source, and the relevance gate refused all
five out-of-scope questions.

The retrieval distances also remained consistent across the three runs because
retrieval is deterministic. The best distances for the five test questions
were approximately 0.258, 0.445, 0.480, 0.270, and 0.302, all below the 0.62
relevance cutoff.

Because every criterion passed, my original targets may have been somewhat
conservative. Criterion 1 was originally set to 4 of 5 questions, but the system
successfully handled 5 of 5 in all three runs. Knowing this result now, I would
consider a stricter 5 of 5 target in the future.

## The Improvement

**What I changed:**

To be completed after selecting and implementing one Project 2 improvement.

**Why I picked it:**

To be completed after connecting the improvement to the evaluation results and
diagnosis above.

### Run Log — After

The after evaluation will be generated with:

```text
python run_eval.py --label after
```

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. Sampled chunks contain complete, usable information | 4 of 5 |  |  |  |  |
| 5. Sources actually support the answers | 5 of 5 |  |  |  |  |

**Did it help?**

To be completed after comparing the before and after evaluation runs.

## What's Still Broken

To be completed after the after evaluation.

## What I'd Do Differently

To be completed after the after evaluation.