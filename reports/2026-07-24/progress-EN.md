# Weekly Report — Call Intelligence

**Date:** 2026-07-24 (Friday) · **Phase 1 (pilot): validation** · Aggregated report, no personal
data and no debtor phone numbers.

This week had four parts: we **delivered the first dashboard**, we **investigated the "no-answer"
calls and found a labeling mistake of our own** (disclosed openly below), we **completed the
configuration comparison** that decides which processing path we ship, and we **made cloud
masking mandatory by construction**. All four are reported plainly below.

## The week at a glance

| Topic | Result |
|---|---|
| Dashboard v1 | **Delivered.** Two switchable layouts (by signal / by theme), English/Russian, a single offline file; **every number drills down to its evidence** (quote + timestamp + audio). Feedback round with your team is open |
| "No-answer" calls | Investigation found **75 of 307** "no-answer" calls were **actually debtor pickups** (**51** confirmed by listening); **62** of them broke off before the debt was discussed → **direct re-contact candidates**. The wrong label came from **our own analysis**, not your dialer — root cause fixed |
| Configuration comparison | **Completed** — the same **1,000 calls** through **4 configurations** (2 speech-to-text × 2 language models). The **production configuration is confirmed** (in-India speech-to-text + the smaller model) — it wins on catching firm promises and on cost |
| Masking gate | All cloud calls now pass **one mandatory masking gate** — a "cannot", not an "agreed not to". **Two latent leak paths were found and closed** while building it |
| Next | Extraction prompt v2, a local number-formatting layer, and selecting/training the in-India-only model |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

We are still proving the system before scaling: it must understand the call correctly and it must
never move personal data out of the country. This week advanced both — the first dashboard for you
to see the results, and a structural hardening of the privacy guarantee.

---

## 2. Dashboard v1 — delivered

You now have the first working dashboard over the processed calls.

- **Two layouts you can switch between:** one organized **by signal** (the extracted fields and
  flags) and one **by theme** (grouped topics), so the same data reads two ways depending on what
  you are looking for.
- **English / Russian** toggle throughout.
- **A single offline file** — no server and no India VPN needed for the aggregate view.
- **Every number drills down to its evidence.** Behind each figure is the exact quote, the
  timestamp, and a link to the audio — so a number is never just a number; it is traceable to the
  call it came from.

A **feedback round with your team is open** — we want your corrections on layout and on which
numbers matter most before we build the next version.

---

## 3. The "no-answer" calls — a label we got wrong, and fixed

We are reporting this openly, in the same spirit as the incident report.

**What we found.** Of the calls filed as **"no-answer"** (307 of them), **75 were actually debtor
pickups** — the debtor *did* answer, but the call was recorded as no-answer. We **hand-listened to
confirm 51** of them as real two-party conversations. And **62 of the 75 broke off before the debt
was even discussed** — these are **direct re-contact candidates**: debtors who were reached, on the
line, and not yet worked.

**Whose mistake it was.** The wrong "no-answer" label did **not** come from your dialer. It came
from **our own analysis layer** reading the transcript and mis-classifying the outcome. We own that
and disclose it, the same way we disclosed the earlier data incident.

**Root cause, fixed.** A call's outcome no longer depends on the model's reading of the words.
It now comes from the **telephony record plus the audio itself** (who spoke, on which channel) —
facts, not interpretation. This closes the specific mistake and the wider class it belongs to.

**Why it matters to you.** These are lost contacts hiding inside a "no-answer" bucket that would
otherwise be written off. Surfacing them turns a discarded segment into a re-dial list.

---

## 4. Configuration comparison — completed

We ran the **same 1,000 calls through 4 configurations** — **2 speech-to-text engines × 2 language
models** — a full head-to-head. This is the comparison that decides which path we ship.

**Result — the production configuration is confirmed.** The **in-India** speech-to-text paired with
the **smaller, cheaper language model** is the right choice: it **wins on the product's core job —
catching a firm promise to pay — and on cost**. Using a larger, more expensive model does not
improve that; it is more cautious and misses more promises.

**The one cloud advantage, and our plan for it.** The cloud speech-to-text is better at **number
formatting** (it writes spoken amounts straight as digits). That is its only real edge, and we will
**replicate it locally** with a dedicated number-formatting layer — so we keep the benefit without
sending audio out of the country.

**A by-product: training data for the fully-local model.** The comparison produced a **labeled
corpus** for the future in-India-only model: **5,375 items where all four configurations agree**
(high-confidence labels) plus **647 prioritized disagreements** (the informative cases worth a human
label). This is the foundation for training a model that needs no cloud at all.

**Cost.** The entire comparison — all four configurations over 1,000 calls — cost about **$55**,
thanks to discounted batch processing.

---

## 5. Mandatory masking gate for all cloud calls

We made the privacy guarantee **structural**.

Previously, masking personal data before a cloud call was the correct behavior but depended on the
code remembering to do it. Now **every cloud call passes through one mandatory gate** that refuses
to send text unless it has been masked — a **"cannot", not an "agreed not to"**. There is no longer
a path that sends raw text by mistake.

While building the gate we **found and closed two latent leak paths** — places where text could have
reached the cloud unmasked under specific conditions. Both are now closed. Finding them is exactly
what the gate is for: it inspects every outgoing call, so gaps surface instead of staying hidden.

---

## 6. What's next

1. **Extraction prompt v2** — refine the field-extraction instructions using what the comparison
   taught us (including moving call-outcome off the model, per Section 3).
2. **Local number-formatting layer** — replicate the cloud's number advantage in-India, so amounts
   come out as clean digits without leaving the country.
3. **Local model — selection and training** — begin choosing and training the in-India-only model on
   the labeled corpus from Section 4, toward dropping the cloud dependency entirely.
4. **Multi-day batches** — extend processing from a single day to multiple days at once.

---

*Questions and corrections — to the development team. All figures in this report were obtained on
real calls; personal data, debtor phone numbers, and quotes are not included in the report.*
