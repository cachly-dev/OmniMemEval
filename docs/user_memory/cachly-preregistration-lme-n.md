# cachly: preregistration of LongMemEval-S run "lme-n"

Written on 2026-11-16, **before** the run starts. This file is published on the
public fork first, so its commit time can be checked. It is not part of any
pull request to MemTensor/OmniMemEval.

## Baseline

The published cachly LongMemEval row (89.60, 448 of 500) is run r2 of
2026-11-14. A question-by-question comparison of r2 with Maasv's public records
(maasv-ai/omnimemeval-results, release `lme-20261101-r2`) found:

- 16 questions only cachly answers correctly, and 24 only Maasv answers correctly.
- Of those 24:
  - 7 are retrieval gaps: a short user sentence is missing from cachly's context.
  - 11 are answer errors with the evidence present.
  - 6 are judge noise.
- In cachly's context, 13.7 % of the characters come from user turns. In Maasv's, 23.9 %.

## What changes

Only cachly's own add/search service changes. The OmniMemEval harness, the
`cachly` adapter, top-k 20, the answer and judge models and all prompts stay
as in r2.

- `AML_STUECKE=nutzer`: the r2 records stay. Every user message is also stored
  as its own short record. A search drops that short record when the record
  holding the same message is already among the results.
- `AML_MARKE=uhrzeit`: each record starts with `[2023-05-26 01:08 · #273]`
  (UTC time and a running number) instead of `[session of 26 May 2023]`.

Both changes run together. Their separate effects cannot be told apart afterwards.

## Thresholds, fixed now

| | Questions correct of 500 |
| --- | ---: |
| Main goal | ≥ 457 |
| Improvement signal | ≥ 451 |
| Regression: switches stay off | < 446 |

One run varies by about ±5 questions: 29 judgments changed between r1 and r2.
So even 451 is not a statistically established improvement.

## Stop rule before any answer or judge call

Ingestion and search run first. Answer and judge calls run only if all four
checks pass. The evidence share of a question is the mean share of 6-word
shingles of its `has_answer` turns found in the search context.

1. At least 4 of the 7 retrieval-gap questions recover: their evidence share
   rises by at least 0.20 over r2, or reaches at least 0.95. The questions are
   gpt4_2f56ae70, d01c6aa8, 0977f2af, gpt4_1e4a8aec, gpt4_194be4b3, 2ce6a0f2
   and 6d550036.
2. At most 2 of the 16 questions only cachly answered correctly in r2 lose
   evidence, meaning their share falls by more than 0.20.
3. The median context size per question stays between 3,000 and 5,500 tokens.
   It was 4,416 in r2.
4. Ten random contexts are read by hand. Time, number and order must be
   correct, and no sentence may appear twice.

## Reporting

- The result is recorded whether it is better or worse.
- A replacement of the published row is proposed only if the main goal is
  reached. Such a proposal names the new service revision, this file and the run.
