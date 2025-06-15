# cyclonedds-discovery-simulation

A distributed sensor communication demo built using **Eclipse Cyclone DDS**, implemented in **C++**.
Each node functions as both a **publisher** (emitting synthetic sensor readings) and a **subscriber** (receiving others' messages), creating a decentralized publish-subscribe mesh.

**Eclipse Cyclone DDS** is a high-performance, open-source implementation of the **Data Distribution Service (DDS)** standard — designed for scalable, real-time, and fault-tolerant communication in distributed systems such as robotics, IoT, and industrial automation.

The primary goal is to simulate **how DDS handles dynamic node participation**, particularly focusing on the behavior of **late-joining participants** — i.e., nodes that join an already active DDS network.
This provides insight into **discovery propagation**, **data availability**, and **communication stability** in real-time distributed systems.

---

## Core Concepts

* **Data-Centric Pub-Sub:** All nodes communicate through a shared DDS topic (`SensorTopic`) without point-to-point configuration.
* **Peer Discovery:** Eclipse Cyclone DDS automatically discovers other participants using multicast or shared configurations.
* **Late Join Simulation:** One node (`node4`) deliberately joins after others are already publishing and subscribing, testing how well discovery and integration work in practice.
* **Resilient Messaging:** DDS ensures reliable delivery using `RELIABLE` QoS and bounded history (`KEEP_LAST`), helping new nodes catch up with ongoing traffic.
* **Per-Node Logging:** Each node records its own publish/receive events into a dedicated log file (`/logs/<node>.log`), making interaction patterns traceable.

---

## Simulation Flow

1. Nodes node1, node2, and node3 start at the same time and immediately begin publishing and subscribing to sensor data.

2. Each node publishes synthetic temperature and humidity readings every 2 seconds.

3. For the first 10 seconds, these three nodes communicate exclusively with each other, exchanging messages seamlessly.

4. After a 10-second delay, node4 joins the network, simulating a late joiner.

5. Thanks to DDS dynamic discovery, node4 is integrated automatically and begins both publishing its own messages and receiving data from the existing nodes.

6. All nodes are designed to ignore their own messages and only log those published by other participants.

7. Throughout the simulation, no disruption, delay, or message loss is observed — the system remains stable and synchronized even after the late join.

---

## Folder Structure

```
├── node/                  # Application source code (main.cpp, CMakeLists.txt, Dockerfile)
│
├── shared/                # Shared configuration and IDL files
│   ├── SensorData.idl     # IDL definition for SensorData type
│   └── cyclonedds.xml     # Cyclone DDS participant discovery config
│
├── logs/                  # Mounted directory where each node writes its own log file
│
├── docker-compose.yml     # Defines and orchestrates DDS nodes as containers
├── Makefile               # Provides helper commands (up, clean, logs)

```
---

## How to Build

Make sure Docker and Docker Compose are installed.

```bash
make up
```

This command:

* Builds all Docker images
* Launches the four DDS nodes
* Ensures shared logs and config files are mounted

---

## How to Clean Up

```bash
make clean
```

Removes all running containers and associated volumes.

---

## View Logs

To inspect logs from a specific node:

```bash
make logs NODE=node2
```

This shows messages published and received by `node2`.

Log files are also persisted to the host under:

```
./logs/node1.log
./logs/node2.log
...
```

---
