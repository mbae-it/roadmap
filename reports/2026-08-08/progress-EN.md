# Weekly Report — Call Intelligence

**Date:** 2026-08-08 (Saturday) · **Phase 1 (pilot): validation** · Aggregated report — no personal
data, no debtor phone numbers, and no figures drawn from your data.

Three things this week. We **processed two further batches of calls end to end**, which is what
turned batch processing from a demonstration into a routine. We **assembled the first complete
version of the on-site engine and examined it against the cloud reference** on a set of calls it had
never seen — the verdict on the metric that matters most to the business is positive, and we are now
working it toward full reference quality. And we **delivered the objection playbook** built from the
analyzed conversations.

The roadmap page has been updated to match: several stages are now marked done, and the on-site
engine stage is marked in progress with a clearer description of what it now means.

## The week at a glance

| Topic | Result |
|---|---|
| Batch processing | **Confirmed as routine.** Two further batches of calls went through the whole pipeline — audio to fields with supporting quotes — with the cost picture measured on both |
| On-site engine | **v1 assembled and examined.** Compared against the cloud reference on calls held out from all development; **positive verdict on the key business metric**. Now improving toward full reference quality, on-premise, against measurable quality gates |
| Objection analytics | **Playbook delivered** — the recurring objections and what happens around them, drawn from the analyzed calls |
| Cross-check with your systems | **Automated reconciliation bridge in place** — what the calls contain can now be compared against what your systems record, without manual work |
| Data protection | **Anonymization is in production** — it runs as part of the normal path, not as an optional step |
| Next | The audit & alerting layer, and continuing the on-site engine toward reference quality |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

The two things we have to prove before scaling are unchanged: the system must understand the call
correctly, and personal data must not leave the country. This week moved both forward, and it moved
the second one decisively — the on-site engine is the path on which nothing needs to leave at all.

---

## 2. Batch processing — now routine

Two further batches of calls were processed end to end: audio in, structured fields out, each field
carrying the quote it came from and the point in the recording where it was said.

What matters here is not that it worked once — that was proved earlier — but that it worked the same
way twice more, on different days of calls, with the cost measured each time rather than estimated.
That is the difference between a demonstration and a process you can plan around.

---

## 3. The on-site engine — v1 assembled and examined

This is the week's main result.

**What it is.** A complete processing engine that runs entirely on our own hardware. No part of a
call — audio or text — has to leave the premises for it to work.

**How we tested it.** Against the cloud reference, on a set of calls **held out from all
development**: never used to build, tune or teach any part of the engine. That is the only kind of
test that says anything about calls the system has not met yet.

**The verdict.** On the metric that matters most to the business — catching a firm promise to pay,
the thing that feeds the re-contact list — the on-site engine's verdict came out **positive**. That
is the result the whole on-premise direction rested on, and it is now measured rather than hoped for.

**What we are honest about.** "Positive on the key metric" is not the same as "matches the cloud
everywhere". There are areas where the on-site engine is still behind the reference, we know which
ones, and each has a **measurable quality gate** attached — a stated bar it has to clear before a
change is accepted. Work continues against those gates rather than against an impression.

**Why this direction and not a bigger machine.** Every improvement this week came from better
engineering around the engine, not from buying more computing power. That keeps the whole system on
hardware we control.

---

## 4. Objection & conversation analytics — playbook delivered

From the analyzed conversations we have produced a **playbook**: the objections that actually recur,
what tends to happen around each one, and how the conversation typically goes from there.

It is built from what was really said on the calls rather than from assumption, and every element in
it traces back to the conversations it was drawn from. It is meant to be used by your team directly
and corrected by them — the corrections are as valuable to us as the playbook is to you.

---

## 5. Cross-check with your systems

There is now an **automated bridge** that reconciles what the calls contain against what your own
systems record. Previously any such comparison meant someone exporting, aligning and reading two
sets of records by hand; now it runs by itself and produces a result that can be reviewed.

This matters beyond convenience: it is how the two pictures — what was said on the call and what was
recorded afterwards — can be held against each other routinely instead of occasionally.

---

## 6. Data protection

**Anonymization now runs in production**, as part of the normal processing path rather than as a
step someone has to remember. Personal data is removed before anything is sent for cloud processing,
and the on-site engine removes the question entirely for the work it handles.

We continue to state the limits of anonymization plainly rather than describing it as absolute; the
detail is in the earlier reports and has not changed.

---

## 7. What's next

1. **The on-site engine, toward reference quality** — working through the known gaps against the
   quality gates already defined, on our own hardware.
2. **Audit & alerting layer** — ongoing checks over processed calls, raising an alert when something
   needs a person to look at it.
3. **Your corrections on the playbook** — the fastest way to make the next version better.

---

*Questions and corrections — to the development team. This report is aggregated: it contains no
personal data, no debtor phone numbers, no quotes, and no figures taken from your data.*
