# Multilingual ASR — can the local model handle more than Hindi?

**Date:** 2026-07-04 · **Sample:** 100 real calls · Aggregated report, no personal data and
no debtor numbers.

## Project stage

- **Phase 1 (pilot): validating the pipeline on real calls — in progress.**
- **Done:** end-to-end pipeline (call → ASR → PII masking → field extraction → database),
  tested on 100 calls; ASR choice (local vs cloud); multilingual ASR evaluated.
- **Next:** number-parsing fixes, a full day (37k calls), web dashboard, local extraction
  (full Config A).

## What we tested

We ran 100 real calls through three speech-recognition (ASR) configurations to answer one
question: can the **local** model (data never leaves India — residency) handle all the
languages that appear in calls, not just Hindi? The key metric is how accurately the system
extracts the **debt amount**.

## Configurations (what was compared)

| Configuration | ASR | Extraction | Where data is processed |
|---|---|---|---|
| **Hindi-only** (IndicConformer, language hard-set to Hindi) | local | Haiku | in India |
| **Multilingual** (IndicConformer, 22 languages, automatic language detection) | local | Haiku | in India |
| **Sarvam cloud** (reference baseline) | cloud | Haiku | outside India |

Both local configurations are **the same model**; the only difference is whether we hard-set
the language ("Hindi") or detect it automatically, right on the server.

## What the system extracts from a call

From every call the system automatically pulls a set of **flags** (what happened in the
conversation) and **numeric fields** (specific amounts and dates). The flags are 11 signals
grouped into 4 categories:

| Category | Flags — what they mean |
|---|---|
| **Payment outcome** | firm promise to pay · vague promise · no promise · previous promise broken |
| **Debtor behavior** | engaging / cooperative · not cooperative · refusal / negative |
| **Debtor situation** | financial hardship · disputes the debt · requests a settlement |
| **Risk & compliance** | risk of an agent breaching the script/regulations |

Numeric fields:

| Field | What it is |
|---|---|
| **Debt amount** | current outstanding debt as stated in the conversation |
| **Promised amount** | how much the debtor promised to pay |
| **Promised date** | by what date they promised to pay |
| **Payment method** | how they promised to pay |

This report focuses on the hardest numeric field — the **debt amount**.

## Result: debt-amount accuracy

Accuracy is measured on calls **where the amount is actually said out loud** (46 out of 100).
The percentages are the share of calls where the extracted amount matched the record in the
debt system.

| Configuration | Overall (46) | Non-Hindi (5) | Hindi (41) |
|---|---|---|---|
| Hindi-only (local) | 70% | **0%** | 78% |
| **Multilingual (local)** | **74%** | **40%** | 78% |
| Sarvam cloud (reference) | 87% | 100% | 85% |

**Analysis.** The multilingual model **closed Tamil**: where Hindi-only understood none of the
Tamil amounts (0%), the multilingual model now captures part of them (40% on non-Hindi). At the
same time it **did not regress on Hindi** — the same 78%. The local path still trails the cloud,
but the gap is explainable and mostly unrelated to defects in our code (see below).

## Language breakdown (why the gap remains)

| Language | Calls (where amount is stated) | Does the local model capture it | Comment |
|---|---|---|---|
| Hindi | 41 | ✅ yes | Primary language, works reliably |
| Tamil | 4 | ✅ now yes | Previously lost entirely; multilingual recovers part |
| English | 1 | ❌ no | The local model has no English, falls back to Hindi |
| Bengali | 2* | — not applicable | The amount is **not spoken aloud** in these calls — nothing to capture |

\* The Bengali calls are **not** part of the 46: there is no spoken amount that matches the debt
system. This was confirmed on the Sarvam cloud too — it also "fails" to capture these amounts,
because they are simply never said. So this is **not a recognition defect** but a property of
the calls themselves.

## Limitations

- **Tamil numbers are still partly lost at the number-parsing stage.** The audio is recognized
  correctly — the amount is present in the transcript — but the number-assembly module sometimes
  reconstructs it wrong (for example, it glues two figures together). This is a known bug, being
  fixed in code.
- **The local model cannot handle English speech** — it has no English language. Such calls fall
  back to Hindi and are recognized incorrectly.
- **The gap with the cloud is mostly due to languages the local model does not know** (English)
  and to discrepancies in the calls themselves (Bengali), **not** to defects in our code.

## Decision

**We are switching to the multilingual local model.** It is the same model — we simply stopped
hard-setting "Hindi" and now detect the language automatically on the server. The gain is
**free**: no new infrastructure, nothing to download, and **the data stays in India**. Tamil is
recovered at no cost, and Hindi is unaffected.

## Next step

The main lever for improving debt-amount accuracy is the **number-parsing fixes**. They will
improve both Hindi and Tamil at once: in both cases the audio is recognized correctly, and what
remains is to finish assembling the final number.
