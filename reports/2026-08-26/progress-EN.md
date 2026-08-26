# Weekly Report — Call Intelligence

**Date:** 2026-08-26 (Wednesday) · **Phase 1 (pilot): validation** · Aggregated report — no personal
data, no debtor phone numbers, and no figures drawn from your data.

Four things this week. The **first training run of the on-site engine is live**, on a graphics card
rented inside India — the toolkit around it was finished, a staged trial run passed on the card
before the real one started, and the bars the result must clear were written down beforehand. The
**report page was restructured into the four sections your team specified**, and the cross-check
between what the calls say and what your own systems record **went live** on it, as a pilot. A
**full revision of the databases we can reach closed the telephony gap** — the missing call-outcome
data turned out to be reachable already, so the request we had drafted for your IT department was
not needed. And your team **approved three definition questions** that had been open for weeks.

The roadmap page is updated to match. Stage 7 now describes the training run rather than the
certification phase that preceded it; stage 13 — **Live processing (GPU)** — moves from planned to
**in progress**, which is the week's main shift on the map; stage 12 takes your team's own name for
it, **Performance Feedback Module & alerting**; stage 10 records the two approvals that are already
live; stage 11 records the cross-check pilot; and two older stages had their text corrected where it
had drifted from what actually ships.

## The week at a glance

| Topic | Result |
|---|---|
| On-site engine | **First training run is live**, on a rented in-India graphics card. The toolkit around it is complete; a staged trial run passed on the card first; the acceptance bars were registered before the run began |
| A defect caught before it cost a rented day | The training examples were written in a different character-for-character shape than the engine is constrained to produce. Every example would have taught the engine a form it is not allowed to emit. Found before training, fixed, and proved against the engine's own past answers |
| Report page | **Restructured into your four sections**, so the operational numbers come first and the method notes sit behind them. The signal legend your team wrote is now built into the page and checked against the code at build time |
| Cross-check with your systems | **Live, as a pilot.** Both directions, every divergence carried with its trail on both sides, and the reconciliation's own error rate measured by hand before anything was shown |
| Data quality | The telephony gap **closed without an IT request** — the data was reachable through an existing link. Call-outcome fields added alongside our own, never over them. A column silently blanked by an earlier re-load was found and restored |
| Approvals | The **two-tier promise rule**, the **vocabulary of offers** and the **conduct classes** are approved by your team and recorded. The new extraction prompt is built and has been read on a small sample |
| Next | The exam of the trained engine against the frozen baseline; the rental decision, taken from measurements rather than in advance |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

The two things that must be proved before scaling have not changed: the system must read a call
correctly, and personal data must not leave the country. This week moved the first one onto new
ground — until now the on-site engine was assembled from parts that were not trained; from this week
it is being trained, and the exam that will judge it was fixed in advance.

---

## 2. The on-site engine — the first training run is live

**The toolkit is finished.** Preparing a training run is most of the work: the examples have to be
assembled, measured, checked for any overlap with the sets reserved for examination, and proved to
carry no personal data before a single byte leaves this building. All of that is done, and each step
was verified against the artifact it produced rather than against the log that described it.

**A defect that would have wasted the rental, caught before it.** The engine is constrained to write
its answers in one exact form. The training examples had been written in a different one — same
meaning, different characters. Nothing would have failed; the run would simply have spent itself
teaching the engine a shape it is not permitted to use, and the result would have read as *training
did not help*. It was found by comparing the two forms character by character, corrected, and the
correction proved by reproducing the engine's own past answers exactly. This is the kind of defect
that is cheap on the day it is found and very expensive a week later.

**A staged trial run on the rented card, before the real one.** The card was taken for a short trial
first, with each assumption checked on the hardware itself rather than read from a table. Two of our
own predictions were corrected by that trial, and one real fault was found and fixed: a memory-saving
setting was reported as active while being silently inactive, which the trial exposed immediately and
which would have stopped the real run hours in. The engine now refuses to start unless that setting
is genuinely live.

**The bars were set before the run, not after it.** What the trained engine must achieve — on the
small guard set and on the large examination set — was written down and committed before the training
command was typed, together with the conditions under which a run is **discarded rather than
interpreted**. This is the same method as the certification phase, and it is the reason its verdicts
could be trusted when they were negative.

**Where it stands.** The run is under way and will produce two candidate versions rather than one, so
that a shorter run remains usable if the card is wanted back. The examination itself stays on our own
hardware, where the frozen baseline was measured — comparing a new result against a baseline measured
on different hardware would compare the hardware as much as the engine.

---

## 3. Deliverables

**The report page now has your four sections.** Operational numbers first, diagnostics next, method
behind them, and everything not yet assigned in its own part. Nothing was re-measured to do it: the
page was rebuilt from the figures already in your hands, and that equality was proved rather than
promised — precisely so that a change of layout could not be mistaken for a change of results.

**Your legend is in the page.** The definitions your team wrote for the call markers are now part of
the page itself, in both languages, bound to the cards they describe so that a card without a
definition, or a definition without a card, stops the build. Where the document and the working code
define something differently, the page **says so, in place**, instead of quietly presenting one of
the two. Those places are yours to rule on; the document is already in your hands and only you can
decide which side should move.

**The cross-check with your systems is live, as a pilot.** Where our reading of a call and your own
records disagree, the page now shows the disagreement with its full trail on both sides. Two things
were done before showing anything: the reconciliation was checked by hand against calls read by a
person, to measure how often the *mechanism itself* is wrong; and the time window it compares within
was declared on the page, because your records hold each case's whole history and a promise entered
years earlier would otherwise be counted as agreement. It is badged as a pilot on part of one batch,
and the page never claims more than that.

**The strongest single result of that work** is that the cross-check independently rediscovered a
defect your QA team had already confirmed by listening — arriving at it from your records, knowing
nothing of the listening. Three independent sources agreeing on the same call is the kind of evidence
this project is built to produce.

---

## 4. Data quality

**The telephony gap closed, and it did not need a request to your IT department.** For weeks the
missing piece was call outcome and duration per call — we had drafted a formal data request for it.
A full revision of what our existing access can actually reach found the data already reachable
through a link that was there all along, covering **every** call in every batch we hold, back several
years. The request was not sent. The conclusion we had drawn from its absence still stands and is
unchanged — our batches were selected from answered recordings, so that field cannot discriminate on
our own calls — but it is now measurable rather than missing.

**The new fields were added beside ours, never over them.** Adding data from another system to
records that already carry our own is exactly where a careless load overwrites something correct.
The load was gated on a control batch agreeing with what we already had, and proved not to have
touched a single pre-existing value before it was accepted.

**A silently blanked column, found and restored.** An earlier re-load of one batch had written empty
values over a column that had been correct — nothing failed, nothing was raised, and the loss was
invisible. It was restored against two independent witnesses that agree with each other exactly; a
restore from a single source would have been a guess dressed as a repair. The hole in the writing
step is closed, so the same re-load can no longer blank anything.

**A negative result worth stating.** We checked whether recordings exist for calls that never
connected — the one group we have never held and cannot yet measure ourselves against. They do not
exist on the source side. That closes a plan rather than opening a request, which is the more useful
of the two outcomes.

---

## 5. Definitions your team approved

Three questions that had been open, several of them for weeks, were ruled on:

- **The two-tier promise rule.** Approved as it stood, in your team's own terms, and it is already
  live on the report page. The distinction it draws — between a promise with a payment method agreed
  and one without — decides how large the re-contact queue is, which is why it was worth waiting for
  rather than deciding ourselves.
- **The vocabulary of offers.** Approved. We mapped it against what our extraction records today and
  reported honestly that the mapping covers only part of it, and that two of the approved values have
  no source in our current field at all — that is work for the new prompt, not something the mapping
  can quietly cover.
- **The conduct classes.** The first four are approved as they stand; the last one is with your
  methodologist. The new prompt names it explicitly as *not* to be recorded, so that pressure of a
  kind you have not yet confirmed cannot quietly inflate a class you have.

**Your ruling on scope was taken as given.** The new fields start from the next processing rather
than being back-filled over what is already delivered. That cancelled a planned re-processing pass —
and the cost we had quoted for it is withdrawn, since a price for something that will not happen is
not a saving.

**The new prompt is built and has been read.** It was piloted on a small, deliberately mixed sample,
alongside a control on the very same calls, so that any difference could be attributed to the prompt
rather than to the calls. It behaves; three specific weaknesses were found and are being corrected
before it is used on a paid batch. One of them is the kind that matters: on calls where the two
speakers cannot be told apart, the prompt should say *unknown* about who did something, and it did
not — which on a signal about your own staff is the one error worth preventing in advance.

---

## 6. What's next

1. **The exam of the trained engine** — against the frozen baseline, on the guard set first and then
   the large examination set, with the bars already fixed. Either it clears them or it does not; both
   answers are useful and both will be reported.
2. **The rental decision** — whether to keep a rented card by the month is a question about measured
   cost and measured benefit, and it is deliberately not being answered before the measurements
   exist. It remains separate from the hardware for live processing in stage 13, which is yours.
3. **The Performance Feedback Module** — the frame is your team's document, reviewed in both
   languages and agreed in its core entities. Nothing is built or estimated until it is finalised on
   your side.

---

*Questions and corrections — to the development team. This report is aggregated: it contains no
personal data, no debtor phone numbers, no quotes and no figures drawn from your data.*
