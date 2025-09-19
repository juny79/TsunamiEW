# TsunamiEW : Tsunami Early Warning

# 🌊 Tsunami Early Warning — Anomaly Detection MLOps

## 1. Project Overview
- **Purpose**: Detect potential tsunamis triggered by earthquakes in Japanese waters and issue early warnings for the Korean east coast (≤60s).
- **Background**: For Korea, tsunamis from Japan’s large offshore earthquakes pose greater risks than domestic earthquakes.
- **KPI**:
  - Recall ≥ 0.9 (catch actual tsunamis)
  - Precision ≥ 0.8 (reduce false alarms)
  - Detection latency ≤ 60 seconds
  - System availability ≥ 99.5%

---

## 2. Threat Scenario
- Offshore Japan (Japan Sea / Pacific) earthquake, magnitude ≥ 7.0
- Tsunami generated within minutes → ETA to Korean east coast: 30 min ~ 2 hr
- Failure to detect → massive damage to coastal cities, ports, power plants

---

## 3. Data Sources & Schema
**Sources**
- [USGS Earthquake API](https://earthquake.usgs.gov/fdsnws/event/1/)
- [JMA (Japan Meteorological Agency)](https://www.jma.go.jp/)
- [KMA (Korea Meteorological Administration)](https://www.kma.go.kr/)
- Buoy / NOAA / Copernicus ocean observation

**Unified Schema (draft)**

| Column          | Type      | Description                       |
|-----------------|-----------|-----------------------------------|
| event_id        | TEXT (PK) | Unique event ID                   |
| event_time_utc  | TIMESTAMP | Event time                        |
| updated_time_utc| TIMESTAMP | Last update time                  |
| latitude        | DOUBLE    | Epicenter latitude                |
| longitude       | DOUBLE    | Epicenter longitude               |
| depth_km        | DOUBLE    | Depth (km)                        |
| magnitude       | DOUBLE    | Earthquake magnitude              |
| place           | TEXT      | Location description              |
| event_type      | TEXT      | earthquake / tsunami              |
| source          | TEXT      | USGS / JMA / KMA                  |
| ingested_at_utc | TIMESTAMP | Data ingestion time               |
| raw             | JSONB     | Raw payload for audit/debug       |

---

## 4. Architecture

```mermaid
flowchart TD
    A[Collectors] -->|USGS/JMA/KMA/Buoy| B[Normalizer & Feature Store]
    B --> C[Detection Models]
    C -->|Rules + ML| D[Alert System]
    D --> E[Slack/SMS/Web Alerts]
    D --> F[Dashboard: ETA Visualization]

