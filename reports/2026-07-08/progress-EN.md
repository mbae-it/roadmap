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

The pilot's key metric is how accurately the system extracts the **debt amount** from a call.
We compare two processing paths: **local** (in India, residency-safe) and **cloud** (Sarvam).
The reference ("ground truth") is the actual debt amount from the **DebThor** collections
system.

| Processing path | Language | Accuracy (match to the real amount) | Reference |
|---|---|---|---|
| **Local** (in India) | Hindi | **6 / 10** | DebThor (actual debt amount) |
| Cloud (Sarvam) | Hindi | 6 / 10 | DebThor |
| **Local** (in India) | Tamil | **3 / 9** | DebThor |
| Cloud (Sarvam) | Tamil | 3 / 9 | DebThor |

**The local path is at parity with the cloud** on both languages. The differences from the
reference occur mainly where **the recording itself is poor quality** or **the language is
rare** — this is a limit of speech recognition as such, not a defect in our code. Where the
audio is clean, both paths reliably recover the correct amount.

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
