# Weekly Report — Call Intelligence

**Date:** 2026-08-17 (Sunday) · **Phase 1 (pilot): validation** · Aggregated report — no personal
data, no debtor phone numbers, and no figures drawn from your data.

Three things this week. We **put everything on one page**: the dashboard now carries the
conversations themselves, so a click on any quoted line opens the call at that line without leaving
the page, and every widget says in one sentence where its number comes from. We **finished the
certification phase of the on-site engine** — three experiments, each with its acceptance criterion
written down *before* it ran, all three verdicts recorded; and a fourth set of calls is frozen as a
held-out exam. And we **hardened the data-quality layer**: spoken promise dates now become calendar
dates by fixed rules rather than by a model, and your QA team's listening verdicts now sit at the
top of our reference ladder.

The roadmap page has been updated to match: stage 7 carries a fuller description of what the on-site
work now consists of, stage 10 records two additions, stage 12 is now **Manager cabinet & alerting**,
and a new stage 13 — **Live processing (GPU)** — is on the map as planned, with the hardware decision
open.

## The week at a glance

| Topic | Result |
|---|---|
| One page, everything on it | **Delivered.** The dashboard now holds the conversations: click a quoted line and the call opens on the same page, at that line. Every widget carries a short "where this number comes from" key |
| Comparison summary | **Delivered.** A one-page summary of how our reading of the calls sits beside the other system's reading and beside your own records |
| Operator standards | **Delivered.** Who is flagged most often, and how closely the agreed script is followed — as part of the same page rather than a separate report |
| Decisions document | **Delivered.** The open definition questions, each with the evidence under it, for your ruling |
| On-site engine | **Certification phase complete.** Three experiments, each with its bar registered in advance; all three verdicts recorded. A fourth held-out set of calls is frozen; the first exam of the complete toolkit is running now |
| Data quality | **Promise dates resolved by rule, not by model.** Your QA team's audio verdicts are now the top layer of our reference set. A processing step that quietly reported success while deciding nothing was caught and closed — and the same guard now covers every cloud step |
| Next | The exam result; a decision on short-term rented hardware to speed up training; the first working version of the manager cabinet |

---

## 1. Project stage

**Phase 1 (pilot): validation — in progress.**

The two things we have to prove before scaling are unchanged: the system must understand the call
correctly, and personal data must not leave the country. This week's work was mostly about the first
one, and about making what we already know **checkable by you** rather than only by us.

---

## 2. Everything on one page

The dashboard is now a single file that contains the conversations as well as the numbers.

**Click a quote, read the call.** Every extracted value has always carried the line it came from.
Now that line is a link: it opens the conversation on the same page, scrolled to the moment it was
said, with that line marked. Nothing to download, no second file, no separate viewer.

**Every widget says where its number comes from.** Each one carries a short key — what it is, where
the number is derived from, and what to do with it. This was your request, and it is the right one:
a number whose origin you cannot see is a number you have to take on trust.

**Comparison summary.** A one-page document putting three readings of the same calls beside each
other: ours, the other system's, and what your own records hold. It is written to be read in one
sitting and to make the disagreements visible rather than smoothed over.

**Operator standards.** The agent-facing view sits in the same page: which operators appear most
often in flagged calls, and how closely the agreed script is followed. It is presented as counts of
calls to look at, never as a score on a person.

**Decisions document.** The open definition questions are collected in one place, each with the
evidence gathered for it, so a ruling can be made on facts. One question in it now rests on four
separately drawn sets of calls that all give the same answer — which is exactly the kind of
agreement a definition should be decided on.

---

## 3. The on-site engine — the certification phase is finished

**What certification means here.** Before each experiment we wrote down what would count as success
— the exact bar, in advance — and only then ran it. Three experiments were run that way, and all
three verdicts are recorded.

**The honest result: none of the three cleared its bar**, so the engine stays as it was. We report
that plainly, because it is the more useful outcome of the two: the method held. Criteria fixed
before the run cannot be adjusted afterwards to fit what came out, and that is the whole reason for
fixing them.

**What the phase did produce.** A working certification suite; a clear picture of *where* the
remaining gap is and, just as importantly, where it is **not**; and the confidence that a future
improvement will be recognised as one rather than argued into existence.

**A fourth held-out set of calls is frozen.** It was frozen before a single call in it was
processed, and it has never been used to build, tune or teach any part of the engine. That is what
makes an exam on it meaningful.

**The first exam of the complete toolkit is running now.** Until this week the engine's parts had
been examined one at a time; this run puts the whole assembled toolkit through a held-out set at
once. It is on our own hardware, it costs no cloud processing, and the result is due next week.

---

## 4. Data quality

**Promise dates: resolved by rule, not by a model.** People do not say calendar dates — they say
"tomorrow", "the day after", "by the fifteenth". Those are now turned into actual dates by fixed,
written rules that a person can re-derive by hand. Two deliberate choices worth stating: a promise
is never placed in the past, and a *range* ("two or three days") is left unresolved rather than
having a point invented inside it. Your QA team, listening to the same calls, left the same range
blank — which is the answer we want to agree with.

**Your QA team's verdicts are now the top layer of the reference set.** The audio verdicts your
department returned outrank every machine reading we hold, including our cloud reference. Where they
disagree with us, they win, and each disagreement is recorded rather than averaged away. This is the
strongest quality signal in the project, because it comes from ears on the recording rather than from
another model.

**A caught incident, and the class of guard it produced.** One processing step reported success while
having decided nothing at all — its work had failed for an external reason and the failure was being
counted as a legitimate "no change". We found it, corrected the affected values, and then did the
part that matters: the same fail-closed guard now covers **every** cloud-processing step, so a step
that cannot do its job stops and says so instead of passing quietly. An empty result that looks like
a clean result is the failure mode we most distrust, and it is not the first time that
distrust has paid for itself.

---

## 5. What's next

1. **The exam result** — the first measurement of the complete on-site toolkit on a held-out set,
   due next week.
2. **A decision on short-term rented hardware** — whether to rent graphics hardware briefly to
   speed up training work. It is a cost-and-time decision, not a change of direction: the engine
   itself stays on premises, and this is separate from the live-processing hardware in stage 13.
3. **The manager cabinet, first working version** — the workspace behind stage 12, so the page stops
   being a file that gets sent and becomes something your managers open.

---

*Questions and corrections — to the development team. This report is aggregated: it contains no
personal data, no debtor phone numbers, no quotes, and no figures taken from your data.*
