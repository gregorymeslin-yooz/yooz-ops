# Yooz Name Generator — Complete Naming Rules

**Language:** English  
**Verified against the current implementation:** 10 November 2026  
**Scope:** Campaign names and all five asset types: Email, Landing Page, Thank You Page, Popup, and Workflow.

## 1. Authority, purpose, and interpretation

This document describes the existing Name Generator, not a proposed convention. It is based on the current `src/features/name-generator/naming.ts` and `src/features/name-generator/NameGenerator.tsx`. Example outputs in the tables were evaluated with the actual naming functions.

The tool builds a campaign identity in Step 1 and inserts asset attributes in Step 2. Two modes exist: **New Campaign** (default) and **Existing Salesforce Campaign**. They share the Step 2 mappings but have different validation and assembly behavior.

Important distinction for an auditing agent: **a name accepted or reproduced by the existing-campaign mode is not necessarily compliant with the new-campaign convention**. That mode deliberately permits nonconforming input with a warning. Do not turn those exceptions into strict business rules, and do not assume the current warning catches every naming issue.

All placeholders in square brackets below are descriptive, not literal output. `?` means an optional segment, not a character to insert.

## 2. Separators, case, and omitted segments

- Underscores `_` separate the field-derived segments.
- Hyphens `-` separate words in a newly entered campaign name.
- Hyphens also occur inside defined tokens: `EMEA-FR`, `FR-EMEAFR`, `Lead-Magnet`, `Follow-up-Event`, `ACQ-UNEN`, `WarmUp-Regular`, and dates, for example.
- `&` occurs in the two permitted combined target tokens: `BUS&EXP` and `BUS&PAR`.
- Generated tokens have the exact capitalization listed in this document. There is **no global lowercasing** in the Name Generator.
- New campaign text preserves the case of the ASCII letters entered by the user.
- Empty optional segments are omitted, not replaced by an empty slot. New-mode assembly therefore does not introduce double underscores, leading underscores, or trailing underscores.
- This omission guarantee does **not** apply to separators already present in a pasted Salesforce campaign: those are preserved during insertion.
- These are the Name Generator rules, not the separate UTM Builder's sanitization rules.

## 3. Step 1 — New Campaign

### 3.1 Sales Territory: mandatory multiple selection

The complete picklist is:

| Choice | Single-selection token |
|---|---|
| EMEA-FR | `EMEA-FR` |
| EMEA-EN | `EMEA-EN` |
| FR | `FR` |
| SP | `SP` |
| UK | `UK` |
| NORAM | `NORAM` |
| LATAM | `LATAM` |
| APAC | `APAC` |

- No selection: missing required input; no campaign or asset name is generated. This is not flagged as an invalid-combination error.
- Exactly one of these choices: valid; the token is that choice unchanged.
- Exactly two: only `EMEA-FR` + `FR` and `EMEA-EN` + `UK` are valid.
- Three or more: always invalid, including a valid pair plus any third choice.
- Selection order does not matter for the valid pairs. The output order is fixed.
- Invalid combinations show an inline territory error and block both the Step 1 name and every Step 2 name in New Campaign mode.

#### Complete two-territory matrix

| Selected territories | Result |
|---|---|
| EMEA-FR + EMEA-EN | Invalid — generation blocked |
| EMEA-FR + FR | `FR-EMEAFR` |
| EMEA-FR + SP | Invalid — generation blocked |
| EMEA-FR + UK | Invalid — generation blocked |
| EMEA-FR + NORAM | Invalid — generation blocked |
| EMEA-FR + LATAM | Invalid — generation blocked |
| EMEA-FR + APAC | Invalid — generation blocked |
| EMEA-EN + FR | Invalid — generation blocked |
| EMEA-EN + SP | Invalid — generation blocked |
| EMEA-EN + UK | `UK-EMEAEN` |
| EMEA-EN + NORAM | Invalid — generation blocked |
| EMEA-EN + LATAM | Invalid — generation blocked |
| EMEA-EN + APAC | Invalid — generation blocked |
| FR + SP | Invalid — generation blocked |
| FR + UK | Invalid — generation blocked |
| FR + NORAM | Invalid — generation blocked |
| FR + LATAM | Invalid — generation blocked |
| FR + APAC | Invalid — generation blocked |
| SP + UK | Invalid — generation blocked |
| SP + NORAM | Invalid — generation blocked |
| SP + LATAM | Invalid — generation blocked |
| SP + APAC | Invalid — generation blocked |
| UK + NORAM | Invalid — generation blocked |
| UK + LATAM | Invalid — generation blocked |
| UK + APAC | Invalid — generation blocked |
| NORAM + LATAM | Invalid — generation blocked |
| NORAM + APAC | Invalid — generation blocked |
| LATAM + APAC | Invalid — generation blocked |

### 3.2 Country: optional and conditional

The country field is available only when the territory selection contains `EMEA-FR` or `EMEA-EN`. It remains optional even then. A valid combined territory containing an EMEA choice also makes the matching country list available.

| Territory selection contains | Country label (exact picklist spelling) | Token |
|---|---|---|
| EMEA-FR | Belgium | `BEL` |
| EMEA-FR | Luxemburg | `LUX` |
| EMEA-FR | Switzerland | `SWI` |
| EMEA-EN | Germany | `GER` |
| EMEA-EN | Ireland | `IRL` |
| EMEA-EN | Netherlands | `NLD` |
| EMEA-EN | South Africa | `SAF` |
| EMEA-EN | United Arab Emirates | `UAE` |

Consequently:

- `FR-EMEAFR` can be followed by `BEL`, `LUX`, or `SWI`, or by no country.
- `UK-EMEAEN` can be followed by `GER`, `IRL`, `NLD`, `SAF`, or `UAE`, or by no country.
- Single `FR`, `SP`, `UK`, `NORAM`, `LATAM`, or `APAC` has no country field and no country segment in New Campaign mode.
- The UI clears a previously selected country when it no longer belongs to the available list.
- Internally, if an invalid territory selection contains both EMEA choices, the country-list helper prioritizes EMEA-FR. The invalid territory combination still blocks generation; this is not an additional supported combination.

### 3.3 Campaign Name: mandatory free text, sanitized for New Campaign

The exact transformation is:

1. Replace every character other than `a–z`, `A–Z`, `0–9`, or `-` with a hyphen.
2. Collapse consecutive hyphens to one.
3. Remove leading and trailing hyphens.

Spaces, underscores, punctuation, accented letters, non-ASCII letters, and emoji are therefore replaced, not retained. Accents are **not transliterated**: `é` does not become `e`. Case is preserved. A sanitized result that is empty is treated as a missing campaign name and blocks generation.

| Raw input | Sanitized campaign token |
|---|---|
| `Q4 Launch` | `Q4-Launch` |
| ` Q4 Launch ` | `Q4-Launch` |
| `Q4_Launch` | `Q4-Launch` |
| `Q4---Launch` | `Q4-Launch` |
| `Q4 / Launch!` | `Q4-Launch` |
| `Café Launch` | `Caf-Launch` |
| `---` | (empty — generation blocked) |
| `___` | (empty — generation blocked) |
| `Q4Launch` | `Q4Launch` |
| `q4 launch` | `q4-launch` |
| `A&B` | `A-B` |
| `2026-11-10` | `2026-11-10` |

There is no implemented campaign vocabulary, length limit, automatic case standardization, uniqueness check, or requirement that the campaign name contain a specific number of words.

### 3.4 Date: mandatory

- Chosen through the single-date calendar.
- Serialized in the generated name as `YYYY-MM-DD`, for example `2026-11-10`.
- The UI displays the selected date using a local date object to avoid a UTC-related one-day shift.
- No date selected: no new campaign name or asset name.
- The naming functions themselves only require a nonempty date in New Campaign mode; they do not independently validate an arbitrary date string. The normal UI supplies the calendar-formatted value.
- No implemented rule restricts the date to the future or defines whether it means launch date, event date, or another business date. Do not invent that interpretation.

### 3.5 Step 1 assembly

```text
[TERRITORY]_[COUNTRY?]_[CAMPAIGN-NAME]_[YYYY-MM-DD]
```

| Case | Example |
|---|---|
| Single territory, no country | `FR_Q4-Launch_2026-11-10` |
| EMEA territory, no country | `EMEA-FR_Q4-Launch_2026-11-10` |
| EMEA territory, country selected | `EMEA-FR_BEL_Q4-Launch_2026-11-10` |
| Combined territory, no country | `FR-EMEAFR_Q4-Launch_2026-11-10` |
| Combined territory, country selected | `UK-EMEAEN_GER_Q4-Launch_2026-11-10` |

Country is always immediately after territory when present. The campaign name and date follow. There is no asset code, target, content, or objective in this Step 1 result.

## 4. Step 1 — Existing Salesforce Campaign

### 4.1 Input and preservation

Only a pasted campaign name is required in this mode. New-mode territory, country, campaign text, and date fields are not used for assembly in this mode.

- Leading and trailing whitespace is trimmed.
- Internal characters, whitespace, capitalization, underscores, and hyphens are otherwise preserved.
- The Step 1 result is the trimmed input itself.
- Empty or whitespace-only input blocks generation.
- Nonempty input can be copied and used by Step 2 even if its format is nonconforming.

### 4.2 Exact nonblocking format check

The current warning check considers a pasted input valid if all these conditions hold:

1. After trimming, it contains an underscore.
2. Splitting on underscores yields at least three segments.
3. The first segment exactly matches one of the eight single territory tokens or `FR-EMEAFR` or `UK-EMEAEN`.
4. The last segment matches the shape `^\d{4}-\d{2}-\d{2}$`.
5. If the second segment exactly matches any known country acronym, at least one segment exists between that country segment and the final date. Otherwise, at least one segment exists between territory and date.

**This is a permissive structural check, not a complete nomenclature audit.** In particular, it does not:

- Validate the calendar reality of the date (`2026-99-99` passes the shape check).
- Verify that a recognized country belongs to the territory (`FR_BEL_...` can pass).
- Require a nonempty campaign-name segment (`FR__2026-11-10` can pass).
- Reject multiple campaign-name segments separated by underscores.
- Sanitize or reject spaces or special characters in the campaign name.
- Reject an unknown second segment as an invalid country: it is simply treated as part of the campaign name.
- Remove an asset segment already present in the pasted string.

The warning is displayed only for nonempty nonconforming input and never blocks copying or Step 2 generation.

### 4.3 Step 2 insertion algorithm for pasted campaigns

Let `MIDDLE` be the asset-specific segment defined below:

- **No underscore in input:** prepend the middle segment, then `_`, then the entire trimmed input.
- **Underscores present, second segment is a known country acronym:** insert the middle after the first two segments.
- **Underscores present, second segment is not a known country acronym:** insert the middle after the first segment.

Country recognition uses exact, case-sensitive membership in `BEL`, `GER`, `IRL`, `LUX`, `NLD`, `SAF`, `SWI`, `UAE`, regardless of whether the first segment is a valid territory or whether the country belongs to it.

All remaining pasted segments are kept as they were. Nothing repairs malformed territory, country, name, date, or separators. Re-pasting a name that already includes asset attributes can therefore duplicate those attributes.

#### Examples of both compliant and permissively accepted input

The inserted middle segment in this table is `BUS_EML_ACQ_Event`.

| Pasted input | Passes current warning check? | Generated email |
|---|---|---|
| `FR_Q4-Launch_2026-11-10` | Yes | `FR_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| `EMEA-FR_BEL_Q4-Launch_2026-11-10` | Yes | `EMEA-FR_BEL_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| `FR-EMEAFR_BEL_Q4-Launch_2026-11-10` | Yes | `FR-EMEAFR_BEL_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| `Plain campaign` | No — warning only | `BUS_EML_ACQ_Event_Plain campaign` |
| `XYZ_Q4-Launch_2026-11-10` | No — warning only | `XYZ_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| `FR_UNKNOWN_Q4-Launch_2026-11-10` | Yes | `FR_BUS_EML_ACQ_Event_UNKNOWN_Q4-Launch_2026-11-10` |
| `FR_BEL_Q4-Launch_2026-11-10` | Yes | `FR_BEL_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| `FR__2026-11-10` | Yes | `FR_BUS_EML_ACQ_Event__2026-11-10` |
| `FR_Q4_Launch_2026-11-10` | Yes | `FR_BUS_EML_ACQ_Event_Q4_Launch_2026-11-10` |
| `FR_Q4-Launch_2026-99-99` | Yes | `FR_BUS_EML_ACQ_Event_Q4-Launch_2026-99-99` |
| `FR_bel_Q4-Launch_2026-11-10` | Yes | `FR_BUS_EML_ACQ_Event_bel_Q4-Launch_2026-11-10` |
| ` FR_Q4 Launch_2026-11-10 ` | Yes | `FR_BUS_EML_ACQ_Event_Q4 Launch_2026-11-10` |
| `FR_BEL_2026-11-10` | No — warning only | `FR_BEL_BUS_EML_ACQ_Event_2026-11-10` |

For the input surrounded by spaces, the displayed/generated value is trimmed. These examples document existing behavior; they do not certify all of these names as compliant.

## 5. Step 2 — Mandatory attribute mappings

All five assets use the same Target, Content, Campaign Objective, and conditional Webinar Type choices. Selecting an invalid target combination or leaving any required attribute missing blocks **all five asset names**, not the Step 1 campaign result.

### 5.1 Target: mandatory multiple selection

| Selected targets | Token/result |
|---|---|
| Business | `BUS` |
| Expert | `EXP` |
| Partner | `PAR` |
| Business + Expert | `BUS&EXP` |
| Business + Partner | `BUS&PAR` |
| Expert + Partner | Invalid — all asset names blocked |
| Business + Expert + Partner | Invalid — all asset names blocked |

No selection is missing input and blocks all assets without an invalid-combination error. Selection order does not change the combined output. `EXP&BUS`, `PAR&BUS`, and `EXP&PAR` are not generated tokens. Three targets are never permitted.

### 5.2 Content: mandatory choice, sometimes no output token

| Content choice | Output token | Requires webinar type? | Offers email follow-up? |
|---|---|---|---|
| Communication | `Communication` | No | No |
| Event | `Event` | No | Yes |
| Infographic | `Infographic` | No | No |
| Lead Magnet | `Lead-Magnet` | No | No |
| Tradeshow | `Tradeshow` | No | Yes |
| Webinar | `Webinar` | Yes | Yes |
| Whitepaper | `Whitepaper` | No | No |
| Other | No content segment | No | No |

`Other` is a valid required choice: it does not block generation, but neither `Other` nor any substitute is inserted. An **unselected** Content field is different: it blocks all asset generation. Omitting content is not an optional blank choice in the UI; it is the result of choosing `Other`.

### 5.3 Webinar Type: conditional mandatory choice

Visible and required only when Content is `Webinar`.

| Choice | Token |
|---|---|
| Advanced | `ADV` |
| Customer | `CUS` |
| Educational | `EDU` |
| Training | `TRN` |

- Webinar without a selected type blocks all five assets.
- The token follows the content segment, including `Follow-up-Webinar` for an email follow-up.
- For non-Webinar content, there is no webinar type segment.
- Leaving Webinar content clears the webinar type selection in the UI.

### 5.4 Campaign Objective: mandatory single selection

| Choice | Base token |
|---|---|
| Acquisition | `ACQ` |
| Nurturing | `NUR` |
| Retention | `RET` |

This token is always after the asset code. Only Email can add the `-UNEN` suffix described next.

## 6. Email-only options — all cases

### 6.1 Targeting unengaged

- Optional checkbox, always visible.
- Changes **Email only**.
- Appends `-UNEN` to the objective, not to the target or the content.
- Complete objective set with this option: `ACQ-UNEN`, `NUR-UNEN`, `RET-UNEN`.
- Can be used with any Content choice, including `Other`.
- Does not rename Landing Page, Thank You Page, Popup, or Workflow.
- Not automatically cleared when Content changes.

### 6.2 Follow-up email

- Optional checkbox offered only for `Event`, `Tradeshow`, or `Webinar`.
- Changes **Email only**.
- Prefixes the content token with `Follow-up-`.
- Exact resulting content tokens: `Follow-up-Event`, `Follow-up-Tradeshow`, `Follow-up-Webinar`.
- For Webinar, the required webinar type remains a separate next segment.
- Changing Content to a choice outside those three clears the follow-up checkbox.
- Can be combined with unengaged; neither option replaces the other.

### 6.3 Complete email modifier matrix

`OBJECTIVE` below can independently be `ACQ`, `NUR`, or `RET`; `CONTENT` is one of the follow-up-enabled choices.

| Unengaged | Follow-up | Email middle pattern |
|---|---|---|
| Off | Off | `[TARGET]_EML_[OBJECTIVE]_[CONTENT]_[WEBINAR-TYPE?]` |
| On | Off | `[TARGET]_EML_[OBJECTIVE]-UNEN_[CONTENT]_[WEBINAR-TYPE?]` |
| Off | On | `[TARGET]_EML_[OBJECTIVE]_Follow-up-[CONTENT]_[WEBINAR-TYPE?]` |
| On | On | `[TARGET]_EML_[OBJECTIVE]-UNEN_Follow-up-[CONTENT]_[WEBINAR-TYPE?]` |

Concrete examples, using FR / Business / Acquisition / Q4 Launch / 2026-11-10 and Educational for Webinar:

| Content | Unengaged | Follow-up | Full email name |
|---|---|---|---|
| Tradeshow | Off | Off | `FR_BUS_EML_ACQ_Tradeshow_Q4-Launch_2026-11-10` |
| Tradeshow | Off | On | `FR_BUS_EML_ACQ_Follow-up-Tradeshow_Q4-Launch_2026-11-10` |
| Tradeshow | On | Off | `FR_BUS_EML_ACQ-UNEN_Tradeshow_Q4-Launch_2026-11-10` |
| Tradeshow | On | On | `FR_BUS_EML_ACQ-UNEN_Follow-up-Tradeshow_Q4-Launch_2026-11-10` |
| Webinar | Off | Off | `FR_BUS_EML_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |
| Webinar | Off | On | `FR_BUS_EML_ACQ_Follow-up-Webinar_EDU_Q4-Launch_2026-11-10` |
| Webinar | On | Off | `FR_BUS_EML_ACQ-UNEN_Webinar_EDU_Q4-Launch_2026-11-10` |
| Webinar | On | On | `FR_BUS_EML_ACQ-UNEN_Follow-up-Webinar_EDU_Q4-Launch_2026-11-10` |
| Event | Off | Off | `FR_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| Event | Off | On | `FR_BUS_EML_ACQ_Follow-up-Event_Q4-Launch_2026-11-10` |
| Event | On | Off | `FR_BUS_EML_ACQ-UNEN_Event_Q4-Launch_2026-11-10` |
| Event | On | On | `FR_BUS_EML_ACQ-UNEN_Follow-up-Event_Q4-Launch_2026-11-10` |

For Communication, Infographic, Lead Magnet, Whitepaper, or Other, follow-up is unavailable; only unengaged Off/On applies. In particular:

```text
Other, unengaged Off: FR_BUS_EML_ACQ_Q4-Launch_2026-11-10
Other, unengaged On:  FR_BUS_EML_ACQ-UNEN_Q4-Launch_2026-11-10
```

No `Other`, content placeholder, webinar type, or follow-up token is inserted in those cases.

## 7. Workflow-only options — all cases

### 7.1 Type of workflow

Optional and affects only Workflow.

| Choice | Token |
|---|---|
| Mobly | `Mobly` |
| Reengagement | `Reengagement` |
| SYNCGTW | `SYNCGTW` |
| SYNCLKD | `SYNCLKD` |
| SYNCMETA | `SYNCMETA` |
| Warm Up | `WarmUp` |

### 7.2 Frequency

Optional; exact choices are `1stand` and `Regular`. Preserve `1stand` exactly; do not “correct” its spelling.

- Disabled in the UI until a workflow type is selected.
- Reset to empty when workflow type is empty.
- Type selected, no frequency: type token alone.
- Type and frequency selected: one segment `[TYPE]-[FREQUENCY]`.
- Neither selected: entire workflow-detail segment omitted.
- The detail segment follows objective and precedes content, not after campaign name.
- Neither field affects the other four asset names.

**Implementation caveat:** the pure naming function can output a frequency-only segment if directly called with frequency but no type. The UI disables that state and clears frequency when there is no type. Frequency-only output is therefore not a supported normal user case, and should not be inferred as an intended convention.

### 7.3 Complete UI-supported type/frequency output matrix

All rows use FR / Business / Acquisition / Event / Q4 Launch / 2026-11-10.

With neither type nor frequency selected:

```text
FR_BUS_WF_ACQ_Event_Q4-Launch_2026-11-10
```

| Workflow type | Frequency | Full workflow name |
|---|---|---|
| Mobly | (none) | `FR_BUS_WF_ACQ_Mobly_Event_Q4-Launch_2026-11-10` |
| Mobly | 1stand | `FR_BUS_WF_ACQ_Mobly-1stand_Event_Q4-Launch_2026-11-10` |
| Mobly | Regular | `FR_BUS_WF_ACQ_Mobly-Regular_Event_Q4-Launch_2026-11-10` |
| Reengagement | (none) | `FR_BUS_WF_ACQ_Reengagement_Event_Q4-Launch_2026-11-10` |
| Reengagement | 1stand | `FR_BUS_WF_ACQ_Reengagement-1stand_Event_Q4-Launch_2026-11-10` |
| Reengagement | Regular | `FR_BUS_WF_ACQ_Reengagement-Regular_Event_Q4-Launch_2026-11-10` |
| SYNCGTW | (none) | `FR_BUS_WF_ACQ_SYNCGTW_Event_Q4-Launch_2026-11-10` |
| SYNCGTW | 1stand | `FR_BUS_WF_ACQ_SYNCGTW-1stand_Event_Q4-Launch_2026-11-10` |
| SYNCGTW | Regular | `FR_BUS_WF_ACQ_SYNCGTW-Regular_Event_Q4-Launch_2026-11-10` |
| SYNCLKD | (none) | `FR_BUS_WF_ACQ_SYNCLKD_Event_Q4-Launch_2026-11-10` |
| SYNCLKD | 1stand | `FR_BUS_WF_ACQ_SYNCLKD-1stand_Event_Q4-Launch_2026-11-10` |
| SYNCLKD | Regular | `FR_BUS_WF_ACQ_SYNCLKD-Regular_Event_Q4-Launch_2026-11-10` |
| SYNCMETA | (none) | `FR_BUS_WF_ACQ_SYNCMETA_Event_Q4-Launch_2026-11-10` |
| SYNCMETA | 1stand | `FR_BUS_WF_ACQ_SYNCMETA-1stand_Event_Q4-Launch_2026-11-10` |
| SYNCMETA | Regular | `FR_BUS_WF_ACQ_SYNCMETA-Regular_Event_Q4-Launch_2026-11-10` |
| Warm Up | (none) | `FR_BUS_WF_ACQ_WarmUp_Event_Q4-Launch_2026-11-10` |
| Warm Up | 1stand | `FR_BUS_WF_ACQ_WarmUp-1stand_Event_Q4-Launch_2026-11-10` |
| Warm Up | Regular | `FR_BUS_WF_ACQ_WarmUp-Regular_Event_Q4-Launch_2026-11-10` |

Workflow can independently use any valid target, objective, content, and campaign context. For `Other`, omit content; for Webinar, append its required webinar type after content. Examples:

```text
FR_BUS_WF_ACQ_Q4-Launch_2026-11-10
FR_BUS_WF_ACQ_WarmUp-Regular_Q4-Launch_2026-11-10
FR_BUS_WF_ACQ_Webinar_EDU_Q4-Launch_2026-11-10
FR_BUS_WF_ACQ_WarmUp-Regular_Webinar_EDU_Q4-Launch_2026-11-10
```

Unengaged and follow-up checkboxes do not alter any of these workflow names.

## 8. All five asset types — exact element order

### 8.1 Asset codes

| Asset | Exact code |
|---|---|
| Email | `EML` |
| Landing Page | `LP` |
| Thank You Page | `TYP` |
| Popup | `POPUP` |
| Workflow | `WF` |

The screen displays Email, Landing Page, Thank You Page, Popup, then Workflow. This display order does not alter segment order within a name.

### 8.2 Middle segments

```text
Email:
[TARGET]_EML_[OBJECTIVE or OBJECTIVE-UNEN]_[CONTENT or Follow-up-CONTENT?]_[WEBINAR-TYPE?]

Landing Page:
[TARGET]_LP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]

Thank You Page:
[TARGET]_TYP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]

Popup:
[TARGET]_POPUP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]

Workflow:
[TARGET]_WF_[OBJECTIVE]_[WORKFLOW-TYPE or WORKFLOW-TYPE-FREQUENCY?]_[CONTENT?]_[WEBINAR-TYPE?]
```

Every omitted segment is removed together with its otherwise redundant separator in New Campaign assembly. `WEBINAR-TYPE` appears only with Webinar content. `CONTENT` is absent only when the selected content is Other in the normal UI.

### 8.3 Full names in New Campaign mode

```text
Email:
[TERRITORY]_[COUNTRY?]_[TARGET]_EML_[OBJECTIVE or OBJECTIVE-UNEN]_[CONTENT or Follow-up-CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]

Landing Page:
[TERRITORY]_[COUNTRY?]_[TARGET]_LP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]

Thank You Page:
[TERRITORY]_[COUNTRY?]_[TARGET]_TYP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]

Popup:
[TERRITORY]_[COUNTRY?]_[TARGET]_POPUP_[OBJECTIVE]_[CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]

Workflow:
[TERRITORY]_[COUNTRY?]_[TARGET]_WF_[OBJECTIVE]_[WORKFLOW-DETAIL?]_[CONTENT?]_[WEBINAR-TYPE?]_[CAMPAIGN-NAME]_[DATE]
```

In all five cases, the campaign name is the penultimate segment and the date is the last segment in New Campaign mode. With no country, target is immediately after territory; with a country, it is immediately after country.

### 8.4 Segment counts for New Campaign output

| Output | Minimum | Maximum | Explanation |
|---|---|---|---|
| Step 1 campaign | 3 | 4 | Optional country |
| Email, Landing Page, Thank You Page, Popup | 6 | 9 | Base 6, plus optional country, content, and webinar type |
| Workflow | 6 | 10 | Same, plus optional workflow detail |

Unengaged, follow-up, combined target, combined territory, and workflow frequency modify a token rather than adding an underscore-separated segment. Webinar type implies Webinar content, so not every intermediate arrangement allowed by a count alone is valid. These counts cannot be applied reliably to permissive Existing Salesforce Campaign outputs.

## 9. Complete content-by-asset examples

Common inputs for the following tables: New Campaign; territory FR; no country; campaign text `Q4 Launch`; date `2026-11-10`; target Business; objective Acquisition; unengaged Off; follow-up Off; no workflow type or frequency. Webinar uses Educational.

### Content = Communication

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Communication_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Communication_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Communication_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Communication_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Communication_Q4-Launch_2026-11-10` |

### Content = Event

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Event_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Event_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Event_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Event_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Event_Q4-Launch_2026-11-10` |

### Content = Infographic

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Infographic_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Infographic_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Infographic_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Infographic_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Infographic_Q4-Launch_2026-11-10` |

### Content = Lead Magnet

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Lead-Magnet_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Lead-Magnet_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Lead-Magnet_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Lead-Magnet_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Lead-Magnet_Q4-Launch_2026-11-10` |

### Content = Tradeshow

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Tradeshow_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Tradeshow_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Tradeshow_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Tradeshow_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Tradeshow_Q4-Launch_2026-11-10` |

### Content = Webinar

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Webinar_EDU_Q4-Launch_2026-11-10` |

### Content = Whitepaper

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Whitepaper_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Whitepaper_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Whitepaper_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Whitepaper_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Whitepaper_Q4-Launch_2026-11-10` |

### Content = Other

| Asset | Full generated name |
|---|---|
| Email | `FR_BUS_EML_ACQ_Q4-Launch_2026-11-10` |
| Landing Page | `FR_BUS_LP_ACQ_Q4-Launch_2026-11-10` |
| Thank You Page | `FR_BUS_TYP_ACQ_Q4-Launch_2026-11-10` |
| Popup | `FR_BUS_POPUP_ACQ_Q4-Launch_2026-11-10` |
| Workflow | `FR_BUS_WF_ACQ_Q4-Launch_2026-11-10` |

## 10. Combined examples across rules

These cases illustrate independent valid combinations, not extra exceptions:

| Inputs | Full name |
|---|---|
| EMEA-FR; Belgium; Business + Expert; Email; Retention; Other; unengaged On | `EMEA-FR_BEL_BUS&EXP_EML_RET-UNEN_Q4-Launch_2026-11-10` |
| EMEA-FR + FR; Switzerland; Business + Partner; Email; Nurturing; Webinar Training; both email options On | `FR-EMEAFR_SWI_BUS&PAR_EML_NUR-UNEN_Follow-up-Webinar_TRN_Q4-Launch_2026-11-10` |
| EMEA-EN + UK; Germany; Expert; Landing Page; Acquisition; Webinar Advanced | `UK-EMEAEN_GER_EXP_LP_ACQ_Webinar_ADV_Q4-Launch_2026-11-10` |
| NORAM; Partner; Thank You Page; Retention; Lead Magnet | `NORAM_PAR_TYP_RET_Lead-Magnet_Q4-Launch_2026-11-10` |
| APAC; Business; Popup; Nurturing; Other | `APAC_BUS_POPUP_NUR_Q4-Launch_2026-11-10` |
| LATAM; Business + Expert; Workflow; Acquisition; Warm Up Regular; Webinar Customer | `LATAM_BUS&EXP_WF_ACQ_WarmUp-Regular_Webinar_CUS_Q4-Launch_2026-11-10` |

No additional dependency is implemented between target, objective, territory, content, or workflow type beyond the rules explicitly listed. For example, the tool does not restrict Retention to Customer webinars or SYNCGTW to Webinar content.

## 11. Missing, invalid, and conditional-state matrix

| State in the normal UI | Step 1 result | Step 2 results |
|---|---|---|
| New Campaign: no territory | Unavailable | All unavailable |
| New Campaign: invalid territory combination | Unavailable; inline error | All unavailable |
| New Campaign: blank/empty-after-sanitization campaign text | Unavailable | All unavailable |
| New Campaign: no date | Unavailable | All unavailable |
| New Campaign: no optional country | Available if required inputs complete | Available if attributes complete |
| Existing Campaign: blank/whitespace-only input | Unavailable | All unavailable |
| Existing Campaign: nonempty input failing format check | Available; warning only | Available if attributes complete |
| No Target | Unaffected | All unavailable |
| Expert + Partner, or all three targets | Unaffected | All unavailable; inline error |
| Content not selected | Unaffected | All unavailable |
| Content Other selected | Unaffected | Available; no content segment |
| Campaign Objective not selected | Unaffected | All unavailable |
| Webinar selected, no Webinar Type | Unaffected | All unavailable |
| Non-Webinar content | Unaffected | No webinar type needed or included |
| No Workflow Type | Unaffected | Workflow generated without detail; Frequency disabled |
| Workflow Type selected, no Frequency | Unaffected | Workflow generated with type alone |
| Email options toggled | Unaffected | Email changes only |
| Workflow options changed | Unaffected | Workflow changes only |

Each output has its own Copy action. Copy is disabled when that output is unavailable. Validation is inline, not a generation popup. Names update live without a Generate button. Copy success shows confirmation; copy failure displays an error. Copying does not change the naming rules.

## 12. State changes and localization

- Country is cleared when no longer valid for the selected territory list.
- Webinar Type is cleared when Content is no longer Webinar.
- Follow-up is cleared when Content is outside Event, Tradeshow, and Webinar.
- Frequency is cleared when Workflow Type is empty.
- Switching New/Existing mode changes the campaign context used, but does not explicitly clear the stored fields of either mode or the Step 2 attributes.
- The Name Generator has no implemented saved-name history or persistence of form values; refreshing resets its form state.
- UI labels support English and French. Picklist values and generated naming tokens remain unchanged across languages.

## 13. Guidance for an AI compliance auditor

### 13.1 Strict new-convention audit

For a name claimed to follow the New Campaign convention:

1. Identify the asset type and require its exact asset code.
2. Require a known territory in the first segment.
3. Recognize an optional country only in the second segment and check its territory compatibility.
4. Require one of the five valid target tokens: `BUS`, `EXP`, `PAR`, `BUS&EXP`, `BUS&PAR`.
5. Require the asset code immediately after target.
6. Require `ACQ`, `NUR`, or `RET`, allowing `-UNEN` only for Email.
7. Identify an optional Workflow Detail only for Workflow, immediately after objective.
8. Identify content from the seven emitted tokens. Content absence corresponds to Other; do not require a literal `Other`.
9. Allow `Follow-up-` only for Email with Event, Tradeshow, or Webinar.
10. Require one of `ADV`, `CUS`, `EDU`, `TRN` immediately after Webinar or Follow-up-Webinar; forbid a webinar type with other content.
11. Require the campaign-name segment to be nonempty and match the sanitization result: ASCII letters/digits/hyphens only, no repeated hyphens, and no edge hyphens.
12. Require the date segment at the end with `YYYY-MM-DD` shape. If independently checking a real calendar date, label that as a stronger audit than the existing pasted-input validator.
13. Reject extra empty segments and unexplained additional segments for strict new-convention compliance.
14. Preserve token case and spellings; do not apply UTM lowercasing rules.

### 13.2 Existing-campaign and historical caveats

Do not report that the application prevents every noncompliant name. Existing Campaign mode intentionally reproduces nonempty input and inserts asset attributes despite a warning. It can preserve legacy underscores, spaces, invalid dates, unknown tokens, empty segments, and territory/country inconsistencies.

Without the original form inputs, a final asset name alone may not reveal whether it came from New Campaign mode or permissive Existing Campaign mode. An auditor should distinguish:

- **Conforms to the new naming convention**.
- **Does not conform to the new convention, but can be produced through permissive pasted-input behavior**.
- **Cannot be resolved from the name alone** where source context is required.

The current tool defines no historical cutoff, grandfathering policy, tolerance score, severity scheme, or automatic renaming policy. Those must not be invented as if they already existed.

## 14. Implementation boundaries — no invented rules

The picklists constrain the normal UI, but the pure helpers are not a standalone hardened validator for arbitrary external input. Some helpers pass through unknown strings or check only presence. This document lists the actual UI-supported values and separately notes meaningful implementation exceptions.

No additional rule is implemented for:

- Maximum name length or unique names.
- Approved campaign-name vocabulary or semantic meaning.
- Territory-specific language, objective, target, or content restrictions beyond conditional Country.
- Associations between objective and webinar type.
- Associations between workflow type and content.
- A definition of the business meaning of Frequency `1stand` or `Regular`.
- Date meaning, permitted date range, or historical migration policy.
- Automatic validation of existing HubSpot asset names; this tool generates names and checks the pasted campaign structure only.

**End of specification.** All supported cases compose from the territory/country cases, target combinations, content/webinar dependencies, objective mappings, asset-specific rules, optional modifier matrices, and the two campaign-mode assembly algorithms above.
