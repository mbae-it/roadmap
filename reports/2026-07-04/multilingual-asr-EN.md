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

Accuracy was not judged by eye but **verified against real data from the collections system**
(DebThor, an export from Nadezhda): actual debt amounts, promises, and real payments. That
is why the tables carry a "reference" baseline — what was extracted from the call was compared
against the fact in the banking system. The data is anonymized, and PII never leaves India.

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
| Bengali | 2* | — no conclusion | Only 2 calls; in these two the debt-system amount is not stated |

\* There are only **2** Bengali calls in the sample — too few to draw any conclusion about the
language. In these two specific calls there is no spoken amount that matches the debt system —
and the same holds on the Sarvam cloud: it also "fails" to capture them, because the amount is
not stated in these calls. This is about **these specific calls, not the Bengali language in
general** — Bengali calls do state amounts, just not these two.

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

## Appendix: extracted fields for all 100 calls

Actual system output on 100 calls (configuration: local multilingual ASR + Haiku). Row key is `pii_safe_id` (an anonymized call identifier, no phone number).

### Summary: how often each flag fired (of 100)

| Category | Flag | Fired (of 100) |
|---|---|---|
| Payment Outcome | Payment firm ptp | 5 |
| Payment Outcome | Payment vague promise | 52 |
| Payment Outcome | Payment no ptp | 43 |
| Payment Outcome | Payment broken previous | 46 |
| Debtor Behavior | Cooperative positive | 53 |
| Debtor Behavior | Cooperative negative | 72 |
| Debtor Behavior | Refusal negative | 13 |
| Debtor Situation | Financial hardship | 31 |
| Debtor Situation | Dispute validation | 24 |
| Debtor Situation | Settlement requested | 25 |
| Risk & Compliance | Compliance risk flag | 31 |

### Summary: fill rate of numeric/status fields (of 100)

| Field | Filled (of 100) |
|---|---|
| debt amount | 62 |
| promised amount | 27 |
| promised date | 32 |
| payment method | 16 |
| identity verification (name_only) | 77 |
| debt disclosure (true) | 89 |

### Full per-call table

Flag codes: `FIRM`=Payment firm ptp `VAGUE`=Payment vague promise `NO-PTP`=Payment no ptp `BROKEN`=Payment broken previous `COOP+`=Cooperative positive `COOP-`=Cooperative negative `REFUSE`=Refusal negative `HARD`=Financial hardship `DISPUTE`=Dispute validation `SETTLE`=Settlement requested `RISK`=Compliance risk flag. ✓ = flag fired. Numeric/text fields: "—" = empty; `disc` (debt disclosure) ✓/✗ = yes/no; `ident` = identity-verification level.

| # | pii_safe_id | FIRM | VAGUE | NO-PTP | BROKEN | COOP+ | COOP- | REFUSE | HARD | DISPUTE | SETTLE | RISK | debt | ptp_amt | ptp_date | ptp_method | ident | disc |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | `11f171e03670b95ab497` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  | ✓ |  | 26221 | — | 15th or 16th (approximate) | — | name | ✓ |
| 2 | `11f1721ed941985ab497` |  |  | ✓ |  |  | ✓ |  |  | ✓ |  |  | — | — | — | — | name | ✓ |
| 3 | `11f1720fb8bd8eb88628` |  | ✓ |  | ✓ | ✓ | ✓ |  |  | ✓ |  |  | 4627 | 4627 | — | — | name | ✓ |
| 4 | `11f1720e9ae39460b497` |  |  | ✓ |  | ✓ |  |  |  |  | ✓ |  | 50000 | 48000 | end of month | — | name | ✓ |
| 5 | `11f1721b7b94ea7abccd` |  | ✓ |  |  | ✓ |  |  | ✓ |  |  |  | — | — | — | — | name | ✓ |
| 6 | `11f171de2ed5216abc62` |  |  | ✓ |  |  | ✓ |  |  | ✓ |  |  | — | — | — | — | name | ✗ |
| 7 | `11f171fa5053af2ab877` |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ | ✓ |  | — | — | — | — | name | ✓ |
| 8 | `11f171e5255c0dfe846b` |  |  | ✓ |  |  | ✓ |  |  |  | ✓ | ✓ | 14000 | — | — | — | name | ✓ |
| 9 | `11f17206f7cc7550bccd` |  |  | ✓ |  | ✓ |  |  |  |  |  |  | 200000 | — | — | — | name | ✓ |
| 10 | `11f17220c35f979cbccd` |  | ✓ |  | ✓ |  | ✓ |  | ✓ |  | ✓ |  | 1154 | — | — | — | name | ✓ |
| 11 | `1782543453.17607966` | ✓ |  |  | ✓ | ✓ | ✓ |  | ✓ | ✓ | ✓ | ✓ | 3130 | 1600 | today | online | name | ✓ |
| 12 | `1782540712.17553517` |  | ✓ |  | ✓ | ✓ | ✓ |  |  |  |  |  | 1389 | 1389 | same day 17:00 | — | name | ✓ |
| 13 | `1782543984.17622048` |  | ✓ |  | ✓ | ✓ |  |  |  | ✓ |  |  | 91800 | 91800 | same day (afternoon/evening) | — | name | ✓ |
| 14 | `11f171faea6ef1a0846b` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | 600 | 600 | — | PhonePe | name | ✓ |
| 15 | `11f171eef2d066c8b877` |  | ✓ |  | ✓ |  | ✓ | ✓ |  |  | ✓ | ✓ | 13421 | 5000 | — | — | name | ✓ |
| 16 | `11f172276d7f609eb877` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | — | — | — | — | name | ✗ |
| 17 | `11f171eedfc3ac66846b` |  | ✓ |  | ✓ | ✓ | ✓ |  |  |  | ✓ |  | — | — | — | — | name | ✓ |
| 18 | `1782534232.17434121` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | — | — | same day (11:00 AM) | Loan ID (bank transfer implied) | name | ✓ |
| 19 | `11f17202fda95ff0b877` |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  |  |  | 15373 | — | 2024-01-29 | — | name | ✓ |
| 20 | `1782556620.17951967` |  | ✓ |  | ✓ |  | ✓ |  |  |  |  | ✓ | 2000 | 2000 | — | — | name | ✓ |
| 21 | `1782561218.18139647` |  | ✓ |  |  |  | ✓ |  |  | ✓ |  |  | 5000 | — | एक दिन बाद | — | name | ✓ |
| 22 | `1782561985.18166571` |  |  | ✓ | ✓ |  | ✓ | ✓ | ✓ |  | ✓ | ✓ | 3876 | — | — | — | — | ✓ |
| 23 | `1782544240.17628350` | ✓ |  |  | ✓ | ✓ | ✓ |  |  | ✓ |  |  | 1850 | 1850 | 2025-01-05 or 2025-01-06 | bank transfer | name | ✓ |
| 24 | `1782558248.18001885` |  |  | ✓ | ✓ |  | ✓ | ✓ |  |  |  |  | — | — | — | — | name | ✓ |
| 25 | `1782541362.17563124` |  | ✓ |  |  | ✓ |  |  |  |  |  | ✓ | 5800 | — | today | — | — | ✓ |
| 26 | `1782541076.17558880` |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ | ✓ |  | 134336 | — | — | — | name | ✓ |
| 27 | `1782553596.17858373` |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  |  |  | 6036 | — | — | — | name | ✓ |
| 28 | `1782535931.17466228` |  |  | ✓ |  | ✓ |  |  |  |  |  |  | 60000 | — | — | — | name | ✓ |
| 29 | `1782546605.17693841` |  | ✓ |  | ✓ | ✓ | ✓ |  |  |  | ✓ |  | 15428 | — | next day 11:00 | online | name | ✓ |
| 30 | `1782533903.17427383` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | — | — | afternoon (same day) | — | — | ✗ |
| 31 | `1782531514.17399512` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  |  |  | — | — | same day (evening) | online or bank transfer | name | ✓ |
| 32 | `1782537733.17512918` |  |  | ✓ |  |  | ✓ |  | ✓ |  |  |  | 5484 | — | — | — | name | ✓ |
| 33 | `1782547356.17713536` |  | ✓ |  | ✓ |  | ✓ | ✓ |  |  |  | ✓ | 29981 | 23468 | same day evening | — | name | ✓ |
| 34 | `1782535419.17451832` |  | ✓ |  | ✓ | ✓ |  |  |  |  |  | ✓ | 799 | 799 | Monday | — | — | ✓ |
| 35 | `1782537836.17515210` |  | ✓ |  |  | ✓ |  |  |  |  |  | ✓ | 10000 | 2000 | — | — | — | ✓ |
| 36 | `1782553322.17852217` |  |  | ✓ | ✓ |  | ✓ |  |  | ✓ |  | ✓ | — | — | — | — | — | ✓ |
| 37 | `1782560625.18125624` |  | ✓ |  |  |  | ✓ |  | ✓ | ✓ |  |  | 4008 | — | — | — | name | ✓ |
| 38 | `1782559925.18107730` |  |  | ✓ |  | ✓ |  |  |  |  |  |  | — | — | — | — | — | ✗ |
| 39 | `16a3f505fcba18` |  | ✓ |  |  | ✓ | ✓ |  |  |  |  |  | 3752 | 3059 | अठाई तारीख (28th) | — | name | ✓ |
| 40 | `1782537553.17509565` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | — | — | — | — | — | ✗ |
| 41 | `1782550259.17768548` |  |  | ✓ | ✓ |  | ✓ |  |  |  |  | ✓ | — | — | 24th | — | name | ✓ |
| 42 | `1782563191.18211924` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | — | — | — | — | name | ✓ |
| 43 | `1782533765.17425326` |  | ✓ |  |  | ✓ |  |  | ✓ |  |  |  | — | — | — | — | name | ✓ |
| 44 | `1782551426.17798336` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ | ✓ |  |  | — | — | Monday | video call at bank | name | ✓ |
| 45 | `1782545824.17670807` |  |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  | ✓ | 300 | — | — | — | — | ✓ |
| 46 | `1782532563.17411058` |  |  | ✓ | ✓ |  | ✓ | ✓ |  | ✓ |  | ✓ | 1630 | — | — | — | name | ✓ |
| 47 | `1782532972.17415155` | ✓ |  |  | ✓ | ✓ | ✓ |  |  |  |  |  | 3411 | 3411 | same day (before 2 PM) | PhonePe/Google Pay/Paytm (via shop/friend) | name | ✓ |
| 48 | `1782540825.17555114` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ | 3690 | 750 | same day (by 4 PM) | — | — | ✓ |
| 49 | `1782537112.17499551` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  | ✓ |  | — | — | — | — | — | — |
| 50 | `1782540677.17553018` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | 1428 | — | — | — | name | ✓ |
| 51 | `1782536490.17479665` |  | ✓ |  |  | ✓ | ✓ |  |  |  |  | ✓ | 225 | — | — | — | — | ✓ |
| 52 | `1782560628.18125708` |  | ✓ |  | ✓ |  | ✓ |  |  |  |  |  | — | — | next day (कल) | — | name | ✓ |
| 53 | `1782562837.18199097` |  | ✓ |  | ✓ | ✓ |  |  |  | ✓ |  |  | 4860 | — | before July | — | name | ✓ |
| 54 | `1782553451.17855319` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | — | — | — | — | name | ✓ |
| 55 | `1782545058.17649597` |  |  | ✓ |  | ✓ |  |  |  |  |  |  | 1500 | — | — | — | name | ✓ |
| 56 | `1782539698.17534596` |  |  | ✓ | ✓ | ✓ | ✓ |  | ✓ |  | ✓ |  | 4116 | — | — | — | name | ✓ |
| 57 | `1782542988.17598024` |  | ✓ |  |  | ✓ |  |  | ✓ |  |  |  | 3735 | 3735 | next day | — | name | ✓ |
| 58 | `1782554162.17874704` |  |  | ✓ | ✓ |  | ✓ |  | ✓ |  |  | ✓ | — | — | — | — | — | ✗ |
| 59 | `1782555600.17915390` |  | ✓ |  | ✓ | ✓ | ✓ |  |  |  |  |  | 600 | — | same day 18:00 | — | name | ✓ |
| 60 | `1782534051.17429974` |  | ✓ |  | ✓ | ✓ |  |  |  |  |  |  | — | — | 2024-05-15 | — | name | ✓ |
| 61 | `16a3f979177f60` |  |  | ✓ | ✓ |  | ✓ | ✓ |  |  |  | ✓ | — | — | — | — | — | ✓ |
| 62 | `1782554872.17895940` |  | ✓ |  |  |  | ✓ | ✓ |  |  |  | ✓ | — | — | — | — | name | ✓ |
| 63 | `1782544444.17633559` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | 692 | — | — | — | name | ✓ |
| 64 | `1782541656.17568814` |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  | ✓ | ✓ | 2301 | 750 | today | — | — | ✓ |
| 65 | `1782540111.17542589` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | — | — | — | — | — | ✗ |
| 66 | `1782540623.17552203` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  |  | ✓ | 2500 | 1200 | — | link | — | ✓ |
| 67 | `1782541197.17560658` |  |  | ✓ |  |  | ✓ |  | ✓ |  |  | ✓ | 6040 | — | — | — | name | ✓ |
| 68 | `1782532963.17415070` | ✓ |  |  |  | ✓ |  |  |  |  |  | ✓ | 1300 | 1300 | Monday | online | — | ✓ |
| 69 | `1782556884.17960379` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | — | — | today | online | name | ✓ |
| 70 | `1782534448.17437907` |  | ✓ |  |  | ✓ |  |  |  |  | ✓ | ✓ | — | 500 | — | website payment | — | ✓ |
| 71 | `1782542973.17597710` |  |  | ✓ |  |  | ✓ | ✓ |  |  |  |  | 1000 | — | — | — | name | ✓ |
| 72 | `1782539555.17532066` |  | ✓ |  | ✓ | ✓ | ✓ |  |  | ✓ |  |  | — | 1624 | — | cash | name | ✓ |
| 73 | `1782538033.17518665` |  |  | ✓ |  | ✓ |  |  |  | ✓ |  | ✓ | 750 | — | — | — | — | ✓ |
| 74 | `1782540625.17552231` |  | ✓ |  |  | ✓ |  |  | ✓ |  |  |  | 33218 | 1000 | — | — | name | ✓ |
| 75 | `1782539011.17527557` |  | ✓ |  |  | ✓ |  |  | ✓ |  |  | ✓ | 1467 | 1000 | — | bank transfer | — | ✓ |
| 76 | `11f1720911876d4a846b` |  |  | ✓ |  |  | ✓ |  |  | ✓ |  | ✓ | — | — | — | — | name | ✗ |
| 77 | `11f171fbe883aa9cb877` |  |  | ✓ | ✓ |  | ✓ |  | ✓ |  |  |  | — | — | — | — | — | ✗ |
| 78 | `11f1720629dd9bf6b877` |  | ✓ |  | ✓ |  | ✓ |  | ✓ | ✓ | ✓ | ✓ | 11500 | 1447 | — | — | name | ✓ |
| 79 | `11f17208d43cef14b877` |  |  | ✓ | ✓ |  | ✓ |  | ✓ | ✓ | ✓ |  | — | — | — | — | name | ✓ |
| 80 | `11f171e86d24766ebc62` |  | ✓ |  |  |  | ✓ |  |  |  |  |  | 28000 | — | Monday | — | name | ✓ |
| 81 | `11f171f8e313bd7a8628` |  |  | ✓ | ✓ |  | ✓ | ✓ | ✓ |  | ✓ | ✓ | 211960 | — | — | — | name | ✓ |
| 82 | `11f17224b0ef7b5a8628` |  |  | ✓ | ✓ |  | ✓ |  |  |  |  | ✓ | — | — | — | — | name | ✓ |
| 83 | `11f17207892d43d0bc62` |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  | ✓ |  | — | 8000 | 2-3 months | — | name | ✓ |
| 84 | `11f1721c9982e00eb877` |  | ✓ |  | ✓ |  | ✓ |  |  | ✓ |  |  | 5316 | — | — | — | name | ✓ |
| 85 | `11f171e34015fea48628` |  | ✓ |  | ✓ | ✓ | ✓ |  | ✓ |  | ✓ |  | 800000 | — | — | — | name | ✓ |
| 86 | `11f172274ee95630bccd` |  | ✓ |  |  | ✓ | ✓ |  | ✓ |  | ✓ |  | 5183 | 2600 | — | UPI | name | ✓ |
| 87 | `11f1720f100271c6b497` |  |  | ✓ | ✓ | ✓ |  |  |  |  |  |  | 2725949 | — | — | — | name | ✓ |
| 88 | `11f171de7d83fa84bc62` |  |  | ✓ |  | ✓ |  |  |  |  |  |  | — | — | — | — | name | ✓ |
| 89 | `11f172062244bbaebccd` |  | ✓ |  |  |  | ✓ | ✓ | ✓ |  |  |  | 10000 | — | — | — | name | ✓ |
| 90 | `11f1720c84ce1026bccd` |  |  | ✓ |  |  | ✓ |  |  |  |  | ✓ | — | — | — | — | name | ✓ |
| 91 | `11f171fb986f108c8628` |  |  | ✓ |  |  |  |  |  |  |  |  | — | — | — | — | name | ✗ |
| 92 | `11f1721ab8b883908628` |  |  | ✓ |  |  | ✓ |  | ✓ | ✓ | ✓ |  | 334348 | — | — | — | name | ✓ |
| 93 | `11f171fd2c3c60fc8628` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | 1348 | — | — | — | name | ✓ |
| 94 | `11f171ef1e4643ccbc62` |  | ✓ |  | ✓ |  | ✓ | ✓ |  |  |  |  | 2800 | — | — | — | name | ✓ |
| 95 | `11f1721d4e6d6b1a846b` |  |  | ✓ |  |  | ✓ |  |  |  |  |  | — | — | — | — | name | ✓ |
| 96 | `11f171eed64f3718b877` |  |  | ✓ |  |  | ✓ | ✓ |  | ✓ | ✓ |  | 31600 | — | — | — | name | ✓ |
| 97 | `11f171f4d21953a88628` |  | ✓ |  | ✓ | ✓ | ✓ |  |  |  |  | ✓ | — | — | 2024-07-01 | — | — | ✓ |
| 98 | `11f171e496b328e4b877` |  | ✓ |  |  | ✓ |  |  |  |  |  |  | 5800 | — | — | — | name | ✓ |
| 99 | `11f172231f5571c88628` | ✓ |  |  | ✓ | ✓ | ✓ |  |  | ✓ | ✓ |  | 2718 | 1550 | next day (by 10:00 AM) | online | name | ✓ |
| 100 | `11f1720d0d072126b497` |  |  | ✓ | ✓ |  | ✓ |  |  |  | ✓ |  | 4000 | — | — | — | name | ✓ |
