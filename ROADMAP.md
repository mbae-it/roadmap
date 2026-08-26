# Call Intelligence — Project Roadmap

A working map of where the project stands. Each stage describes **what it delivers** and **when it is considered done**. This page updates as the project progresses.

**Last updated:** 2026-08-26

**Legend:** ✅ done · 🟡 in progress · ⬜ planned

---

- [x] **1. Foundation ready** ✅
  Servers, repository and processing environment are set up and verified.
  *Done when:* the environment is in place and validated.

- [x] **2. One call, end to end** ✅
  A single real call goes the whole way: recording → text → key fields (amount owed, promise to pay, outcome) → stored with supporting quotes.
  *Done when:* one call produces meaningful fields with evidence quotes.

- [x] **3. A full day of calls** ✅
  A day's worth of calls processed in batches; long calls split by speaker (agent / debtor); very short calls filtered out cheaply; field accuracy cross-checked.
  *Done when:* a full day is processed and the cost picture is confirmed on real numbers.

- [x] **4. First dashboard screen** ✅
  A simple web page showing the key numbers: conversion / promises to pay, agents, objections, compliance. The page has since been restructured into the four sections your team specified, so the operational numbers come first and the method notes sit behind them.
  *Done when:* the page shows real metrics from processed calls.

- [x] **5. Choosing the best setup** ✅
  Compare processing setups on a sample and pick the working combination for quality and cost.
  *Done when:* a justified working setup is chosen, with quality differences shown on numbers.

- [ ] **6. Search & pinned views** ⬜
  Drill-down from any number to the exact quoted line, the moment it was said and the conversation around it — delivered, inside the report page itself. Search across calls by free text and by meaning, and pinning a frequent query as a permanent widget, remain planned and belong with the workspace of stage 12.
  *Done when:* calls can be found by query and a query pinned as a widget.

- [ ] **7. Our own on-site model** 🟡 *in progress*
  On-site engine v1 assembled and examined against the cloud reference on calls it has never seen. The training toolkit is now complete and the first fine-tuning run is under way, on a graphics card rented inside the country: a staged trial run passed its checks first, and the bars the result must clear were registered before it started. Four held-out sets of calls are frozen before use; the certification suite fixes its quality gates in advance of each experiment; and audio verdicts from your QA team feed the reference set.
  *Done when:* the on-site engine reaches reference quality on the agreed gates, measured on calls it has never seen.

- [x] **8. Rich storage & data protection** ✅
  Store everything needed with headroom; for cloud processing, personal data is removed before anything leaves — phone numbers and account-shaped identifiers structurally, with every batch scanned at the boundary and a batch that fails the scan stopped rather than sent. Personal names are reduced by an automatic recognizer rather than guaranteed eliminated, which is one of the reasons the sensitive work stays in-country.
  *Done when:* the storage model is locked in and anonymization works.

- [ ] **9. Emotion & agent-quality insights** ⬜
  Prototype emotion and agent-performance scoring on a sample; measure reliability.
  *Done when:* reliability is measured and product-readiness is clear.

- [x] **10. Objection & conversation analytics** ✅
  Recurring objections and the conversation patterns around them, drawn from the processed calls and written up as a working playbook for your team. Also from this layer: spoken promise dates turned into calendar dates, and an operator view — who is flagged most often, and how closely the agreed script is followed. The two-tier promise rule your team approved is live on the page, and the vocabulary of offers is agreed.
  *Done when:* a playbook derived from analyzed calls is delivered.

- [x] **11. Cross-check with client systems** ✅
  An automated bridge that reconciles what the calls contain against what your own systems record, so the two can be compared without manual work. A mismatch view is now live on the report page as a pilot on part of one batch, badged as one: every divergence carries its trail on both sides, and the reconciliation's own error rate was measured by hand before anything was shown.
  *Done when:* the reconciliation runs from our side without manual data exchange and its output is reviewable divergence by divergence. Running it unattended on a schedule belongs to stage 12.

- [ ] **12. Performance Feedback Module & alerting** ⬜
  A workspace for your managers over the processed calls — saved views, drill-down to the exact quote — together with ongoing checks that raise an alert when something needs a person to look at it. The module's logical frame is your team's own document; it has been reviewed in both languages and its core entities are agreed. It awaits your finalisation, and nothing is built or estimated before then.
  *Done when:* the workspace is in your hands, checks run on a schedule and alerts reach the right people.

- [ ] **13. Live processing (GPU)** 🟡 *in progress*
  Calls processed as they come in, on hardware sized for it, so results are available without waiting for a batch to be run. A graphics card is now rented inside the country and in use for the engine's training work, which is what puts this stage in motion; the sizing, and whether to rent by the month or to buy, follow from what is measured on it. Live processing itself has not started, and the hardware decision remains yours.
  *Done when:* calls are processed live at working volume on the agreed hardware.

---

**Phase 2 (later):** plain-language search, near-real-time updates, full-scale volume, roles & access.

<!--
INTERNAL — checkpoint tag contract (not shown on the rendered page).
This file is auto-generated from checkpoint-* tags in the private repo.
  stage  1: Foundation ready                   -> checkpoint-0-setup
  stage  2: One call, end to end               -> checkpoint-1-one-call
  stage  3: A full day of calls                -> checkpoint-2-batch-day
  stage  4: First dashboard screen             -> checkpoint-3-dashboard-v1
  stage  5: Choosing the best setup            -> checkpoint-4-quality-matrix
  stage  6: Search & pinned views              -> checkpoint-5-search-pin
  stage  7: Our own on-site model              -> checkpoint-6-local-model
  stage  8: Rich storage & data protection     -> checkpoint-7-storage-pii
  stage  9: Emotion & agent-quality insights   -> checkpoint-8-heavy-metrics
  stage 10: Objection & conversation analytics -> checkpoint-objection-analytics
  stage 11: Cross-check with client systems    -> checkpoint-crm-crosscheck
  stage 12: Performance Feedback Module & alerting -> checkpoint-audit-alerting
  stage 13: Live processing (GPU)              -> checkpoint-live-gpu
  Phase 2 umbrella                            -> checkpoint-9-phase2
  a stage is IN PROGRESS when <its tag>-inprogress also exists.
-->
