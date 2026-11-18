# 2027 Content Angles vs NORAM data (Oct 2026)

**Demandeur :** Mathieu Tagnon (Head of Content) · **Statut :** ✅ livré 21/10/2026

Request: Matthieu Tagnon (Head of Content) asked to back the 2027 content plan angles (Simplicity, Security, Productivity, Scalability) with numbers.
Deliverables (21/10/2026): deck "2027_Content_Angles_vs_NORAM_Data.pptx" (15 slides, Yooz template) + annex xlsx (scorecard, angle x format, first touch, assets, plan themes, sensitivity, inventory, codebook, 109 influenced opps).

Spec for independent recomputation: [`SPEC_metrics.md`](SPEC_metrics.md).

## Pipeline
Run from a folder with raw/ out/ ref/:
- raw: TPR exports of 09/10 (E0 cohort, E1 email clicks, E2 forms, E5 opps contact roles, E9 page views) from Greg's Downloads; Salesforce campaign members = "Campaigns with Campaign Members-2026-10-15" (original E6 missing). E0/E1/E2/E5/E9 hashes match theme-reporting/raw_sha256.txt.
- 04_build_base → 05_classify (TPR rules `projects/theme-performance-reporting/ref/regles_sujets.csv`) → 06_postprocess → 08_clean_link (756 opps, 20,942,139 USD pipeline: same as TPR QA) → 10_angles (codebook.py) → 11_metrics.

## Decisions
- Validated by Greg 21/10: media partners (IndustryDive, IOFM, NADA, MSDW, CBO…) = separate channel; mapping rule = deck angle definitions first, calendar placement breaks ties (ERP → Scalability, payments → Simplicity); format = PPT + Excel annex.
- Method choices: 'Sent' campaign members excluded; rates on leads with first touch ≤ 10/08/2026 (60 days hindsight); demo-session touches excluded from first touch/influence; influenced opp = touch strictly before opp creation day.
- QA: independent recompute (agent, from SPEC_metrics.md) = 0 differences on 40 rows; mapping audit by a second agent, fixes applied (MSDW → unknown topic, gp/bc regex, intuit(?!ive)).

## Key results
All channels, baseline MQL 13.4%, opp rate 0.9%, 109 of 756 opps with a prior content touch:
- Simplicity 1,319 leads, MQL 17.3%, 16 opps $916k — 75% of leads from payments content (97% bought); core "fragmented stack" message untested.
- Security 1,596 leads, MQL 15.8%, 11 opps $326k — owned 24.8% vs 19.6%; YoozProtect demos 49%; bought fraud eBooks 2.8% vs 8.7%.
- Productivity 391 leads, MQL 29.5%, opp 3.5%, 9 opps $272k — almost only demos (29% vs 38% demo avg); no gated content.
- Scalability 1,098 leads, MQL 14.6%, 18 opps $640k, 2 won $59k — ERP/DMS demos 12 opps, 2 won.
- Format: demos 37.7% MQL, EDU webinars 21.7%, gated 8.2%. All-product demos: 42 opps $1.74M.

## Open
Topics of IOFM demo days Oct-25/Feb/Apr/Aug-26 and MSDW assets unknown; Fraud Readiness Report 2026 campaign (372 leads, Sept 2026) too recent.
