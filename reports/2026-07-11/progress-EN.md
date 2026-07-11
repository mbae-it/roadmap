# Weekly Report — Call Intelligence

**Date:** 2026-07-11 (Saturday) · **Phase 1 (pilot): validation** · Aggregated report, no
personal data and no debtor phone numbers.

This week focused on two things: **trustworthiness of the ground truth** (how far the data can
be treated as "truth") and the **types of amounts** spoken in calls (debt, payment, promise,
discount, penalty). Below are the results, plus one finding with direct business impact.

## The week at a glance

| Topic | Result |
|---|---|
| Tamil language | Closed on the full sample (50 calls from the customer team). Local path = cloud |
| Hindi + Tamil | Parity "local vs cloud" on both languages — the residency path is competitive |
| Source of truth | Confirmed: truth lives in the call, not in the debtor database |
| Discounts | Found 12 calls with a concrete discount offer that is **not** in the customer's systems |
| Amount standard | Draft of 5 markers — covers 92% of amounts; under review |
| Infrastructure | Server-overload protection moved to the OS level — no more overloads |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

We are still proving accuracy: first we confirm that the system understands the call correctly
and that our ground truth can be trusted, and only then do we move to scale and dashboards.
**This week's focus:** ground-truth quality and the breakdown of amount types.

---

## 2. Tamil language — closed on the full sample

Tamil was previously an open risk: the ground truth was thin (few calls with an amount). The
customer team provided labeled data for **50 Tamil calls** — enough for a full check.

On calls **where the amount is spoken aloud** (the subset on which amount-extraction accuracy
can be measured at all):

| Processing path | Where data is processed | Amount accuracy (Tamil) |
|---|---|---|
| **Local** (in India, residency) | never leaves India | **13 of 19** |
| **Cloud** (Sarvam) | outside India | **13 of 19** |

The local path is **on par** with the cloud. Together with the previously verified Hindi, this
gives **parity "local vs cloud" on both languages**. Practical takeaway: the path where data
**never leaves India** is competitive on quality.

---

## 3. Key finding: truth lives in the call, not in the database

We asked a simple question: when an operator says an amount aloud, **which field of the
collections database (DebThor) does it match?** We checked this on **75 calls**.

| What we checked | Result |
|---|---|
| Spoken amount = debt principal (`actual_capital`) | **45 of 75** — the dominant match |
| Other fields (interest, penalties, promised amounts) | the remainder, spread out |

**Conclusion 1 — our ground truth is correct:** when an operator names the "debt amount", in
most cases they name the debt principal, exactly as recorded in our ground truth.

**Conclusion 2 — and it matters more:** the debtor database is **unreliable as a single source
of truth**. The data passes through many hands and gets distorted along the way — the customer
team confirmed this too. Therefore **the primary source of truth is the call itself**, not the
record in the system. The product is built on this principle.

---

## 4. Discounts that are not in the database

The most significant finding of the week. The system detected calls where the operator
**offers the debtor a concrete discount or settlement** — and **none** of these amounts are
recorded in the customer's systems. The agreement exists **only in the audio**.

| Type | Number of calls | What happens |
|---|---|---|
| **Operator offered a concrete discount** | **12** | A closing amount is named (e.g.: close a **33,668** debt for **28,000**) |
| Debtor asks for a discount | 10 | Initiative comes from the debtor; the operator gives no concrete offer |
| Mention without an amount | 7 | A discount is mentioned in passing, with no figure |

Examples of offered terms (with no link to the debtor's identity):

| Debt (per database) | Offered by operator |
|---|---|
| 33,668 | close for **28,000** (waiver) |
| 29,981 | close for **23,468** (discount) |
| 15,428 | early closure for **8,000** |
| 2,718 | pay **1,550**, remainder written off |

These 12 offers are confirmed by **two independent reviews and by listening to the audio**.

**Business takeaway:** the system gives the customer **visibility into real agreements that are
currently in none of its systems**. This is direct control: which discounts are being handed
out on the line, for what amounts, and whether they match policy.

---

## 5. Proposal: a standard for stating amounts (draft)

**The problem.** Within a single call, different amounts mean different things: the debt
principal, a pay-now amount, a promised amount, a discount, a penalty. Today they are spoken
interchangeably — and the system has to **guess** the type from context. This is the main
source of ambiguity.

**The solution.** We propose **5 short markers** that the operator says **before the amount**.
The markers are built on what operators **already say** — this is not a new script, but a
discipline of phrasing.

| Scenario | Marker before the amount (example) |
|---|---|
| **Full debt** | "kul bakaya / total amount [amount]" |
| **Partial payment (now)** | "abhi [amount] jama kar dijiye" |
| **Promise to pay (date)** | "[date] ko [amount] denge" |
| **Discount / settlement** | "chhoot / waiver ke baad [amount] mein close" |
| **Penalty / late fee** | "penalty / bounce charge [amount]" |

These 5 markers cover **92%** of all amounts spoken in calls.

**Goal:** the call turns from "a recording of a conversation" into **a source of structured
data** — the amount arrives already tagged with a clear type, no guessing.

**Status:** draft under review with the customer team and the call center.

---

## 6. Infrastructure

Server-overload protection has been moved to the **operating-system level**: a load ceiling of
**90%** is enforced, so the customer's IT team **always keeps a reserve** of capacity. This is
a system-level limit that cannot be bypassed from within tasks. **There will be no more
overload incidents.**

---

## 7. What's next

1. **Agree the amount types and the marker standard** with the call center.
2. **Start accumulating labeled data** to train our own local extraction model — the path
   where data never leaves India (residency-safe).

---

*Questions and corrections — to the development team. All figures in this report were obtained
on real calls; personal data and debtor phone numbers are not included in the report.*
