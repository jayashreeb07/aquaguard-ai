# AquaGuard AI – Market Requirements Document (MRD)

Nov 1, 2026 · Jayashree

## Market opportunity

Indian pond farmers need an affordable way to catch oxygen and water-quality problems before overnight fish kills. Inland fisheries and aquaculture produced about [15.16 million tonnes in 2024–25, around 77% of India's total fish output](https://weareaquaculture.com/regions/asia/india-celebrates-national-fish-farmers-day-as-output-nears-20-million-tonnes), and [major carps make up about 54% of inland production](https://en.vikaspedia.in/viewcontent/agriculture/agri-directory/handbook-of-fisheries-statistics?lgn=en). The problem statement notes that manual testing is infrequent, so low-oxygen events often go unseen overnight.

AquaGuard AI targets small and medium pond farmers: low-cost solar sensor nodes, early warning of night-time oxygen crashes, and alerts in Tamil by SMS and voice. Initial focus is Tamil Nadu; the design extends to other states by adding languages.

## Target customers

| Segment | Main pain | What decides the purchase |
| --- | --- | --- |
| Small pond farmer (1–5 ponds) | Overnight fish kills; cannot test often; limited tech skill | Low upfront cost, Tamil SMS/voice, works without Wi-Fi |
| Medium farm (6+ ponds) | Cannot tell which pond needs attention first | Multi-pond ranking, aerator automation, reliable alerts |
| Farmer groups and cooperatives | Shared advice and monitoring across members | Per-farm dashboards, shared support |
| Extension officers and fisheries departments | Many farms, little data | Aggregated pond data and alert history |

## Market requirements

| Priority | Requirement | Why the market needs it |
| --- | --- | --- |
| Must | Continuous DO, pH, temperature, turbidity; ammonia via periodic TAN entry | Core water-quality risks; continuous ammonia sensing is costly |
| Must | Species-specific limits and instant alerts | Safe ranges differ by species |
| Must | Tamil/English SMS and voice alerts | Farmers may lack smartphones or reading ease |
| Must | Automatic aerator control with local failsafe and manual override | Protects fish when the network is down |
| Must | Night-time DO crash prediction using trends and weather | Warning before the damage, not after |
| Must | Sensor fault detection and calibration reminders | Wrong readings cause wrong actions |
| Should | Solar-powered LoRa nodes | Remote ponds often lack grid power and Wi-Fi |
| Should | Multi-zone view inside a pond | Locates the problem area |
| Should | Multi-pond comparison and live/historical charts | Shows which pond needs action first |
| Later | Pump control, feed advice, cooperative dashboards | Extends value after the pilot |

Affordability is a constraint on every row: the DO probe is the biggest cost item, so zone coverage must be balanced against price.

## Competition and differentiation

Monitoring, dashboards and aerator automation already exist, for example in NECTEC's Aqua-IoT in Thailand and in published IoT research from India and China, so these features alone are not a differentiator. AquaGuard AI competes by combining four things for small farmers: a sensor reliability check, zone-level risk, evening forecasts of the pre-dawn oxygen dip, and Tamil SMS and voice alerts. Commercial Indian products and their prices still need to be collected and compared (see open questions).

## Success metrics and open questions

**Pilot metrics**

- Alert lead time before DO falls below the critical limit
- False-alarm rate per week
- Sensor fault detection rate in bench tests
- Fish mortality compared with the farmer's earlier seasons (needs a field pilot)
- Prototype cost per pond against a verified competitor quote

**Open questions**

- What will farmers pay for hardware and for any monthly alert fee?
- Which Indian commercial products should be benchmarked on price and features?
- How many zones per pond can the budget support?
- Which species and Tamil Nadu districts should the pilot cover?
- Which species thresholds does local fisheries guidance recommend?
