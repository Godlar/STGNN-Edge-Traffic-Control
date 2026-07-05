# 🚦 STGNN Edge Traffic Controller: Secure Spatio-Temporal AI

![Python](https://img.shields.io/badge/Python-3.12-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Geometric-EE4C2C)
![Hardware](https://img.shields.io/badge/Hardware-ESP32--S3-black)
![OS](https://img.shields.io/badge/OS-Ubuntu_24.04-E95420)

This repository contains an end-to-end pipeline for optimizing city-wide traffic grids using deep learning. Instead of relying on rigid, localized timing loops, this system uses a **Spatio-Temporal Graph Neural Network (STGNN)** to predict traffic spillover dynamically. 

Crucially, the AI is wrapped in a hardcoded security and state-machine layer designed for physical edge deployment, mitigating critical cyber-physical threats like emergency preemption spoofing.

## 🧠 Core Architecture
The predictive brain replaces standard Convolutional Networks with a dynamic attention-based architecture:
* **Spatial Routing (GATConv):** A multi-head Graph Attention Network dynamically shifts weight to congested nodes, ignoring empty roads and solving the "unobserved node" problem without needing synthetic data injections.
* **Temporal Memory (LSTM):** Processes the spatial output over sequential time-steps to recognize historical traffic rhythms (e.g., daily rush hours).

## 🛡️ Production-Grade Safety & Security
Raw neural networks cannot be trusted to directly drive physical infrastructure. This pipeline features a robust Python state machine that intercepts AI predictions before hardware actuation:

* **MFA for Physics (Anti-Spoofing):** Legacy traffic grids are vulnerable to hackers using $5 infrared strobes to hijack green lights. This system requires cross-verification between a hardware optical pulse counter (e.g., 12Hz) and a local YOLO computer vision classification (e.g., `ambulance`). Mismatches are flagged as spoofing attempts and safely dropped.
* **Automated Environmental Fallback:** If heavy precipitation is detected via physical rain sensors, or if YOLO vision confidence drops due to lens blur, the AI yields to a conservative "Wet Road Profile," extending minimum yellow-light clearance times.
* **Starvation Prevention:** Hardcoded override timers ensure isolated side-street drivers are not permanently trapped by the AI's macro-optimization logic.

## 🔌 Hardware-in-the-Loop (HITL) Setup
The system is designed to run locally, passing optimized serialization packets to edge microcontrollers.

* **Compute Environment:** Ubuntu 24.04 (Noble Numbat)
* **Edge Actuation:** ESP32-S3 receiving payloads via `/dev/ttyUSB0`
* **Data Ingestion:** Simulated integration with OpenCV/YOLO video streams and analog sensor polling.

### Payload Example
The Python control loop generates clean, constrained serial strings mapped to dynamic timing parameters:
```bash
--- Local System Handoff Payload ---
Target Machine Local Port: /dev/ttyUSB0 (ESP32-S3)
Transmitting Command String: G:52
