# AquaGuard AI – Final Solution

Nov 1, 2026 · Jayashree

## Final proposed solution

AquaGuard AI warns fish farmers hours before a night-time oxygen crash, names the pond and zone at risk, and starts the aerator automatically, but only when the sensor data passes a reliability check. Alerts reach the farmer in Tamil or English by SMS, voice call and app.

Unlike a dashboard that only shows sensor values, it answers four questions: where is the problem, can the reading be trusted, what will happen in the next hours, and what should the farmer do now.

**Positioning line:** a trustworthy, predictive and location-specific decision-support system that moves pond management from reactive monitoring to prevention.

## Problem statement coverage

Every listed feature of Problem 16 (IoT-Based Fish Pond Water Quality Monitoring) is met, and two features go beyond it.

| Problem statement requirement | How AquaGuard AI meets it |
| --- | --- |
| Sensors: temperature, pH, DO, ammonia, turbidity | DS18B20, pH, optical DO and turbidity probes; ammonia from a periodic TAN test plus live pH and temperature, converted to toxic NH3 |
| Species-specific safe limits, instant alerts | Species profiles (Tilapia, Rohu, Catla, Pangasius, Catfish, Shrimp) with warning and critical bands, editable by the farmer |
| Live and historical charts per pond | Per-pond live view and day/week/season history, with aeration and alert events marked |
| Mobile and SMS alerts in local language | Flutter app with push, plus Tamil/English SMS that works on basic phones |
| Automatic aerator or pump control on low oxygen | Relay control of aerator and pump, with safety limits, failsafe on the node and manual override |
| Solar-powered, low-cost LoRa nodes for remote ponds | ESP32 + LoRa nodes on a solar panel and battery, deep sleep, one gateway per farm |
| Predictive night-time oxygen crash alerts using trends and weather | Forecast model using DO trend, temperature, turbidity and weather forecast; alert in the evening, before the dip |
| Calibration reminders and fault detection | Sensor reliability engine plus calibration due-date and drift tracking |
| Voice alerts for low-literacy farmers | Automated Tamil voice call and spoken in-app alert for critical events |
| Multi-pond comparison | Farm overview ranks all ponds by risk, with side-by-side charts; zones inside each pond give a second level |

## How it works

The system runs seven steps, from probe to action, and the safety-critical one also runs on the device when the internet is down.

1. **Sense.** Solar-powered nodes in each pond zone measure DO, pH, temperature and turbidity every few minutes and send them over LoRa to a farm gateway.
2. **Validate.** The reliability engine checks range, sudden jumps, stuck values, physical consistency and agreement with neighbouring zones, and flags each reading as trusted, suspect or faulty.
3. **Compute.** Toxic ammonia (NH3) is calculated from the latest TAN test, pH and temperature.
4. **Score.** Each zone and each pond gets a 0 to 100 risk score against the chosen species profile, with the reason shown (for example, DO falling 0.4 mg/L per hour while temperature rises).
5. **Predict.** A forecast model uses DO trend, temperature, turbidity and the weather forecast to estimate the pre-dawn DO minimum. If it is likely to cross the critical limit, the alert goes out in the evening.
6. **Act.** On a confirmed low-DO condition the node starts the aerator, or the pump, within safety limits. The farmer can override at any time.
7. **Alert.** The farmer gets Tamil/English SMS and app push, and a voice call if the risk is critical and unacknowledged, with a short action card.

## Features

**Core**

- Five-parameter water quality sensing with computed toxic ammonia
- Species-specific safe limits with warning and critical alerts
- Live and historical charts per pond, with event markers
- Tamil/English SMS, app push and voice alerts

**Advanced**

- Automatic aerator and pump control with a node-level failsafe that works offline
- Solar-powered, low-cost LoRa nodes for remote ponds
- Weather-aware prediction of night-time oxygen crashes
- Sensor reliability engine, drift tracking and calibration reminders
- Multi-pond ranking, with zone-level drill-down inside each pond
- Offline buffering: nodes and gateway store data and sync when the network returns
- Farmer-friendly app: coloured pond map, icons, and a plain "what to do now" card

**What sets it apart**

Sensor reliability checking, zone-level risk localisation and a Tamil voice alert path are combined in one system, so the farmer is told where to act and whether to trust the reading.

## Architecture

```
POND                 FARM                    CLOUD                   FARMER
Zone nodes     -->   Gateway          -->    MQTT broker       -->   Flutter app (push)
ESP32 + LoRa         ESP32 / Pi + LoRa       InfluxDB                SMS (Tamil/English)
DO, pH, temp,        + GSM                   Node.js API             Voice call (IVR)
turbidity            offline buffer          Python AI service       Grafana (operators)
Relay: aerator,                              (reliability, risk,
pump (local rule)                            forecast)
        <------------- control commands travel back the same way ------------
```

Data flows from the pond through the gateway to the cloud and out to the farmer; control commands travel back the same way, and the node keeps its own low-oxygen rule so the aerator still works if the link drops.

## Hardware and software

The stack follows the suggested technologies of the problem statement, with a Python service for the AI part.

| Layer | Choice | Role |
| --- | --- | --- |
| Node controller | ESP32 with LoRa module | Reads sensors, runs local safety rules, deep sleeps between readings |
| Sensors | DO, pH, DS18B20 temperature, turbidity | Core water quality readings per zone |
| Ammonia | Periodic TAN test kit entered in the app (optional ion-selective probe) | Input to the toxic NH3 calculation |
| Actuators | Relay module for aerator and pump | Automatic and manual control |
| Power | Solar panel, battery, charge controller | Off-grid operation for remote ponds |
| Gateway | ESP32 or Raspberry Pi with LoRa and GSM | Collects node data, buffers offline, sends SMS |
| Messaging | MQTT | Gateway to cloud transport |
| Time-series storage | InfluxDB | Sensor history and events |
| Backend | Node.js API (pond, user, species profile data) and a Python AI service | Business logic, reliability engine, risk and forecast |
| Machine learning | Scikit-learn gradient boosting; LSTM as an option | DO forecast |
| Dashboards | Grafana for operators, Flutter app for farmers | Charts, alerts, pond map |
| Alerts | SMS gateway, push notifications, voice call (IVR) | Tamil and English notification |
| Weather | Open-Meteo forecast API | Cloud cover, rain and air temperature features |

## AI: reliability, risk and prediction

Three models work in sequence, and each one only sees data that the previous one has cleared.

**1. Sensor reliability engine**

- Range and rate-of-change checks per parameter
- Stuck-value detection (no change over a set time)
- Physical consistency: DO against temperature saturation, and the daily pH and DO swing from photosynthesis
- Zone-to-zone agreement: a reading far from its neighbours is flagged, not trusted
- Drift and fouling tracking, with a calibration due date per probe

**2. Zone and pond risk score**

- Combines DO, pH, temperature, turbidity and computed NH3 against the species profile
- Gives a 0 to 100 score and a green, amber or red band, plus the main cause in one line
- Pond score = worst trusted zone, which feeds the multi-pond ranking

**3. Night-time DO crash forecast**

- Predicts the pre-dawn DO minimum for each zone, from the previous hours of DO, temperature and turbidity trends, time of day, and the weather forecast (cloud cover, rain, air temperature)
- Alerts in the early evening if the minimum is likely to fall below the critical limit, so aeration can start before the dip
- Starting model: gradient boosting, trained on data collected from the pond, with synthetic data used only to test the pipeline
- Reported metrics: forecast error (MAE), alert lead time and false-alarm rate

The forecast needs real pond data to be validated; until enough nights are collected, the system relies on the risk score and thresholds, and says so in the app.

## Species limits and example alert

Each pond is assigned a species profile, and the thresholds switch with it. The values below are indicative starting points and must be confirmed with local fisheries guidance before field use; the farmer can edit them.

| Species profile | DO warning (mg/L) | DO critical (mg/L) | Safe pH | Safe temperature (°C) |
| --- | --- | --- | --- | --- |
| Tilapia | below 4 | below 3 | 6.5 to 8.5 | 26 to 32 |
| Rohu / Catla (carp) | below 4 | below 3 | 6.5 to 8.5 | 25 to 32 |
| Pangasius | below 3 | below 2 | 6.5 to 8.5 | 26 to 32 |
| Shrimp | below 4.5 | below 3.5 | 7.5 to 8.5 | 26 to 32 |

Toxic ammonia (NH3) uses the same profile logic, with a warning band and a critical band set per species.

**Example: three-zone pond, evening forecast**

| Zone | DO now (mg/L) | Forecast pre-dawn DO (mg/L) | Status |
| --- | --- | --- | --- |
| Zone A | 6.2 | 4.8 | Normal |
| Zone B | 5.4 | 3.9 | Warning |
| Zone C | 4.6 | 2.8 | High risk |

SMS (Tamil/English): "Pond 2, Zone C: oxygen likely to drop to 2.8 mg/L before dawn. Aerator started. Check the aerator and the Zone C probe." If the farmer does not acknowledge within a set time, an automated Tamil voice call repeats the message. The sensor in Zone C is first checked by the reliability engine, and the aerator starts only if the reading is trusted and agrees with the trend. The numbers are illustrative.

## Differentiation, feasibility and impact

Existing systems already offer sensors, dashboards, prediction or aeration in various combinations. AquaGuard AI's contribution is to join four things that rarely appear together:

- **Trusted data:** every reading is validated before it drives a prediction or an aerator.
- **Located risk:** the alert names the pond and the zone, not just "the pond".
- **Early, weather-aware warning:** the pre-dawn oxygen dip is forecast the evening before.
- **Reachable farmer:** Tamil SMS and voice calls work without a smartphone or literacy.

**Feasibility**

- Solar LoRa nodes remove the need for Wi-Fi or mains power at the pond.
- The aerator failsafe runs on the node, so protection continues if the network is down.
- The ammonia design avoids a costly continuous probe by using a periodic TAN test.
- Open questions for the team: target cost per pond, and the number of zones per pond for the pilot.

**Validation plan and success metrics**

| Metric | How it is measured |
| --- | --- |
| Sensor accuracy | Probe readings against a reference test kit and lab values |
| Fault detection rate | Injected faults (stuck, spike, drift) in a bench test |
| Forecast error and alert lead time | Pre-dawn DO predicted against measured DO, over collected nights |
| False-alarm rate | Alerts that did not need action, per week |
| Alert delivery | Time from risk detection to SMS and voice call received |
| Fish mortality | Comparison with the farmer's earlier seasons, in a field pilot |

A reduction in fish mortality is a goal that needs a field pilot to confirm, and is not claimed in advance.

## Project abstract

**AquaGuard AI: an intelligent IoT-based fish pond water quality monitoring and predictive risk management system**

Small changes in dissolved oxygen, pH, temperature or ammonia can stress fish or cause mass mortality, often overnight, while manual testing is infrequent. AquaGuard AI is an IoT and AI system that monitors ponds continuously and warns farmers before readings reach dangerous levels.

Solar-powered ESP32 nodes with LoRa measure dissolved oxygen, pH, temperature and turbidity in each pond zone, and toxic ammonia is calculated from a periodic TAN test with live pH and temperature. A sensor reliability engine detects abnormal, stuck and drifting readings and reminds the farmer to calibrate. A risk engine scores every zone and pond against species-specific limits, and a machine learning model uses sensor trends and the weather forecast to predict night-time oxygen crashes in advance.

Alerts reach farmers in Tamil and English through SMS, app notifications and automated voice calls, and a multi-pond view ranks ponds so problem ponds are found first. When low oxygen is confirmed, the system starts the aerator or pump with safety limits, a node-level failsafe and manual override.

The aim is to turn pond monitoring from reactive checking into prevention, so that farmers can act early, lose fewer fish and improve yields.
