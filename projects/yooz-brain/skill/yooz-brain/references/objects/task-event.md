# Task & Event

*Status: Drafted · Sources: Greg (voice notes 2026-11-10), Salesforce schema 2026-11-09*

## How to read activities
- No single field reliably says what an activity is. `Type`, `Type_de_T_che__c` (Task Type), `Subtype__c` and `Subject` are used inconsistently: sometimes one holds the event's name, sometimes the task's name.
- **Read them all** (Type, Task Type, Subtype, Subject, description, call result) before classifying an activity as an email, a call, a meeting or a demo. Look for wordings such as "Discovery Call" or "Demo".
- Link activities to people with `WhoId` (Lead/Contact) and to accounts or opps with `WhatId`.
- Salesloft is no longer used and writes nothing. Gong reads data but is not believed to write activities.

## BookIt
- A BookIt meeting normally creates an Event (`BookItMeetingOriginPick__c`: `BookIt Form` = web form with LeanData routing, `BookIt Link` = direct link).
- Pitfall: a prospect who booked through BookIt without a form may not exist in Salesforce (BookIt is not synced). Their lead is created later by the SDR if they are qualified, so match those meetings by name, email or company. Never conclude "no demo" without checking.
- `BookItMeetingStatus__c`: `Canceled` / `Rescheduled`; empty = meeting still on.
