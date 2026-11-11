# AquaGuard AI

**Sector 4: Fish Farming Management · Problem 16: IoT-Based Fish Pond Water Quality Monitoring**

AquaGuard AI warns fish farmers before a night-time oxygen crash, names the pond zone at risk, and flags sensor readings that cannot be trusted. Alerts are designed for Tamil and English farmers (SMS, voice, app).

This repository holds the complete project: solution, market and product requirements, backlog, and a working prototype.

## Repository contents

| Path | What it is |
| --- | --- |
| [`docs/solution.md`](docs/solution.md) | Final solution: features, architecture, AI design, species limits, abstract |
| [`docs/MRD.md`](docs/MRD.md) | Market Requirements Document (one page) |
| [`docs/PRD.md`](docs/PRD.md) | Product Requirements Document with epics and story summary |
| [`docs/epics-and-user-stories.md`](docs/epics-and-user-stories.md) | 5 epics, 15 user stories with Given-When-Then criteria, MoSCoW, prototype status |
| [`prototype/index.html`](prototype/index.html) | Working single-page dashboard prototype (simulated data) |

## The prototype

- Three pond zones with DO, pH, temperature and turbidity, a status (Normal, Warning, Critical) and a reliability badge.
- Every zone is labelled "Simulated". No real sensor data is used.
- Detects impossible values, stuck sensors and sudden jumps, and excludes unreliable readings from risk decisions.
- Warns when the DO trend line would reach the critical limit within 60 minutes, and names the zone.
- Up to two suggested next steps per alert; it only recommends and controls no equipment.
- English and Tamil; built-in scenario tests (normal night, faulty sensors, falling DO).

**Run it:** open `prototype/index.html` in a browser. No build or server needed.

**Demo:** pick a species, choose a zone, press *Freeze DO sensor* or *Spike pH reading*, press *Start DO decline* on another zone, then *Farmer fixes the issue*. Switch language with the buttons.

## Honesty notes

- The early warning is rule-based trend detection, not a trained AI model; no accuracy claim is made.
- Scenario results come from simulated data only.
- Species limits are indicative and must be confirmed with local fisheries guidance.
- Tamil wording needs review by a native speaker.
- The docs describe a fuller system (LoRa/solar nodes, aerator control, SMS/voice, ML forecast, multi-pond, ammonia, Kerala/Goa extension: salinity, Malayalam, Konkani). The prototype covers only the three Must-Have features of the 1.5-day backlog. See `docs/PRD.md` for the scope differences.

## Next steps

ESP32 node feeding one zone as "Live sensor", a Python backend, forecast validation on real pond data, and native review of the language wording.
