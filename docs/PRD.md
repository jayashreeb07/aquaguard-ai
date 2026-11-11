# AquaGuard AI – Product Requirements Document (PRD)

Nov 1, 2026 · Jayashree

## Product overview

AquaGuard AI is a solar-powered, LoRa-based IoT system that monitors fish pond water quality, warns farmers before a night-time oxygen crash, and starts the aerator within safety limits. This PRD defines what the first version must do; the market need is in the [MRD](MRD.md).

**Goals**

- Warn the farmer before dissolved oxygen (DO) reaches the critical limit for the chosen species.
- Show which pond and which zone is at risk, and whether the sensor reading can be trusted.
- Reach farmers by Tamil/English SMS, app push and voice call.
- Keep protecting the pond when the internet is down.
- Keep hardware cost low enough for small farmers.

**Non-goals for version 1**

- Proving a reduction in fish mortality (needs a field pilot).
- Continuous ammonia sensing (ammonia uses periodic TAN entry).
- Feed management and cooperative dashboards.

## Users and key scenarios

| User | Needs | Main scenario |
| --- | --- | --- |
| Farmer (primary) | Simple alerts in Tamil, clear action | Gets an evening SMS: oxygen likely to drop before dawn; aerator started |
| Farm manager | Compare ponds, review history | Opens the farm view, sees the highest-risk pond first |
| Technician | Install and maintain nodes | Gets a calibration-due or faulty-sensor reminder |
| Admin / operator | Manage ponds, species, users | Sets the species profile and alert contacts for each pond |

**Key scenarios**

1. Normal night: readings stay in range, no alert, history is stored.
2. Predicted oxygen crash: the forecast crosses the critical limit, the farmer is alerted, and the aerator starts if the reading is trusted.
3. Faulty sensor: a stuck or jumping reading is flagged and does not trigger the aerator on its own.
4. Network down: the node runs its local low-DO rule and stores data until the link returns.

## Scope

| Area | Version 1 (hackathon prototype) | Later |
| --- | --- | --- |
| Sensors | Temperature, pH, DO, turbidity; ammonia via TAN entry | Continuous ammonia sensor |
| Coverage | One pond, two zones, simulated second pond for the demo | Several ponds with LoRa nodes |
| Communication | ESP32 to dashboard over MQTT | Full LoRa gateway and field range testing |
| Alerts | Dashboard, Tamil/English SMS, one Tamil voice alert | Escalation to backup contacts |
| Control | Aerator relay demo with failsafe | Pump control, field aerators |
| AI | Reliability checks, risk score, DO forecast on sample data | Forecast validated on real pond data |
| Power | Mains or battery on the bench | Solar and battery deployment |

## Functional requirements

| ID | Requirement | Priority |
| --- | --- | --- |
| FR-1 | Read temperature, pH, DO and turbidity from each zone at a set interval | Must |
| FR-2 | Accept periodic TAN input and compute toxic ammonia (NH3) from TAN, pH and temperature | Must |
| FR-3 | Store species profiles with warning and critical limits, editable by the farmer | Must |
| FR-4 | Raise warning and critical alerts when readings leave the species limits | Must |
| FR-5 | Flag readings as trusted, suspect or faulty (stuck, spike, drift, disconnected) | Must |
| FR-6 | Calculate a 0 to 100 risk score per zone and pond, with the main cause shown | Must |
| FR-7 | Forecast the pre-dawn DO minimum and alert in the evening if it will cross the critical limit | Must |
| FR-8 | Send Tamil/English SMS and app notifications; send a Tamil voice call if a critical alert is not acknowledged | Must |
| FR-9 | Start the aerator on a confirmed low-DO condition, with minimum on/off times and manual override | Must |
| FR-10 | Run the low-DO rule on the node when the network is down, and store data until it returns | Must |
| FR-11 | Show live values and day, week and season history per pond, with alert and aeration events marked | Must |
| FR-12 | Remind the user when a sensor calibration is due | Should |
| FR-13 | Rank all ponds by risk and compare them side by side | Should |
| FR-14 | Use a weather forecast (cloud cover, rain, air temperature) as a forecast input | Should |
| FR-15 | Support solar-powered LoRa nodes sending to a farm gateway | Should |
| FR-16 | Control a water pump with the same safety rules as the aerator | Later |

## Epics and user stories

The backlog has 5 epics and 15 stories, sized for a 1.5-day prototype with three Must-Have features: the multi-zone dashboard, sensor reliability checks, and the early DO-trend warning. Full Given-When-Then criteria, dependencies, owners and team roles are in [epics-and-user-stories.md](epics-and-user-stories.md).

| Epic | Description | Stories |
| --- | --- | --- |
| EPIC-01 Multi-Zone Data Acquisition | Collect DO, pH, temperature and turbidity per zone from real or simulated sources, with simulated data labelled | US-01, US-02 |
| EPIC-02 Sensor Reliability Assessment | Detect impossible, stuck or jumping readings and show how far each reading can be trusted | US-03, US-04, US-05 |
| EPIC-03 Early Risk Detection and Zone Alerts | Detect falling DO trends, raise zone-specific alerts and suggest next steps | US-06 to US-10 |
| EPIC-04 Farmer Dashboard and Language Support | Show zones, status, trends and alerts in Tamil and English | US-11 to US-14 |
| EPIC-05 Validation and Demo Readiness | Test normal, faulty-sensor and falling-DO scenarios and report results honestly | US-15 |

| ID | User story (farmer wants...) | Priority | Linked FR |
| --- | --- | --- | --- |
| US-01 | DO, pH, temperature and turbidity collected for each pond zone | Must | FR-1 |
| US-02 | Each zone marked as real or simulated | Must | FR-1 |
| US-03 | Impossible readings flagged | Must | FR-5 |
| US-04 | A stuck or suddenly jumping sensor detected | Must | FR-5 |
| US-05 | A reliability badge and "check sensor" message per zone | Must | FR-5 |
| US-06 | A warning when DO is falling toward the low limit | Must | FR-7 |
| US-07 | An alert that names the affected zone | Must | FR-4 |
| US-08 | A suggested next step with each alert | Should | FR-4 |
| US-09 | Alerts sent to the phone | Could | FR-8 |
| US-10 | Aerator starts automatically on a Critical alert | Won't (prototype) | FR-9 |
| US-11 | One screen showing every zone's values and status | Must | FR-11 |
| US-12 | Switch between Tamil and English | Should | FR-8 |
| US-13 | A recent DO trend chart per zone | Should | FR-11 |
| US-14 | A list of past alerts | Could | FR-11 |
| US-15 | System checked with normal, faulty-sensor and falling-DO scenarios | Must | Acceptance tests |

**Scope differences to resolve.** The story backlog is narrower than the requirements above:

- US-10 and assumption A4 exclude automatic aerator control, while FR-9 and release phase 8 include an aerator relay demo.
- The backlog treats early warning as rule-based trend detection (US-06), while FR-7 describes an ML forecast.
- Voice alerts, ammonia, LoRa and solar nodes, and multi-pond views are excluded from the 1.5-day prototype but appear in the requirements.

Note: the epics and stories document has since been extended with salinity, Malayalam/Konkani/Tamil languages and Kerala and Goa additions; see section 10 of that document.

## AI and data requirements

**Reliability checks (FR-5)**

- Range and rate-of-change limits per parameter
- Stuck-value detection over a set time window
- DO against temperature saturation, and agreement with neighbouring zones
- Drift tracking against the last calibration

**Risk score (FR-6)**

- Combines DO, pH, temperature, turbidity and NH3 against the species profile into a green, amber or red band
- Pond score equals the worst trusted zone

**Forecast (FR-7)**

- Inputs: previous 1 to 6 hours of DO, temperature and turbidity, time of day, species, and the weather forecast when available
- Outputs: estimated pre-dawn DO minimum, a 30 to 60 minute DO estimate, and a Low, Medium or High risk
- Start with Random Forest or XGBoost, compared against a moving-average baseline using MAE and RMSE
- Training data: a synthetic diurnal DO simulator to test the pipeline, then real pond data; synthetic results are labelled as such

**Data**

- Time-series readings with zone, pond, sensor ID and quality flag
- Events: alerts, acknowledgements, aerator on/off, calibrations, TAN entries

## Non-functional requirements

| Area | Requirement |
| --- | --- |
| Safety | The aerator is never driven by the AI forecast alone; relay-failure protection, minimum on/off times, manual override and an emergency fallback are required |
| Offline operation | The node keeps its low-DO rule and stores readings locally; data syncs without loss when the link returns |
| Reliability | A node that stops reporting is flagged as offline; sensors must hold calibration between scheduled reminders |
| Power | Solar panel with rechargeable battery; deep sleep between readings |
| Alert delivery | Time from risk detection to SMS delivered is measured and logged |
| Security | User login, per-farm data separation, encrypted transport for MQTT and API |
| Usability | Tamil and English; icons and short action cards for low-literacy users |
| Cost | Planning range from the team's cost estimate of ₹12,350 to ₹22,200 for one node and gateway, before ammonia and installation; quotes to be confirmed |
| Maintainability | Probes are easy to remove for cleaning; cleaning and calibration reminders are tracked |

## User experience

**Main screens**

1. **Farm overview:** all ponds as cards, ranked by risk score, with a green, amber or red band.
2. **Pond detail:** zone map with coloured zones, live values, trend charts, and sensor status (trusted, suspect, faulty).
3. **Alert card:** the risk, the zone, the forecast value and one clear action, for example "Zone C: oxygen likely to drop before dawn. Aerator started. Check the aerator."
4. **History:** day, week and season charts with alert and aeration events marked.
5. **Setup:** species profile, alert contacts, language, and TAN test entry.

**Alert flow**

1. The system detects or forecasts a risk and checks that the reading is trusted.
2. It sends an SMS and app push in the chosen language.
3. If the aerator conditions are met, it starts the aerator and logs the action.
4. If the farmer has not acknowledged within a set time, it places a Tamil voice call.
5. The alert closes when DO returns to the safe range or the farmer resolves it.

## Release plan

Build the working core first, then add field features, as the team's hackathon plan recommends.

| Phase | Work | Output |
| --- | --- | --- |
| 1 | ESP32 with temperature, pH, DO and turbidity sensors | Live readings |
| 2 | MQTT and time-series database | Data stored in the cloud |
| 3 | Dashboard with live and historical charts | Pond view |
| 4 | Species limits and threshold alerts | Warning and critical alerts |
| 5 | Sensor fault detection and trust status | Fault warning |
| 6 | Risk score and DO forecast on sample data | Risk and prediction demo |
| 7 | Tamil SMS and voice alerts | Farmer alerts |
| 8 | Aerator relay with failsafe | Automatic control demo |
| 9 | LoRa and solar nodes | Remote sensing |
| 10 | Multi-zone and multi-pond views | Comparison dashboard |

Phases 1 to 8 make up the hackathon demo. Phases 9 and 10, real-data forecast validation and continuous ammonia sensing are presented as the extension.

## Acceptance criteria, risks and open questions

**Acceptance tests**

| Test | Pass condition |
| --- | --- |
| Sensor accuracy | Readings agree with a reference test kit within the tolerance the team sets |
| Fault detection | Injected stuck, spike and disconnect faults are flagged in a bench test |
| Alert path | A simulated low-DO event produces dashboard, SMS and voice alerts; delivery time is logged |
| Aerator control | The relay switches on a confirmed low-DO event and respects minimum on/off times and manual override |
| Offline mode | With the network cut, the node still runs the low-DO rule and syncs stored data afterwards |
| Forecast | MAE and RMSE are reported against the moving-average baseline |
| Demo | DO is lowered safely in a test tank (for example with sodium sulfite or an air pump) to trigger the alert and relay live |

**Risks**

- The DO probe is the largest cost, so several DO probes per pond may exceed the budget.
- Probe fouling and drift can cause false alarms.
- Forecast accuracy is unknown until real pond data is collected.
- LoRa range and solar sizing need site testing.

**Open questions**

- How many zones per pond can the budget support?
- Which species and thresholds does local fisheries guidance recommend?
- Which SMS and voice providers will be used, and what registration do they need in India?
- Where will real training data come from?
