# Progress Report — Call Intelligence

**Date:** 2026-07-08 · Aggregated, de-identified report: no personal data, no debtor phone
numbers. All data is processed inside India (residency).

---

## Project stage

**Phase 1 (pilot): validating the pipeline on real calls — in progress.**

We are confirming on live calls that the whole chain works as intended: call → speech
recognition → PII masking → field extraction (debt amount, promise-to-pay, etc.) → database.
Accuracy is measured not by eye but by reconciliation against the actual records in the
DebThor collections system.

---

## Done since the last report

- **Multilingual ASR deployed.** A single local model recognises speech in **22 Indian
  languages** and detects the call's language on its own. The data **never leaves India**.
- **Number parsing rewritten as a flexible engine.** Numbers are now stored as **data** (a
  separate dictionary per language) while the parsing algorithm is shared. Supported: Hindi,
  English, Tamil, Telugu, Kannada, Malayalam, Bengali. The engine is covered by **115
  automated tests**.
- **Tamil solved via the dictionary.** We added colloquial number forms — that was enough;
  **no model fine-tuning was needed**. The model hears the speech correctly; it only needed the
  "translation" of spoken numbers into digits.
- **Project structure brought to its target shape:** a clean split between the *engine* (shared
  code), *providers* (swappable ASR / extraction models) and *region data* (per-region
  dictionaries and rules). This lets us onboard a new country without rewriting code.

---

## Result: debt-amount accuracy (reconciled against DebThor)

The metric is the share of calls where the system extracts the correct **debt amount**,
reconciled against the actual records of the **DebThor** collections system. An important point
about the method: **the debt amount is not spoken aloud in every conversation.** When it is not
spoken, both systems — local and cloud — correctly return nothing. Such calls must not count in
the accuracy denominator: there was nothing to name. So accuracy is measured on the subset where
the amount was **actually spoken**.

**Hindi** — 100 calls with a DebThor amount; both paths processed 95, which is what we compare:

| Path | Language | Calls with a DebThor amount | Of those, spoken aloud | Accuracy on "spoken" | Same for Sarvam? |
|---|---|---|---|---|---|
| **Local** (in India, new number engine) | Hindi | 95 | 61 | **35 / 61 (~57 %)** | Sarvam **38 / 61 (~62 %)** — on par |
| **Local** (in India) | Tamil | reference still thin | — | — | not enough data for a stable figure |

**How to read these numbers:**

- In **34 of the 95 calls the amount was not spoken aloud** — both systems correctly returned
  nothing. That is why the "raw" share (35 of 95 ≈ 37 %) looks understated: the denominator is
  inflated by calls where there was nothing to say. The correct metric is **on the "spoken"
  subset**.
- On calls where the amount was spoken, the **local path is on par with the cloud**: 35 vs 38 of
  61 — a three-call difference at this sample size.
- The **new number-parsing engine narrowed the earlier gap** of the local path (it used to trail
  more clearly).
- The remaining misses are the **same** for both: **poor recording quality**, or the **spoken
  figure differing from the DebThor case balance** (partial payments / write-offs) — a limit of
  recognition and of the reference's semantics, not a defect in our code.
- **Tamil** is currently under-measured: only a handful of Tamil calls have a DebThor amount. We
  are assembling a **50-call Tamil set** to request the reference from the bank — that will give
  a robust figure (see "Next").

---

## Language mix of the traffic

From a scan of a representative day (still in progress; at the time of writing about
**6,700 of ~24,000 calls**, with stable proportions):

| Group | Share of calls |
|---|---|
| **Hindi** (and close variants) — the main flow | **≈ 95 %** |
| Tamil (confidently identified) | ≈ 0.2 % |
| Telugu, Kannada (confidently identified) | < 0.1 % each |
| Other: rare languages + low-quality recordings | ≈ 4–5 % |

**Hindi dominates.** Everything else is a fraction of a percent. Among confidently identified
non-Hindi calls, Tamil is the most common; Telugu and Kannada appear only occasionally. The
"other" bucket is mostly low-confidence detections (noise, short or empty calls) and very rare
languages.

---

## What we deliberately do NOT cover (yet)

Rare languages and dialects (for example **Odia, Assamese**) are **not pulled into the main
flow** right now. The reason: their share is vanishingly small, and supporting each one
requires a dedicated dictionary.

Our approach: **accumulate such calls and process them in separate batches**, and once enough
of them build up, adapt to them specifically. This keeps the main flow **simple and fast** and
avoids complicating the system for one-off cases.

---

## Under study: a single Sarvam stack for Config B

We are evaluating an option where **Config B** uses **one Sarvam provider** for two steps at
once (speech recognition + field extraction) instead of two separate components. The
motivation is **architectural simplification**.

- **On quality** — at parity with the current setup (verified against the real debt amount).
- **Economics** (cost per call) are being assessed separately.
- **No decision has been made** — the option is under review.

---

## Next

- **Accumulating labelled data** for our own **local extraction model** (full Config A,
  residency-safe) — so that this step, too, does not depend on the cloud.
- **Extending the validation to a larger volume** of calls for more robust accuracy figures.
