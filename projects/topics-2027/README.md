# Topics 2027 – results (step 1, delivered 2026-11-02)

**Demandeur :** Mathieu Tagnon (Head of Content) · **Statut :** 🟡 étape 1 livrée, étape 2 en attente de validation des topics

Deliverables (Greg's Downloads): 2027_Topics_Data_Check.pptx (12 slides, EN, for Matthieu) + 2027_Topics_Data_Check_Annex.xlsx (all proof data + QA).
Codebook: [`ref/TOPICS.md`](ref/TOPICS.md).
Former local workspace (session, not kept): /home/claude/t2 (out/, scripts2/00_qa_overrides.py, 01–06, 01b_demand_corrected.py; deliver/build_deck.py, build_xlsx.py; qa/).

## Key figures (after QA corrections)
Demand = share of usable SDR/partner notes (NORAM 936, UK 843, FR 260):
- C1 capture/data entry 43% NORAM / 36% UK (not in the six) · T2 approvals 31% / 40% · T4 vendor & payments 28% / 29% (template-only tags removed) · C3 ERP 21% / 23% · T1 team capacity 15% / 13% · C2 PO matching 12% / 39% · T5 multi-entity stated need 10% / 15% (21% / 47% incl. entity counts) · T3 controls 9% / 8% · T6 close 1.7% / 3.0% · FR: C6 e-invoicing 39%.
- The six cover 61% of NORAM notes; 14% are only about capture.

Email click index vs same format (NORAM / EMEA): T5 1.93 / 0.79 · T3 1.18 / 0.78 · T1 0.90 / 0.82 · T4 0.70 / 0.58 · T2 0.48 / 0.50 · T6 2.27 (8 emails, n/s) / 0.81.

Web views NORAM: T3 22.3k, T4 16.6k, T5 14.4k, T2 2.6k, T1 0.5k, T6 0.1k. EMEA dominated by C6 (127k).

NORAM cohort (avg MQL 13.4%, owned 19.6%): T4 1,041 leads, 14.5% MQL, 9 opps $759k · T5 660, 15.8%, 7 opps $254k · T3 1,463, 11.8% (owned 24.4%), 8 opps $310k · T1/T2 ≤32 leads (no history) · T6 none · C3 ERP 11 opps, 2 won.

## Recommendation given
Keep T4, T5, T3; rework T2 hook (high need, low clicks); add capture/data entry by widening T1 ("less manual work, same team", AI thread); T6 light at year end. EMEA: approvals & matching; e-invoicing as local track.
Sequence: Jan–Feb T4 · Mar–Apr T2 · May–Jun T5 · Jul–Aug T1+capture · Sep–Oct T3 & fraud · Nov–Dec T6 (timing hooks = judgment; no seasonality measurable).

## QA
Independent recompute 702/703 match. Classification re-review: web 98%, email 100%, notes 93–97% agree/acceptable. Fixes applied: 41 T4 template tags removed, T5 need-only, 3 T6 added, 5 emails + 4 web pages relabelled.

## Next
Step 2 once topics are validated: best past content per topic, NORAM + EMEA.
message_topics.csv and corrected CSVs were not saved (Claude project storage limit at the time) — contain personal data, keep outside this repo.
