---
layout: default
title: "NetworkLab Arena"
description: "A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization"
theme: jekyll-theme-cayman
---

# NetworkLab Arena

### A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization

**CSC 466 · Fall 2026 · University of Victoria**

**Author:** Anh Thu Bui (V00998044)  
**Instructor:** Dr. Jianping Pan  
**Department:** Computer Science, University of Victoria

---

## Project at a Glance

NetworkLab Arena is a lightweight, 2D multiplayer experimental testbed for investigating how network conditions affect hit registration and fairness in real-time shooter games.

| Component | Description |
|:--|:--|
| Client | HTML5 Canvas |
| Server | Node.js and WebSockets |
| Target update rate | 60 Hz |
| Network testing | Linux `tc` / `netem` |
| Main comparison | Uncompensated vs. lag-compensated hit registration |
| Current focus | Historical position buffer and rewind logic |

**Research question:** How do latency, jitter, and packet loss affect hit registration, and how effectively can server-side lag compensation reduce positional disagreement?

## 1. Project Overview

In competitive multiplayer shooters, the position displayed on a player's screen may differ from the position maintained by the authoritative server. Network delay can cause a shot to arrive after its target has moved, even when the player aimed correctly at the position they observed.

NetworkLab Arena investigates this problem through two hit-registration modes under controlled network conditions.

### Project objectives

- Implement a browser-based 2D multiplayer aiming environment.
- Compare uncompensated hit registration with server-side historical rewind.
- Introduce controlled network delay, jitter, and packet loss.
- Measure spatial error, server tick timing, and hit-registration outcomes.
- Analyze how different network conditions influence consistency and fairness.

### Technology stack

`Node.js` · `WebSockets` · `HTML5 Canvas` · `Linux tc/netem` · `CSV` · `Python`

---

## 2. System Architecture

The client handles player input and rendering, while the authoritative server maintains game state and determines hit-registration outcomes. A historical position buffer supports lag-compensated evaluation.

### Architecture diagram

```text
             PLAYER
               |
               v
    +-----------------------+
    | Browser Game Client   |
    |                       |
    | HTML5 Canvas          |
    | Aim, movement, clicks |
    +-----------+-----------+
                |
                | WebSocket messages
                | Input and shot events
                v
    +-----------------------+
    | Authoritative Server  |
    |                       |
    | Node.js               |
    | Target update: 60 Hz  |
    | Hit registration      |
    +-----------+-----------+
                |
                v
    +-----------------------+
    | Historical Position   |
    | Buffer                |
    |                       |
    | Timestamped states    |
    | Rewind lookup         |
    +-----------------------+

    Network test environment
    +-----------------------+
    | Linux tc / netem       |
    |                       |
    | Delay, jitter, loss    |
    +-----------------------+
```

The network impairment harness is a test environment, not a separate game-processing component. Its effect depends on the network interface and traffic path to which the rules are applied.

### Netcode mode A — Uncompensated

The server evaluates a shot against the target's current authoritative position when the event is processed.

If the target has moved since the player observed it, the server may reject a shot that appeared accurate on the client.

### Netcode mode B — Lag-compensated rewind

The server stores a rolling history of target positions. When a shot arrives, it estimates the relevant historical server time and evaluates the shot against the target's position at that time.

The rewind duration must be bounded by the available history and validated against implausible timestamps. Correct rewind timing also requires a consistent relationship between client timestamps, server time, and estimated network delay.

### Experimental comparison

Both modes will be evaluated under the same controlled network conditions. This makes it possible to investigate whether rewind reduces positional disagreement and improves hit-registration consistency.

---

## 3. Quantitative Evaluation

The experiment focuses on three primary measurements.

### 3.1 Spatial pixel error

Let the target position displayed by the client at click time be \((x_c,y_c)\), and let the corresponding authoritative target position at server evaluation time be \((x_s,y_s)\).

The Euclidean distance is:

$$
\Delta p =
\sqrt{(x_c-x_s)^2+(y_c-y_s)^2}
$$

When both positions use the same screen-coordinate system, this distance can be reported as spatial pixel error, \(\Delta px\).

**Interpretation:** A smaller error indicates closer agreement between the two positions. However, positional error alone does not determine whether a shot hits; the target hitbox and collision rules must also be considered.

### 3.2 Server tick interval

The interval between consecutive server-loop iterations is:

$$
\Delta t_i = t_i-t_{i-1}
$$

At a target update rate of 60 Hz, the nominal interval is:

$$
\Delta t_{\text{target}} =
\frac{1000}{60}
\approx 16.67\text{ ms}
$$

The actual interval may vary because of event-loop scheduling and processing load.

### 3.3 Server tick jitter

Tick jitter is measured using the population standard deviation of the observed tick intervals:

$$
\sigma_{\Delta t} =
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}
(\Delta t_i-\overline{\Delta t})^2
}
$$

where:

- \(N\) is the number of measured intervals.
- \(\Delta t_i\) is an observed interval.
- \(\overline{\Delta t}\) is the mean interval.

Lower standard deviation indicates more consistent timing, but it does not guarantee that the server meets its target rate. The mean interval and delayed or missed ticks should be reported as well.

### 3.4 Experimental variables

| Variable | Planned conditions |
|:--|:--|
| Network delay | Added delay from 10 to 150 ms |
| Jitter | Controlled variation in packet delay |
| Packet loss | Configurable loss rates |
| Netcode mode | Uncompensated / rewind |
| Measurements | Spatial error, hit outcomes, tick timing |

Exact test values and repeated runs will be documented during experimentation.

### 3.5 Data collection

Each test run should record:

- Configured delay, jitter, and packet-loss rate.
- Netcode mode and test-run identifier.
- Shot timestamp and server evaluation time.
- Client and server target positions.
- Spatial error and hit-registration outcome.
- Server tick intervals and delayed ticks.

Results will be exported to CSV for analysis and visualization.

---

## 4. Development Logbook

### Phase 1 — Baseline server and client setup

**Status: Completed**

- Implemented the Node.js WebSocket game loop targeting 60 Hz.
- Implemented browser-based 2D rendering using HTML5 Canvas.
- Measured baseline round-trip time below 5 ms on localhost.

### Phase 2 — Lag compensation and historical ring buffer

**Status: In progress**

- Developing a one-second historical position buffer.
- Adding client-side timestamps to shot-event payloads.
- Implementing historical position lookup and rewind validation.

### Phase 3 — Network impairment and telemetry

**Status: Upcoming**

- Configure Linux `tc` / `netem` for controlled delay, jitter, and packet loss.
- Implement automated CSV logging.
- Validate repeatability of network test scenarios.

### Phase 4 — Data analysis and final deliverables

**Status: Upcoming**

- Generate spatial error versus latency plots.
- Visualize server tick intervals and timing distributions.
- Compare hit-registration outcomes across both modes.
- Prepare the final report, presentation, and demonstration video.

---

## 5. Deliverables and Resources

- **Project proposal:** [Download project-proposal.pdf](./project-proposal.pdf)
- **Source code:** [View the GitHub repository](https://github.com/)
- **Video demonstration:** Coming soon.
