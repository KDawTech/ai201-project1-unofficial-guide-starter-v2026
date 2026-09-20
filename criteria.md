# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
The city guide corpus sometimes repeats or spreads information across a town-specific guide and a regional guide. I chose 4 out of 5 because I expect retrieval to find the correct information most of the time, while allowing one difficult question where the relevant information may be surrounded by similar travel information.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
Every answer should be based on the retrieved city guide documents rather than unsupported information. Since the pipeline already keeps track of the source file for retrieved chunks, all five answers should be able to name at least one source.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

**Why this target:**
The city guide corpus only covers travel information about the provided region, so questions about unrelated topics should normally have poor retrieval matches. I chose 4 out of 5 because embedding similarity is not perfect and one unrelated question could accidentally retrieve a chunk that appears somewhat similar.

---

## 4. Sampled chunks contain complete, usable information

At least 4 of 5 sampled chunks should contain a complete thought that could be
used to answer a question without requiring the missing beginning or ending of
a sentence.

**Why this target:**
The city guide documents are organized into short sections such as transportation, food, accessibility, and seasons. A useful chunk should preserve enough of one of those sections to make sense by itself. I chose 4 out of 5 because an occasional boundary between sections may still produce a less complete chunk.

---

## 5. Sources actually support the answers

For all 5 test questions, at least one source named by the system must contain
information that directly supports the answer that was produced.

**Why this target:**
Simply displaying a source name is not enough if the cited document does not support the answer. Because each of my five questions has a specific answer in the city guide corpus, I expect every generated answer to be traceable to at least one retrieved source document.
 
