# Topic codebook – 2027 content topics (Matthieu) + candidate topics

Classify each content item (web page, marketing email, campaign, form message) by its MAIN subject = what the content promises / is about.
Give: primary_topic (exactly one code below), secondary_topic (optional, one code), threads (any of AI, FRAUD, ERP), confidence (High / Medium / Low), short reason.

## The 6 proposed topics
- T1 TEAM_CAPACITY – Doing more with the same team: AP team workload, manual/repetitive work burden on people, staffing, hiring freeze, headcount, burnout, turnover, retention, PTO / vacation / summer coverage, tribal knowledge, onboarding new AP staff, agility of the accounting team, "same team, more impact", "do more with less", time freed for strategic work.
  Keywords: team, capacity, headcount, staff, staffing, hire, hiring, burnout, turnover, retention, workload, overtime, vacation, PTO, summer, coverage, "more with less", "same team", agile team, busywork, time savings for the team, productivity of the team.
- T2 APPROVAL_AUTOMATION – Invoice / purchase approval workflows: routing, approvers, approval rules & thresholds, chasing approvals, approval bottlenecks & delays, mobile approval, same-day approval, purchase requests / PO approval, configurable workflows.
  Keywords: approval, approve, approver, sign-off, workflow(s), routing, bottleneck, chasing, delay(s), purchase request, PO approval, "same-day approval", dynamic workflows.
- T3 CONTROLS – Internal controls, audit & compliance, traceability, audit trail, segregation of duties, policies, governance, risk, 1099 / tax compliance in NORAM, and FRAUD PREVENTION (fraud is treated as part of Controls AND tagged with the FRAUD thread, so results can be shown with and without fraud).
  Keywords: control(s), audit, audit-ready, audit trail, compliance, traceability, SOX, segregation of duties, governance, policy, risk, 1099, fraud, fake invoice, duplicate payment, YoozProtect, security.
- T4 VENDOR_MANAGEMENT – Vendors/suppliers and paying them: vendor onboarding, vendor master data, bank-detail verification, vendor statements & reconciliation, supplier relationships & communication, supplier portal, AND PAYMENTS (ACH, virtual card, check, payment runs, early-payment discounts, late fees, cash back, YoozPay, P2P "purchase to pay" when payment-focused).
  Keywords: vendor, supplier, statement(s), reconciliation, vendor onboarding, bank details, payment(s), pay, ACH, virtual card, check, early-pay, discount, late fee, cash back, YoozPay, YoozVendorStatements.
- T5 MULTI_ENTITY_GROWTH – Multiple entities / locations / sites / rooftops / branches, consolidation, standardization across sites, M&A / acquisitions, growth, scaling, expansion, growing pains, distributed teams, unified AP.
  Keywords: multi-entity, multi-location, multi-site, entities, subsidiaries, rooftops, branches, locations, consolidat*, standardiz*, M&A, merger, acquisition, growth, grow, scale, scaling, scalable, expansion, distributed, unified AP, growing pains.
- T6 YEAR_END_CLOSE – Month-end / year-end close, closing the books faster, accruals, cut-off, fiscal year, year-end audit preparation, end-of-year planning.
  Keywords: close, closing, month-end, year-end, end of year, fiscal year, books, accrual(s), cut-off, close faster.

## Candidate topics NOT in the list (to test whether something important is missing)
- C1 CAPTURE_AI_AUTOMATION – Invoice capture, data extraction, OCR, eliminating data entry, AI/agentic automation of AP processing, touchless AP, automatic coding/allocation.
- C2 PO_MATCHING – PO matching, 2-/3-way match, line-level matching.
- C3 ERP_INTEGRATION – Integration with a named ERP/DMS/accounting system (NetSuite, Sage, Dynamics, Acumatica, QuickBooks, CDK, Karmak…) as the main subject.
- C4 VISIBILITY_CASHFLOW – Real-time visibility, reporting, analytics, spend/commitment tracking, budgets, cash-flow management.
- C5 COST_ROI – AP cost, cost per invoice, ROI, savings, business case, cost of manual AP.
- C6 EINVOICING_TAX – E-invoicing mandates/regulation (e.g. French 2026 reform, Verifactu, Peppol), VAT, electronic invoicing compliance.
- C7 CFO_STRATEGY – CFO role, finance transformation, finance leadership trends, strategic finance (no specific operational topic).
- C8 PAPERLESS_DIGITIZATION – Going paperless / dematerialization / digital transformation of AP as the main promise (generic "digitize AP").

## Non-topic buckets
- G1 GENERIC_PRODUCT – Generic AP automation / all-product pages & demos, homepage, product, pricing, brand (Lean Financial Operations umbrella with no specific subject), customer stories without a dominant subject.
- G2 INDUSTRY_GENERIC – Industry page/guide whose subject is the industry in general (construction, automotive, trucking, manufacturing…) with no dominant topic above. (If an industry asset clearly focuses on one topic, use that topic.)
- X0 NOT_CONTENT – Utility pages (contact, support, unsubscribe, login, 404, thank-you, legal, careers), customer training / product updates for existing users, partner recruitment, events/tradeshow logistics, internal.

## Threads (tags, any number)
- AI – AI, machine learning, agentic, intelligent automation is central to the message.
- FRAUD – fraud, fake invoices, impersonation, duplicate payments, YoozProtect.
- ERP – a specific ERP/DMS integration or "connects to your ERP" is central.

Rules: main subject wins; do not infer content you cannot see — classify only from title/URL/name/subject/description given; if the item gives no topic information, use G1 (generic) or X0, never guess a topic. Confidence High = topic explicit in title/subject; Medium = clear from URL/slug or description; Low = indirect.
