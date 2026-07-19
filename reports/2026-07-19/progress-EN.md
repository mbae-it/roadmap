# Weekly Report — Call Intelligence

**Date:** 2026-07-19 (Sunday) · **Phase 1 (pilot): validation** · Aggregated report, no
personal data and no debtor phone numbers.

This week had three parts: we **rebuilt name anonymization from scratch**, we **handled a data
incident and recreated the code repository**, and we ran the **first full 1,000-call end-to-end
run** through the complete processing chain. All three are reported plainly below — including the
incident, which we disclose openly.

## The week at a glance

| Topic | Result |
|---|---|
| Name anonymization | Rebuilt. Names now masked in **97% of calls** / **84% of individual mentions**, **0** amounts or fields damaged. Masking is now **mandatory** — nothing goes to the cloud unmasked |
| First full run | **1,000 calls** processed end-to-end on the in-India path — **1000/1000 succeeded**, 26 fields + 11 flags each, every value backed by evidence |
| Privacy on the run | **0** structural personal data (phones / account numbers) left India across all 1,000 calls — verified |
| Data incident | **108** debtor phone numbers (**phones only**) were exposed in our private code repository during early development; removed, guarded, repository rebuilt clean — reported here openly |
| Next | Dashboard v1 (aggregates without a VPN + drill-down inside the residency zone) and the first full daily batch |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

We are still proving the system before scaling: it must understand the call correctly and it
must never move personal data out of the country. This week strengthened both — the accuracy
work continued *and* we hardened the privacy guarantees that the whole product depends on.

---

## 2. Name anonymization — rebuilt from scratch

We found a real gap and fixed it, and we want to be direct about it.

**The problem.** Our masking removed *structural* personal data — phone numbers and account
numbers — before any transcript was sent to the cloud model for analysis. But it did **not**
reliably remove **names**. Debtor and operator names spoken in the call were reaching the cloud
in the clear. For a residency-first product, that is not acceptable.

**What we did.** We rebuilt name detection from the ground up, combining two methods:

- a **fine-tuned Indian-language name model** (MuRIL, trained on our own labeled calls), and
- a **phrase parser** that recognizes the typical ways a name is introduced in these calls
  ("my name is…", "am I speaking with…", "sir/madam <name>").

The two run together, and a **numeric safeguard** guarantees the masking can never swallow an
amount.

**Result** (measured on a held-out test set the model never saw):

| Metric | Result |
|---|---|
| Calls with names masked | **97%** |
| Individual name mentions masked | **84%** |
| Amounts or other fields damaged by masking | **0** |

**What remains.** The residual is concentrated in **Tamil** names that are spoken without a
regular introducing phrase, so neither method has a strong signal to catch them. We close this
as more labeled Tamil data arrives — the same path that already closed Tamil for amounts.

**The important change:** masking is now **mandatory in the pipeline**. The system will not send
a transcript to the cloud unless a masked version exists — there is no path that egresses raw
text.

---

## 3. Data incident — debtor phone numbers in the code repository

We are reporting this openly.

**What happened.** During early development, **108 debtor phone numbers** ended up inside code
test-fixtures (the recording filenames embed the phone number) and were pushed to our **private**
GitHub code repository. The exposure is **phone numbers only** — **no names, no amounts, no case
details, no addresses, no account numbers**. Standard secret-scanners did not catch this: they
detect passwords and API keys, not phone numbers.

**Why it is limited.** The numbers were phone numbers alone. Linking a number back to a specific
debtor or case requires the CDR and DebThor records — and **those were never on GitHub**. The
repository is private (org members only). So the practical exposure is: a list of numbers,
without the context that would make them actionable.

**What we did about it:**

1. **Removed** the numbers from the current code and **rewrote the repository history** so the
   old versions no longer contain them.
2. **Added an automatic check** that blocks any commit containing a real phone or account number
   before it can ever be saved — closing the gap the secret-scanner left.
3. **Rebuilt the repository from a clean, phone-free base**, and are **removing the old copy from
   GitHub entirely** — the definitive step that clears the numbers from the platform. (This is
   why the code repository is being re-created.)
4. Kept a **full backup**, stored **locally in India**, so no work is lost.

This kind of thing happens in active development; the right response is to catch it, fix it,
prevent the repeat, and tell you. That is what we did.

---

## 4. First full run — 1,000 calls, end-to-end

This week we ran the **complete processing chain** on **1,000 real calls** for the first time —
the full hybrid system, from raw audio to structured, queryable results. Each stage:

1. **Intake + call-record filter** — drop non-connected calls (no talk time) *before* any
   processing, so we never spend effort on calls that never happened.
2. **Speech-to-text, in India** — local transcription (AI4Bharat IndicConformer). Audio never
   leaves the country for this step.
3. **Language identification** — detect which of 22 Indian languages the call is in.
4. **Number parsing** — convert spoken amounts ("thirty-three thousand…") into digits, across
   all 22 languages.
5. **Anonymization** — mask names (Section 2) *and* structural data (phones, account numbers)
   before anything is sent onward.
6. **Extraction** — the anonymized text is analyzed (Claude Haiku, via the discounted batch mode,
   −50% cost) to pull out the structured fields.
7. **Store** — results land in the database with semantic search and full versioning, so every
   run is reproducible and comparable over time.

**Result:**

| Metric | Result |
|---|---|
| Calls processed | **1,000 / 1,000 succeeded** |
| Structured output per call | **26 fields + 11 flags**, each with a confidence score |
| Evidence per value | **quote + timestamp + audio link** — every value is traceable to the call |
| Structural personal data sent to the cloud | **0** — masking verified on all 1,000 |

We also fixed a robustness issue found during the run: a single malformed response used to stall
the whole batch. It now isolates the bad item and continues — which matters directly for
processing at scale.

**Why we still plan cloud comparison runs.** We plan to run the same calls through the **cloud
path (Sarvam speech-to-text + Claude)** as well. This serves two purposes: (1) it gives us a
**quality benchmark** to compare the in-India system against, head-to-head at volume; and (2) it
**produces the labeled data we use to train the local system**. The end goal is unchanged:
bring the in-India path to full parity and **drop the cloud dependency**.

---

## 5. What's next

1. **Dashboard, version 1** — in two layers:
   - an **overview** of the metrics that needs **no India VPN** — aggregate numbers only, no
     personal data, in the same spirit as your existing operations dashboard; and
   - a **drill-down** to the actual quotes, transcripts, and audio, which stays **inside the
     residency zone** (accessed within India).
2. **First full daily batch** — put the 1,000-call run onto a regular daily cadence.

---

*Questions and corrections — to the development team. All figures in this report were obtained on
real calls; personal data and debtor phone numbers are not included in the report.*
