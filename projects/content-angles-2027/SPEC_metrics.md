# Spec – 2027 angle analysis (for independent recomputation)
Inputs (all in /home/claude/w/out):
- content_touches.csv : one row per content touch. Columns used: contact_id, ts (timestamp), source, label, texte_classe, title, angle, asset, subtopic, confidence, channel (Owned / Syndication (bought) / Media partner), session_demo (True/False), piece, format.
  Already excludes campaign members with status 'Sent'. angle ∈ {Simplicity, Security, Productivity, Scalability, No single angle, Unknown topic}.
- contacts.csv : cohort (17,673 contacts entered MEL/MQL 01/10/2025–09/10/2026). contact_id, mql (MQL first entry datetime, may be empty).
- liens_contact_opp.csv : contact ↔ new-business opp links. 'Record ID - Contact', 'Opportunity ID', created (opp created date).
- opps_new_business.csv : 'Opportunity ID', pipe (USD), arr_won (USD), won (True/False).

Definitions for a group G of touches (e.g. angle == Security, optionally channel == Owned, optionally format == ...):
- day = ts truncated to day. MATURE = 2026-08-10.
- leads_reached = distinct contact_id in G (all touches, incl. session_demo).
- Let S = G without session_demo touches. first(c) = min day of contact c in S.
- leads_matured = contacts with first(c) <= MATURE.
- MQL rate = among matured contacts whose mql is empty OR mql >= first(c): share with mql non-empty.
- opp rate = (matured contacts having a linked opp with created > first(c), strictly later day) / leads_matured.
- opps (influenced) = distinct opps linked to any contact of S where that contact has a touch in S with day < opp created (strictly earlier day). No maturity filter. pipeline = sum pipe, won = count won, arr_won = sum arr_won over those opps.
- Baseline = same computation on ALL content touches (all six angles) for the same channel scope.
