---
layout: default
title: "NetworkLab Arena"
description: "A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization"
theme: jekyll-theme-cayman
---

# NetworkLab Arena
### A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization

**Anh Thu Bui (V00998044)** · CSC 466: Overlay and Peer-to-Peer Networking · University of Victoria · Fall 2026

> A lightweight experimental testbed for comparing uncompensated hit validation with lag-compensated rewind under controlled network conditions.

[**Open the HTML5 prototype**](./networklab-arena/index.html) · [View the project logbook](./logbook.md)

---

## Project Overview

Competitive online games depend on timely synchronization between a player's view and the authoritative server state. Network delay, jitter, and packet loss can cause a shot that appears accurate on the client to be rejected by the server.

NetworkLab Arena is designed to study this issue using a small 2D aim trainer. The planned experiment compares two server-side validation approaches:

- **Uncompensated server mode:** validate a shot against the target's position when the shot event reaches the server.
- **Lag-compensated rewind mode:** use a rolling history of target positions to validate the shot against the position corresponding to the client's click timestamp.

The project focuses on two measurements: spatial pixel error, \(\Delta px\), and server tick jitter, \(\sigma_{\Delta t}\).

## Current Status

**Current milestone: browser-based prototype created.**

The HTML5 Canvas prototype currently includes:

- A moving target and click-to-shoot interaction.
- Selectable uncompensated and rewind demonstration modes.
- Browser-side controls for simulated one-way delay (0–150 ms), jitter (0–30 ms), and packet loss (0–10%).
- A local one-second target-position history used by the rewind demonstration.
- Live counters for shots, registered hits, mean spatial error, and measured browser-loop timing variation.
- A recent-shot table and CSV export.

**Important scope note:** these features currently run inside one browser. The prototype does **not** yet constitute a connected multiplayer client/server implementation, and its controls do **not** configure Linux `tc/netem`. The displayed values are prototype-level simulated measurements, not results from the planned real network experiments.

## Architecture and Experimental Plan

```text
HTML5 Canvas client
  └─ renders moving target and records click timestamp
              │
              ▼
Node.js authoritative server (planned integration)
  ├─ 60 Hz update loop (target interval ≈ 16.67 ms)
  ├─ current authoritative target state
  ├─ one-second historical position ring buffer
  └─ shot validation: uncompensated vs. rewind
              │
              ▼
Linux tc/netem test harness (planned)
  └─ delay, jitter, and packet-loss profiles
              │
              ▼
Telemetry and analysis
  ├─ spatial pixel error (Δpx)
  ├─ tick interval and tick jitter (σΔt)
  └─ comparative plots and findings
```

## Evaluation Metrics

### Spatial pixel error

The Euclidean distance between the click position and the server's target position used for validation:

\[
\Delta px = \sqrt{(x_c-x_s)^2 + (y_c-y_s)^2}
\]

### Server tick interval and jitter

The tick interval is the elapsed time between consecutive server-loop iterations:

\[
\Delta t_i = t_i-t_{i-1}
\]

Tick jitter is the standard deviation of the measured intervals:

\[
\sigma_{\Delta t} = \sqrt{\frac{1}{N}\sum_{i=1}^{N}(\Delta t_i-\overline{\Delta t})^2}
\]

The target server rate is 60 Hz, corresponding to approximately 16.67 ms per tick.

## Logbook

See [the project logbook](./logbook.md) for milestone history, implementation progress, limitations, and next steps.

## Roadmap

| Phase | Work | Status |
|---|---|---|
| 1 | Proposal, research question, and initial design | Completed |
| 2 | HTML5 Canvas prototype and browser-side demonstration of two modes | Prototype created; requires further validation |
| 3 | Node.js/WebSocket authoritative server and timestamped shot events | Planned / integration work remains |
| 4 | Linux `tc/netem` experiments, repeated trials, and data collection | Planned |
| 5 | Comparative plots, analysis, and final recommendations | Planned |

## How to Run the Prototype

1. Download or clone the project repository.
2. Open `networklab-arena/index.html` in a modern browser.
3. Press **Start / Resume**.
4. Choose a netcode mode and adjust the simulated network settings.
5. Click the moving target and observe the measurements.
6. Export the recent shot events as CSV.

The prototype is useful for demonstrating the intended interface and experiment concept. Do not interpret its browser-generated measurements as evidence from real `tc/netem` runs.

## References

- F. Author et al., “Adaptive time delay for improving player experience and fairness in first-person shooter games with network latency,” *Proceedings of the 21st International Conference on Foundations of Digital Games (FDG)*, 2021.
- J. Jardine and D. Zappala, “A hybrid architecture for massively multiplayer online games,” *Proceedings of the 7th ACM SIGCOMM Workshop on Network and System Support for Games (NetGames)*, 2008, pp. 60–65.
- Y. Bernier, “Latency compensating methods in client/server in-game protocol design and optimization,” Valve Developer Community, 2001.

---

*Project status last updated: October 28, 2026.*

