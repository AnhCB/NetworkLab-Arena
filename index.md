```
---
layout: default
title: "NetworkLab Arena"
description: "A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization"
theme: jekyll-theme-cayman
---

# NetworkLab Arena

### A Multiplayer Aim Trainer for Studying Network Latency and Netcode Synchronization

**Course:** CSC 466: Overlay and Peer-to-Peer Networking (Fall 2026)  
**Author:** Anh Thu Bui (V00998044)  
**Instructor:** Dr. Jianping Pan  
**Institution:** Department of Computer Science, University of Victoria  

---

## 1. Project Overview

**NetworkLab Arena** is an open-source, lightweight 2D experimental testbed built to quantify how network latency, jitter, and packet loss impact hit registration and competitive fairness in real-time multiplayer First-Person Shooter (FPS) games.

* **Tech Stack:** Node.js (60 Hz WebSocket server), HTML5 Canvas (2D client rendering), Linux `tc`/`netem` (network impairment harness).
* **Key Goal:** Measure spatial pixel drift (\\(\Delta px\\)) and server tick jitter (\\(\sigma_{\Delta t}\\)) when comparing **Uncompensated Server Mode** against **Lag-Compensated Rewind Mode**.

---

## 2. System Architecture &amp; Netcode Modes

```

[ HTML5 Canvas Client ] &lt;--- WebSockets (60 Hz) ---&gt; [ Node.js Authoritative Server ] | | Input &amp; Clicks Historical Position Buffer | | +------------------&gt; [ Linux tc/netem ] &lt;---------------+ (Injected Delay/Jitter)

```

### Core Netcode Mechanisms
1. **Uncompensated Mode:** The server evaluates hits at the exact arrival timestamp of the click packet. Under high latency, targets move before the hit packet arrives, causing shots to miss.
2. **Lag-Compensated Rewind Mode:** The server stores a rolling historical position buffer. When a client shoots, the server rewinds target positions back to what the player actually saw on screen at click time.

---

## 3. Quantitative Evaluation Metrics

* **Spatial Pixel Error (\\(\Delta px\\)):** Euclidean distance between client-rendered target position at click time \\((x_c, y_c)\\) and authoritative server position \\((x_s, y_s)\\) upon event processing:
  \[\Delta px = \sqrt{(x_c - x_s)^2 + (y_c - y_s)^2}\]
* **Server Tick Interval (\\(\Delta t\\)):** Elapsed execution time between consecutive server loop iterations (\\(\Delta t = t_n - t_{n-1}\\)).
* **Tick Jitter (\\(\sigma_{\Delta t}\\)):** Standard deviation of server loop tick timing across \\(N\\) sample ticks:
  \[\sigma_{\Delta t} = \sqrt{\frac{1}{N} \sum_{i=1}^{N} (\Delta t_i - \bar{\Delta t})^2}\]

---

## 4. Development Logbook

### **Phase 1: Baseline Server &amp; Client Setup** *(Completed)*
* Implemented the 60 Hz Node.js WebSocket game loop and browser-based 2D HTML5 canvas rendering engine.
* Verified baseline round-trip time (RTT) under 5 ms on localhost.

### **Phase 2: Lag Compensation &amp; Historical Ring Buffer** *(In Progress)*
* Developing a 1-second historical position ring-buffer on the server.
* Adding client-side timestamping to event payloads for backward reconciliation.

### **Phase 3: Network Impairment Harness &amp; Telemetry** *(Upcoming)*
* Configuring Linux `tc`/`netem` shell scripts for controlled delay (10–150 ms), jitter, and packet loss.
* Writing automated CSV logging scripts to measure \\(\Delta px\\) and tick stability across test runs.

### **Phase 4: Data Analysis &amp; Final Deliverables** *(Upcoming)*
* Generating comparative line plots (\\(\Delta px\\) vs. Latency) and tick timing distribution charts.
* Preparing final presentation slides, video demonstration, and project report.

---

## 5. Deliverables &amp; Resources

* 📄 **Project Proposal:** [Download PDF (`networklab-arena-proposal-v13.pdf`)](./networklab-arena-proposal-v13.pdf)
* 💻 **Source Code:** [GitHub Repository](#)
* 📹 **Video Demo:** *(Coming soon)*
```
