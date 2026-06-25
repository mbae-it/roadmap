# Call Intelligence — Project Roadmap

A working map of where the project stands. Each stage describes **what it delivers** and **when it is considered done**. This page updates as the project progresses.

**Last updated:** 2026-06-25

**Legend:** ✅ done · 🟡 in progress · ⬜ planned

---

- [x] **1. Foundation ready** ✅
  Servers, repository and processing environment are set up and verified.
  *Done when:* the environment is in place and validated.

- [x] **2. One call, end to end** ✅
  A single real call goes the whole way: recording → text → key fields (amount owed, promise to pay, outcome) → stored with supporting quotes.
  *Done when:* one call produces meaningful fields with evidence quotes.

- [ ] **3. A full day of calls** 🟡 *in progress*
  A day's worth of calls processed in batches; long calls split by speaker (agent / debtor); very short calls filtered out cheaply; field accuracy cross-checked.
  *Done when:* a full day is processed and the cost picture is confirmed on real numbers.

- [ ] **4. First dashboard screen** ⬜
  A simple web page showing the key numbers: conversion / promises to pay, agents, objections, compliance.
  *Done when:* the page shows real metrics from processed calls.

- [ ] **5. Choosing the best setup** ⬜
  Compare processing setups on a sample and pick the working combination for quality and cost.
  *Done when:* a justified working setup is chosen, with quality differences shown on numbers.

- [ ] **6. Search & pinned views** ⬜
  Search across calls (filters + text + meaning) with drill-down to the exact quote, timestamp and audio; frequent searches pinned as permanent widgets.
  *Done when:* calls can be found by query and a query pinned as a widget.

- [ ] **7. Our own on-site model** ⬜
  Try a fully on-premise setup and measure whether it matches cloud quality.
  *Done when:* a clear "matches / doesn't match" verdict on numbers.

- [ ] **8. Rich storage & data protection** ⬜
  Store everything needed with headroom; for cloud processing, personal data is anonymized before it leaves.
  *Done when:* the storage model is locked in and anonymization works.

- [ ] **9. Emotion & agent-quality insights** ⬜
  Prototype emotion and agent-performance scoring on a sample; measure reliability.
  *Done when:* reliability is measured and product-readiness is clear.

---

**Phase 2 (later):** plain-language search, near-real-time updates, full-scale volume, roles & access.

<!--
INTERNAL — checkpoint tag contract (not shown on the rendered page).
This file is auto-generated from checkpoint-* tags in the private repo.
  stage 1: Foundation ready             -> checkpoint-0-setup
  stage 2: One call, end to end         -> checkpoint-1-one-call
  stage 3: A full day of calls          -> checkpoint-2-batch-day
  stage 4: First dashboard screen       -> checkpoint-3-dashboard-v1
  stage 5: Choosing the best setup      -> checkpoint-4-quality-matrix
  stage 6: Search & pinned views        -> checkpoint-5-search-pin
  stage 7: Our own on-site model        -> checkpoint-6-local-model
  stage 8: Rich storage & data protection -> checkpoint-7-storage-pii
  stage 9: Emotion & agent-quality insights -> checkpoint-8-heavy-metrics
  Phase 2 umbrella             -> checkpoint-9-phase2
-->
