# AquaGuard AI

A prototype dashboard for IoT-based fish pond water quality monitoring and early risk detection. Built for Problem 16 (Sector 4, Fish Farming Management).

Small changes in dissolved oxygen (DO), pH or temperature can stress fish or cause mass mortality, often overnight. AquaGuard AI watches several pond zones, flags unreliable sensor readings, and warns early when DO is falling toward a low limit.

## What the prototype does

- Shows three pond zones with DO, pH, temperature and turbidity, a status (Normal, Warning, Critical) and a reliability badge for each.
- Labels every zone as "Simulated". No real sensor data is used.
- Detects impossible values, stuck sensors and sudden jumps, and excludes unreliable readings from risk decisions.
- Warns when the DO trend line would reach the critical limit within 60 minutes, and names the affected zone.
- Gives up to two suggested next steps with each alert. It only recommends and does not control equipment.
- Switches between English and Tamil.
- Runs built-in scenario tests (normal night, faulty sensors, falling DO) and shows expected against actual results.

## Run it

Open `index.html` in a browser. No build step or server is needed.

## Try the demo

1. Pick a species profile.
2. Choose a zone, then press **Freeze DO sensor** or **Spike pH reading** to see the reliability badge and alert.
3. Press **Start DO decline** on another zone to see the early warning.
4. Press **Farmer fixes the issue** to recover.
5. Switch to Tamil with the language buttons.

## Limits and honesty notes

- The early warning is rule-based trend detection, not a trained AI model. No accuracy claim is made.
- Scenario results come from simulated data only.
- Species limits are indicative and must be confirmed with local fisheries guidance.
- Tamil wording needs review by a Tamil speaker.
- Not included yet: aerator relay control, SMS and voice alerts, ammonia sensing, LoRa and solar nodes, multi-pond view.

## Planned next steps

ESP32 sensor node feeding one zone as "Live sensor", a Python backend, and validation of the forecast on real pond data.
