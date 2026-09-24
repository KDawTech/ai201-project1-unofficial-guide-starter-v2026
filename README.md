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

**3.** In Unit 2, I used AI to help compare the before and after evaluation
results and implement a hybrid retrieval experiment using semantic similarity
and BM25 keyword matching. I still used the actual committed run logs to judge
the results. The comparison showed that all five criteria remained met, but the
change did not produce a clear overall improvement.

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

The Kestrelford bus service runs hourly on weekdays.

Source: guide_regional_transport.md

Best distance: 0.2576
```

The expected answer was `hourly`, and the same question was answered correctly
in all three runs.

### Evidence for Criterion 2

Every generated answer named at least one source document.

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

```text
## The railway

The line runs along the river valley, connecting Brightwater to the regional
hub in 50 minutes. Eleven services a day on weekdays, six on Sundays.
```

The sampled chunks contained complete thoughts without missing sentence
beginnings or endings.

### Evidence for Criterion 5

The named source documents directly supported the generated answers.

```text
Question: How many railway services run to Brightwater on Sundays?

Six railway services run to Brightwater on Sundays
(from guide_regional_transport.md).
```

The `guide_regional_transport.md` document contains the six-services-on-Sundays
fact used in the answer.

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

The best retrieval distances for the five in-corpus questions were approximately
0.258, 0.445, 0.480, 0.270, and 0.302. All were below the 0.62 relevance
cutoff.

Because every criterion passed, my original targets may have been conservative.
Criterion 1 was originally set to 4 of 5 questions, but the system successfully
handled 5 of 5 in every run. I would use a stricter 5-of-5 target in the future.

## The Improvement

**What I changed:**

I changed retrieval from semantic-vector search alone to a hybrid retrieval
strategy in `store.py::search`.

The new strategy combines:

- semantic similarity from the existing embedding search
- BM25 keyword matching
- a combined hybrid score used to rank the returned chunks

The semantic and keyword scores were weighted equally.

**Why I picked it:**

The system already met all five criteria, so there was no failed criterion to
repair directly. I chose hybrid search as a controlled retrieval experiment
because the corpus contains exact town names, times, numbers, and transportation
terms. BM25 can reward exact keyword matches while the existing vector search
handles semantic similarity.

I made only this one system change before running the after evaluation.

### Run Log — After

The complete after-run evidence is stored in:

`results/run_2026-09-23_2123_after.md`

The after evaluation again used the `city_guides` corpus, top-k 5, cutoff 0.62,
and three uncached runs per test question.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks contain complete, usable information | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Sources actually support the answers | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

### After-run evidence

The Kestrelford bus question remained correct in all three runs:

```text
The bus to Kestrelford runs hourly on weekdays.

Sources: guide_regional_transport.md and guide_kestrelford.md
```

The bakery question remained correct:

```text
Kestrelford's bakery usually sells out by 11am
(from guide_eating.md and guide_kestrelford.md).
```

The accessibility question remained correct:

```text
Thornby Wells is described as the easiest town in the region for visitors
with limited mobility (guide_accessibility.md).
```

The railway question remained correct:

```text
Six railway services run to Brightwater on Sundays
(guide_regional_transport.md).
```

The Halden Bay question remained correct:

```text
To avoid the parking problem when visiting Halden Bay in August,
visitors should arrive before 10am (guide_seasons.md).
```

The relevance gate again refused all five out-of-scope questions:

```text
refused  (best distance 0.896)  What is the capital of Mongolia?
refused  (best distance 0.932)  How do I change the oil in a diesel engine?
refused  (best distance 0.899)  Who won the 1994 World Cup?
refused  (best distance 0.904)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.874)  How do I write a for loop in Rust?

gate refused 5 of 5
```

### Before vs. After Retrieval Distances

| Question | Before | After | Change |
|---|---:|---:|---:|
| Kestrelford weekday bus | 0.2576 | 0.2576 | 0.0000 |
| Kestrelford bakery | 0.4450 | 0.4450 | 0.0000 |
| Limited mobility / Thornby Wells | 0.4804 | 0.5104 | +0.0300 |
| Brightwater Sunday railway | 0.2701 | 0.2701 | 0.0000 |
| Halden Bay August parking | 0.3019 | 0.3019 | 0.0000 |
| Capital of Mongolia | 0.754 | 0.896 | +0.142 |
| Diesel engine oil | 0.892 | 0.932 | +0.040 |
| 1994 World Cup | 0.899 | 0.899 | 0.000 |
| Ibuprofen dosage | 0.846 | 0.904 | +0.058 |
| Rust for loop | 0.813 | 0.874 | +0.061 |

**Did it help?**

The hybrid-search improvement produced a mixed result rather than a clear
overall improvement.

All five acceptance criteria were still met after the change, and all 15
generated answers remained correct and sourced.

For four of the five in-corpus questions, the best reported semantic distance
was unchanged. The accessibility question became slightly less close, moving
from about 0.480 to 0.510, although it still passed the 0.62 gate and produced
the correct answer.

For four of the five tested out-of-scope questions, the best distance in the
returned hybrid result set increased, giving the relevance gate more margin
from the 0.62 cutoff. The World Cup question stayed at 0.899.

Because the acceptance-criterion scores stayed identical before and after, I
cannot claim that hybrid search clearly improved the system. It changed the
retrieval ranking while preserving answer quality, and it created a larger
observed separation for several unrelated test questions, but one relevant
question's retrieval distance became worse.

## What's Still Broken

No acceptance criterion remained missed after the improvement.

However, the hybrid search did not improve the measured criterion scores because
the original system already achieved the maximum result on these five test
questions.

The accessibility question also showed a small retrieval regression, with its
best distance increasing from approximately 0.480 to 0.510. Although that was
still safely below the 0.62 cutoff and the answer remained correct, it shows
that hybrid ranking can favor exact-keyword matches differently from pure
semantic retrieval.

If I continued working on the system, I would test the semantic/BM25 weighting
on a larger set of questions instead of assuming that a 50/50 combination is
the best balance.

## What I'd Do Differently

I would rewrite Criterion 1 more strictly.

The original criterion was:

> For at least 4 of my 5 test questions, the retrieved chunks include one that
> contains the answer.

Knowing what I know now, I would use:

> For all 5 of my 5 test questions, the retrieved chunks include at least one
> chunk containing the information needed to answer the question.

The original 4-of-5 target was too lenient for this corpus because both the
before and after evaluations successfully handled all five questions in every
run.

I would also use a larger and more varied set of retrieval test questions in a
future version so that an improvement such as hybrid search has more difficult
cases on which to demonstrate whether it actually helps.