# HubSpot ↔ Salesforce

*Status: Drafted · Sources: Greg (voice notes 2026-11-09/10), Lead glossary 2026-11-09, inventory*

## Source of truth
- **Salesforce is the real CRM and wins in most cases.**
- HubSpot wins for what originates in HubSpot: form submissions, scoring and email engagement.
- Field-level sync direction is in the Lead glossary ("Hubspot Sync" column): `Two way`, `Always use Salesforce`, `Prefer Salesforce unless blank`. [TO ADD: per-field values in objects/*.md]

## Inclusion list (what syncs)
- A HubSpot contact syncs to Salesforce only if it belongs to the HubSpot list "Sales Force Inclusion list" (list 7978). Filter groups, from a screenshot on 2026-11-10 — a contact needs at least one group in full:
  1. Email + Country + Last name + Company name + Request Type all known
  2. Last name + Email + Country + "ID du compte" (Salesforce Account ID) all known
  3. Salesforce Lead ID known
  4. Salesforce Contact ID known
- Consequence: a new HubSpot contact with no Request Type (no form) and no account ID does not sync. A HubSpot contact with no Salesforce ID is usually outside the list, not a sync bug.
- BookIt is not synced with Salesforce and does not ask for every inclusion-list field. A prospect who books through BookIt without filling a form may therefore not exist in Salesforce until the SDR creates the lead.

## Records and identity
- HubSpot has one contact per email, which can be associated with several companies. Salesforce allows unlimited Leads/Contacts with the same email. A partner present at several clients exists X times in Salesforce and once in HubSpot.
- A HubSpot contact carries either a Salesforce Lead ID or a Salesforce Contact ID.
- After conversion, the Lead and the Contact both still exist in Salesforce.

## Probance
- Probance is used only to email MCLs (`Status = 'Suspect'`). Yooz may not email them from HubSpot. They often get the Probance version of an email that HubSpot sends to engaged leads.
- Probance knows each record's status: once a lead is MEL or higher, Probance can no longer contact it.
- Probance tracks opens and clicks, but these never change the status.

## Demand dates
- Use `Last_Hubspot_Demand_Date_Yooz__c` (excludes newsletter and subscribe/unsubscribe forms, filled since 2024-02).
- Do not use `Date_Derni_re_Demande__c`: it counts every form, including the newsletter.
