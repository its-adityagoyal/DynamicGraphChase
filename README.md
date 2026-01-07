# 🏛️ Savior of the Louvre  
### Dynamic Graph-Based Police–Thief Chase Simulation

A real-time pursuit–evasion simulator built on a **dynamic directed weighted graph**, inspired by a daytime art heist at the Louvre Museum, Paris.  
This project demonstrates **Dijkstra’s algorithm**, **dynamic path replanning**, and **event-driven graph updates** in an interactive chase between burglars and police.

---

## 🚨 Scenario Overview

A group of burglars (🚗 **Car A**) escapes from the Louvre and races toward an exit through the streets of Paris.  
The Paris Police (🚓 **Car B**) respond after a short delay and attempt to intercept the burglars before they escape.

The city is modeled as a **directed weighted graph**, where:
- **Nodes** → Intersections  
- **Edges** → Roads  
- **Edge weights** → Distance + traffic cost  

The environment is **dynamic**, with random traffic jams, road blockages, and one-way restrictions that force both vehicles to **recompute optimal paths in real time**.

---

## 🎯 Objectives

- Implement **Dijkstra’s Algorithm from scratch**
- Simulate **two autonomous agents** with different goals and information
- Handle **dynamic graph changes**
- Log the complete simulation state at every timestep
- (Optional) Visualize the chase using `networkx` and `matplotlib`

---

## 🧠 Core Features

### 1️⃣ Graph Representation
- Loaded from `graph_with_metadata.json`
- Directed weighted adjacency list
- Node positions for visualization
- Exit nodes embedded in metadata

### 2️⃣ Path Planning (Dijkstra)
- Implemented **without external shortest-path libraries**
- Supports multiple target nodes
- Recomputed only when required (event-driven)

### 3️⃣ Two-Car Simulation

| Property | Car A (Burglars) | Car B (Police) |
|--------|-----------------|---------------|
| Start Node | `0` | `49` |
| Start Time | Immediately | After 3 timesteps |
| Speed | `1.0×` | `1.5×` |
| Goal | Reach exit | Catch Car A |
| Knowledge | Graph + exits | Graph + Car A position |

---

## ⚡ Dynamic Events

At each timestep, with probability **p = 0.3**, one of the following may occur:

| Event Type | Effect | Duration |
|----------|-------|----------|
| 🚧 Traffic Jam | Edge weight × (2.0–2.5) | 3 steps |
| ⛔ Blockage | Edge removed | 4 steps |
| ➡️ One-Way | Reverse edge disabled | 6 steps |

All events automatically **revert** once expired.

---

## 🔄 Dynamic Replanning Rules

### 🚗 Car A (Burglars)
- Computes shortest path to nearest exit
- Recomputes **only when an event occurs**
- If mid-edge, finishes edge before replanning
- Stops once an exit is reached

### 🚓 Car B (Police)
- Starts after a delay of 3 timesteps
- Always knows Car A’s exact position
- Recomputes path:
  - After every edge traversal
  - After any dynamic event
- Targets the node Car A is moving toward if mid-edge

---

## 🏁 Win Conditions

- **Car A wins** 🏆 if it reaches an exit first
- **Car B wins** 🚔 if:
  - Both cars are on the same node, or
  - Both are on the same edge with distance < `0.5`
- Simulation stops after **50 timesteps** if unresolved

---

## 📁 Project Structure
├── graph_with_metadata.json # City graph (nodes, edges, metadata)
├── simulation.json # Full timestep-by-timestep simulation log
├── simulation_log.json # Sample / debug logs
├── 23EC30067.ipynb # Main implementation notebook
├── README.md # Project documentation


---

## 🧾 Simulation Log Format

```json
{
  "step": 12,
  "carA": {
    "pos": 38,
    "edge_from": 38,
    "edge_to": 48,
    "progress": 1.0,
    "Dijkstra_path": [48]
  },
  "carB": {
    "pos": 14,
    "edge_from": 14,
    "edge_to": 48,
    "progress": 0.0,
    "Dijkstra_path": [48]
  },
  "caught": false,
  "reached": false,
  "log_events": [
    {
      "type": "One-way",
      "edge": [33, 32],
      "steps": 6
    }
  ]
}

