# AquaGuard AI – Epics and User Stories

## 1. Project overview

AquaGuard AI monitors dissolved oxygen (DO), pH, temperature, turbidity and salinity in several pond zones, flags unreliable sensor readings, warns early when DO is trending toward a low limit, and shows zone-specific alerts on a farmer-friendly dashboard in English, Malayalam, Konkani and Tamil. The backlog below has 5 epics and 15 stories, sized for a 1.5-day hackathon prototype with three Must-Have features.

**Team roles**

| Role | Responsibility |
| --- | --- |
| Business Analyst (BA) | Requirements, priorities, alert wording, documentation |
| UI/UX Designer (UX) | User flows, dashboard design, Tamil/English text layout, presentation |
| Developer (Dev) | Architecture, data ingestion, detection logic, backend and frontend |
| Tester/QA (QA) | Acceptance criteria, test cases, scenario validation, traceability |

**Assumptions**

- A1: Zone A may use a real sensor node if hardware is available; other zones use simulated data. If no hardware exists, all zones are simulated.
- A2: Every simulated value is labelled "Simulated" on screen. No result from simulated data is presented as real pond performance.
- A3: Early warning in this prototype is rule-based trend detection. No AI accuracy or prediction performance is claimed until tested.
- A4: The system only recommends actions. Automatic aerator control is not built or demonstrated.
- A5: Thresholds in the criteria below (DO limits, stuck-value count, spike size, reading interval) are demo defaults and stay configurable.

**Unresolved decisions**

- D1: Is real hardware available, and for how many zones? Owner: Developer.
- D2: Final DO warning and critical limits and the species to demo. These need confirming with fisheries guidance. Owner: BA.
- D3: Reading interval for the demo (default 10 s). Owner: Developer.
- D4: Who reviews the Tamil wording for accuracy. Owner: UI/UX Designer.

## 2. Epics

| ID | Epic | Description | Business value | Related farmer needs |
| --- | --- | --- | --- | --- |
| EPIC-01 | Multi-Section Data Acquisition | Collect DO, pH, temperature, turbidity and salinity per pond section (zone or cage) from real or simulated sources, and label simulated data | Pond-wide visibility instead of one-point checks | See the whole pond; know which data is real |
| EPIC-02 | Sensor Reliability Assessment | Detect impossible, stuck or sharply jumping readings and show how far each reading can be trusted | Stops the farmer acting on wrong data; reduces false alarms | Trust the numbers; know when to check a sensor |
| EPIC-03 | Early Risk Detection and Alerts | Detect falling DO trends, raise section alerts, and for Kerala and Goa raise pond-wide inflow, salinity, bloom and alkalinity alerts, each with next steps | Time to act before fish are stressed | Early warning; know where and what to do |
| EPIC-04 | Farmer Dashboard and Language Support | Show sections, status, trends and alerts in a simple English, Malayalam, Konkani and Tamil interface | Usable by farmers with limited technical skill | Simple screen in a familiar language |
| EPIC-05 | Validation and Demo Readiness | Test with normal, faulty-sensor, falling-DO, inflow, salinity and bloom scenarios and record results honestly | Shows features work as described, with no overclaiming | Confidence that alerts behave as promised |

## 3. User stories

Priority uses MoSCoW. Complexity is Low, Medium or High.

### US-01 · EPIC-01 · Must Have · Medium · Owner: Developer

**As a** fish farmer, **I want** DO, pH, temperature, turbidity and salinity collected for each pond section, **so that** I can see the condition of the whole pond.

**Acceptance criteria (Given-When-Then):** Given 3 sections are configured, when the system runs for 10 minutes, then every section has all 5 parameters stored with timestamp and section ID at each reading, and a new reading reaches the dashboard within 5 s.

**Depends on:** none

### US-02 · EPIC-01 · Must Have · Low · Owner: UI/UX Designer

**As a** fish farmer, **I want** each section marked as real or simulated, **so that** I never mistake demo data for real pond readings.

**Acceptance criteria:** Given a section uses simulated data, when its values appear anywhere on screen, then the label "Simulated" appears next to it. Given it uses a real sensor, it shows "Live sensor". 100% of sections carry one label.

**Depends on:** US-01

### US-03 · EPIC-02 · Must Have · Low · Owner: Developer

**As a** fish farmer, **I want** impossible readings flagged, **so that** I do not act on obviously wrong data.

**Acceptance criteria:** Given a reading outside its allowed range (DO 0 to 20 mg/L, pH 0 to 14, temperature 0 to 50 °C, turbidity 0 to 3000 NTU, salinity 0 to 45 ppt), when it arrives, then it is marked Unreliable within one reading and excluded from risk checks. In a test of 10 injected impossible values, all 10 are flagged.

**Depends on:** US-01

### US-04 · EPIC-02 · Must Have · Medium · Owner: Developer

**As a** fish farmer, **I want** a stuck or suddenly jumping sensor detected, **so that** a faulty probe does not trigger false alarms or hide a real problem.

**Acceptance criteria:** Given a sensor repeats the same value, when it reaches 6 identical readings it is marked Doubtful and at 10 Unreliable. Given a change larger than the parameter limit (DO 2.0 mg/L, pH 1.0, temperature 4 °C, turbidity 150 NTU, salinity 3 ppt) between two readings, it is flagged with the reason. QA records detected vs injected faults; no accuracy percentage is claimed beyond that table.

**Depends on:** US-01

### US-05 · EPIC-02 · Must Have · Medium · Owner: UI/UX Designer

**As a** fish farmer, **I want** each section to show whether its readings are reliable, **so that** I know when to check the sensor instead of trusting the number.

**Acceptance criteria:** Given a section has an Unreliable or Doubtful sensor, when I open the dashboard, then it shows a reliability badge and a "check sensor" message. Given a danger-range DO value comes from an unreliable sensor, then "verify with a test kit" is shown and the value is never silently hidden.

**Depends on:** US-03, US-04

### US-06 · EPIC-03 · Must Have · High · Owner: Developer

**As a** fish farmer, **I want** a warning when DO in a section is falling toward the low limit, **so that** I can act before fish are stressed.

**Acceptance criteria:** Given a section has reliable DO readings, when a line fitted to the last 8 readings (at least 6) reaches the Critical limit within 60 min, then the section turns Warning within one reading. Given DO is stable, no warning appears.

*Extension (Should):* given DO falls by 1.0 mg/L or more over 2 readings in at least 2 sections, one pond-wide inflow Warning appears.
*Extension (Could):* turbidity up 40% and pH up 0.4 over 8 readings shows a bloom hint.
The rule is trend-based and never called AI prediction.

**Depends on:** US-01, US-05

### US-07 · EPIC-03 · Must Have · Medium · Owner: UI/UX Designer

**As a** fish farmer, **I want** an alert that names the affected section, **so that** I know where in the pond to go.

**Acceptance criteria:** Given a section enters Warning or Critical, when the alert is raised, then it shows section name, parameter, value, time and severity within 5 s, and other sections are unaffected.

*Extension (Should):* salinity outside the species range raises a Warning.
*Extension (Could):* a hand-entered alkalinity below 50 mg/L (indicative) raises a Warning.
Alerts show "Cleared" with the time when the cause ends.

**Depends on:** US-06

### US-08 · EPIC-03 · Should Have · Low · Owner: Business Analyst

**As a** fish farmer, **I want** a suggested next step with each alert, **so that** I know what to check first.

**Acceptance criteria:** Given an alert is shown, when I read it, then it includes at most 2 steps (for example check with a test kit, inspect aeration, delay water exchange, check the sluice gate). The text states the system only recommends and does not control equipment.

**Depends on:** US-07

### US-09 · EPIC-03 · Could Have · Medium · Owner: Developer

**As a** fish farmer, **I want** alerts sent to my phone, **so that** I am notified when I am away from the dashboard.

**Acceptance criteria:** Given an alert is raised, when messaging is configured, then one message with section and severity reaches the test phone or chat within 30 s. Not committed; depends on a chosen channel.

**Depends on:** US-07

### US-10 · EPIC-03 · Won't Have · High · Owner: Business Analyst

**As a** fish farmer, **I want** the aerator to start automatically on a Critical alert, **so that** oxygen is restored without my action.

**Acceptance criteria:** Excluded from this prototype. No relay or aerator control is built or demonstrated; the dashboard says "recommendation only".

**Depends on:** US-07

### US-11 · EPIC-04 · Must Have · Medium · Owner: UI/UX Designer

**As a** fish farmer, **I want** one screen showing every section's values and status, **so that** I can judge the pond at a glance.

**Acceptance criteria:** Given 3 sections are running, when I open the dashboard, then each shows its 5 values, a status of Normal, Warning or Critical, a reliability badge and the real or simulated label on one screen at 1366×768.

*Extension (Could):* sections can be named Zone or Cage.

**Depends on:** US-01, US-02, US-05

### US-12 · EPIC-04 · Should Have · Medium · Owner: UI/UX Designer

**As a** fish farmer, **I want** to switch the dashboard between English, Malayalam, Konkani and Tamil, **so that** I can read it in my own language.

**Acceptance criteria:** Given I select a language, when the page updates, then labels, statuses, alerts and steps appear in it. 100% of labels and alert templates exist in all four languages; a native speaker of each of Malayalam, Konkani and Tamil reviews the wording.

**Depends on:** US-11, US-07

### US-13 · EPIC-04 · Should Have · Medium · Owner: Developer

**As a** fish farmer, **I want** a recent trend chart for each section, **so that** I can see how DO has been moving.

**Acceptance criteria:** Given a section has at least 30 readings, when I open its chart, then DO over the last 4 pond-hours is plotted with Warning and Critical limits marked, and it updates with each reading.

**Depends on:** US-01

### US-14 · EPIC-04 · Could Have · Low · Owner: Developer

**As a** fish farmer, **I want** a list of past alerts, **so that** I can review what happened overnight.

**Acceptance criteria:** Given alerts have occurred, when I open the alert list, then they are shown newest first with section, severity, time and cleared time. The target keeps them after a refresh.

**Depends on:** US-07

### US-15 · EPIC-05 · Must Have · Medium · Owner: Tester/QA

**As a** fish farmer, **I want** the system checked with normal, faulty-sensor, falling-DO, inflow, salinity and bloom scenarios, **so that** I can trust its alerts behave as described.

**Acceptance criteria:** Given the scenarios run on simulated data, when QA compares outputs with expected results, then each Must story has at least one passing test, and a table shows expected vs actual for 11 checks on 5 random seeds. The report states results come from simulated data and makes no accuracy claim.

**Depends on:** US-03, US-04, US-05, US-06, US-07

## 4. Epic-to-story mapping

| Epic | Stories | Must | Should | Could | Won't |
| --- | --- | --- | --- | --- | --- |
| EPIC-01 Multi-Section Data Acquisition | US-01, US-02 | 2 | 0 | 0 | 0 |
| EPIC-02 Sensor Reliability Assessment | US-03, US-04, US-05 | 3 | 0 | 0 | 0 |
| EPIC-03 Early Risk Detection and Alerts | US-06, US-07, US-08, US-09, US-10 | 2 | 1 | 1 | 1 |
| EPIC-04 Farmer Dashboard and Language Support | US-11, US-12, US-13, US-14 | 1 | 2 | 1 | 0 |
| EPIC-05 Validation and Demo Readiness | US-15 | 1 | 0 | 0 | 0 |
| **Total** | 15 stories | 9 | 3 | 2 | 1 |

Every story maps to exactly one epic, and no epic is empty.

## 5. Must-Have features for the 1.5-day prototype

Three features only. All 9 Must Have stories serve one of them or validate them.

| # | Must-Have feature | Stories | Why it is in |
| --- | --- | --- | --- |
| 1 | Multi-zone monitoring dashboard (DO, pH, temperature, turbidity, salinity; real or simulated labelled) | US-01, US-02, US-11 | The base of the demo; shows the whole pond at once |
| 2 | Sensor reliability and unusual-reading detection | US-03, US-04, US-05 | The main difference from plain monitors; easy to demonstrate with injected faults |
| 3 | Early DO-trend warning with zone-specific alert | US-06, US-07 | Delivers the farmer's key benefit: time to act |

US-15 (scenario validation) is a supporting Must Have for QA, not a fourth feature.

## 6. Should-Have and Could-Have features

| Priority | Story | Feature | Build only if |
| --- | --- | --- | --- |
| Should Have | US-08 | Suggested next step with each alert | Must Haves pass testing |
| Should Have | US-12 | Four-language switch | Wording is reviewed |
| Should Have | US-13 | Per-zone DO trend chart | Dashboard is stable |
| Could Have | US-09 | Phone alert (channel not decided) | Time remains and a channel is chosen |
| Could Have | US-14 | Alert history list | Alerts are already stored |

If time is short, drop in this order: US-14, US-09, US-13, US-12, US-08.

## 7. Excluded from the current prototype

- Automatic aerator or pump control (US-10); the system only recommends.
- Claims of AI accuracy or prediction performance; early warning is rule-based and tested only on scenarios.
- Ammonia sensing, disease detection, feed advice and fish-behaviour cameras.
- Solar, LoRa or NB-IoT field deployment and a real-pond pilot.
- Multi-pond management, user accounts, billing and cooperative dashboards.
- Native mobile app and voice alerts.

## 8. Validation checklist

| Requirement | Status | Evidence |
| --- | --- | --- |
| 3–5 epics | Met | 5 epics (EPIC-01 to EPIC-05) |
| 8–15 user stories | Met | 15 stories (US-01 to US-15) |
| Maximum 3 Must-Have features | Met | 3 features in section 5 |
| Every story maps to one epic | Met | Section 4 |
| Priority, Given-When-Then criteria, dependencies, complexity and owner on every story | Met | Section 3 |
| Each role owns work | Met | Primary owners: Developer 8, UI/UX Designer 4, BA 2, QA 1 (QA also writes test cases for every story) |
| Simulated data clearly labelled | Met | US-02 |
| No unproven AI accuracy claim | Met | Assumption A3, US-06, US-15 |
| No automatic aerator control assumed | Met | A4, US-10 (Won't Have) |
| No invented hardware, API or integration | Met | Hardware and phone channel listed as open decisions D1 and US-09 |
| Runnable and demonstrable in 1.5 days | Plausible, not yet proven | 9 Must stories are mostly Low or Medium complexity; US-06 is the one High item. Re-check after the first half day |

## 9. Prototype status against this backlog

The delivered prototype is a single browser page (AquaGuard AI Prototype) that simulates the three zones and runs the rules. It has no backend, database or live sensor. Time-lapse: one reading stands for 5 pond-minutes, so the demo defaults in the criteria map as follows: a stuck sensor is Doubtful after 6 and Unreliable after 10 identical readings, and the DO trend uses the last 8 trusted readings.

| Story | Priority | Status in prototype | Note |
| --- | --- | --- | --- |
| US-01 | Must | Built (simulated) | 3 zones, 4 parameters (salinity planned), one reading per 5 pond-minutes |
| US-02 | Must | Built | Every zone shows "Simulated" |
| US-03 | Must | Built | Range check on all 4 parameters |
| US-04 | Must | Built | Stuck and spike checks |
| US-05 | Must | Built | Reliability badge, "verify with a test kit", last trusted reading shown |
| US-06 | Must | Built | Rule-based trend, no AI claim |
| US-07 | Must | Built | Zone, value, time, severity; alerts show "Cleared" |
| US-08 | Should | Built | Two steps per alert; recommendation only |
| US-09 | Could | Not built | No phone channel chosen |
| US-10 | Won't | Not built | By decision |
| US-11 | Must | Built | One-screen dashboard |
| US-12 | Should | Built (English and Tamil only) | Malayalam and Konkani planned (section 10); wording needs native review |
| US-13 | Should | Built | Inline DO chart with limit lines |
| US-14 | Could | Partly | Recent alerts list; not kept after refresh |
| US-15 | Must | Built | 7 scenario checks; all pass for tilapia, carp, pangasius and shrimp profiles |

**Differences from the criteria above:** the 10-second reading interval and the 5.0 and 3.0 mg/L placeholder limits were replaced in the prototype by the time-lapse and by species limits (Tilapia 4.0 and 3.0, Carp 4.0 and 3.0, Pangasius 3.0 and 2.0, Shrimp 4.5 and 3.5 mg/L). These limits are indicative and still need confirming with fisheries guidance (D2).

## 10. Kerala and Goa additions

> **Repo note:** this section describes the planned Kerala/Goa extension. The v1 prototype in `prototype/` does not yet include salinity, Malayalam/Konkani, inflow/bloom alerts or the alkalinity log.


The pilot region is now Kerala (backwater, brackish and cage farms) and Goa (Khazan ponds). The backlog stays at 15 stories, so the new features extend existing stories instead of adding new ones. None of them changes the three Must-Have features.

| Addition | Priority | Extends | What it adds | Status in prototype |
| --- | --- | --- | --- | --- |
| Malayalam and Konkani, Tamil kept | Should | US-12 | Interface and alerts in English, Malayalam, Konkani and Tamil; 100% of labels in each language; native-speaker review needed | Planned (not in v1 prototype) |
| Salinity parameter and brackish species (Pearlspot, Grey mullet, Shrimp) | Should | US-01, US-07, US-11 | Fifth value per section; Warning when outside the species range | Planned (not in v1 prototype) |
| Oxygen-poor inflow alert | Should | US-06, US-07, US-15 | One pond-wide Warning when DO falls by 1.0 mg/L or more over 2 readings in at least 2 sections | Planned (not in v1 prototype) |
| Algal-bloom hint | Could | US-06 | Hint when turbidity rises 40% and pH rises 0.4 over 8 readings | Planned (not in v1 prototype) |
| Alkalinity test log | Could | US-07 | Hand-entered test; Warning below 50 mg/L (indicative) | Planned (not in v1 prototype) |
| Cage naming | Could | US-11 | Sections can be named Zone or Cage | Planned (not in v1 prototype) |

**Planned test update (US-15):** the scenario tests would grow to 11 checks, adding oxygen-poor inflow (alert within 2 readings), salinity surge, algal bloom and "faults alone raise no inflow alert".

**Assumptions and open decisions:** salinity ranges, the 50 mg/L alkalinity level and species DO limits are placeholders to confirm with fisheries guidance (D2). Malayalam, Konkani and Tamil wording needs native review (D4). The documents do not claim that Kerala and Goa account for most cases nationally.
