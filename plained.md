# CAPI Control API

## Overview

CAPI Control API is a developer-first industrial connectivity platform that automatically converts PLCs and industrial devices into modern APIs.

Instead of learning Modbus, OPC UA, EtherNet/IP, and other industrial protocols, developers can connect machines and immediately access data through REST APIs, gRPC, WebSockets, and SDKs.

Our goal is to make industrial automation as easy to integrate as modern cloud services.

---

## Vision

Building software for industrial equipment is still unnecessarily complex.

Traditional industrial systems require developers to:

* Learn PLC protocols
* Configure drivers
* Understand industrial networking
* Write custom communication layers
* Maintain protocol-specific code

CAPI removes this complexity.

A developer should be able to connect a PLC and start building applications within minutes.

---

## How It Works

```text
PLC
 ↓
CAPI Edge
 ↓
CAPI Cloud
 ↓
Generated APIs
 ↓
Web Apps, Mobile Apps, Dashboards
```

---

## Core Components

### CAPI Edge

A lightweight edge agent installed inside a factory network.

Responsibilities:

* PLC discovery
* Tag discovery
* Data collection
* Command execution
* Local buffering
* Secure cloud synchronization
* Offline operation

Supported deployment targets:

* Raspberry Pi
* Industrial PCs
* Linux Servers
* Docker Containers

---

### CAPI Cloud

Centralized platform responsible for:

* Device management
* Factory management
* User management
* Authentication
* Historical data storage
* Alerting
* Analytics

---

### Auto Generated APIs

When a machine is connected, CAPI automatically generates APIs.

Example:

Machine Tags:

```text
Temperature
Speed
Start
Stop
```

Generated API:

```http
GET /machines/mixer/temperature
GET /machines/mixer/speed

POST /machines/mixer/start
POST /machines/mixer/stop
```

No manual driver development required.

---

## Supported Protocols

Current and planned protocol support:

* Modbus TCP
* Modbus RTU
* OPC UA
* EtherNet/IP
* MQTT
* BACnet
* Profinet (planned)
* CAN Bus (planned)

---

## Developer SDKs

### JavaScript

```javascript
const machine = await capi.machine("mixer");

await machine.start();

const temperature = await machine.temperature();
```

### Python

```python
machine = capi.machine("mixer")

machine.start()

temperature = machine.temperature()
```

---

## Real-Time Events

CAPI provides machine events in real time.

Examples:

* Machine Started
* Machine Stopped
* Machine Fault
* PLC Offline
* High Temperature
* Emergency Stop

Example:

```javascript
machine.on("fault", (event) => {
    console.log(event);
});
```

---

## Machine Monitoring

Monitor:

* Machine status
* Production counts
* PLC connectivity
* Fault history
* Downtime
* Uptime
* Energy consumption

Example Dashboard:

```text
Factory A

Line 1
🟢 Running

Line 2
🔴 Fault

Line 3
🟡 Maintenance
```

---

## Downtime Tracking

CAPI automatically tracks:

* Planned downtime
* Unplanned downtime
* Fault downtime
* Maintenance downtime
* Material shortages

Metrics:

* Availability
* OEE
* MTBF
* MTTR

---

## Security

Security features include:

* TLS Encryption
* Device Authentication
* API Keys
* Role-Based Access Control (RBAC)
* Audit Logs
* Secure Edge-to-Cloud Communication

---

## Multi-Factory Architecture

```text
Factory A
 ├─ Line 1
 ├─ Line 2
 └─ Line 3

Factory B
 ├─ Line 1
 └─ Line 2

Factory C
 └─ Line 1
```

Each factory operates independently while being managed from a centralized platform.

---

## Example Use Cases

### Manufacturing

* Production monitoring
* OEE tracking
* Downtime analytics

### Utilities

* Water treatment monitoring
* Pump station monitoring
* Remote asset management

### Agriculture

* Irrigation systems
* Greenhouse automation
* Livestock monitoring

### Energy

* Generator monitoring
* Solar plant monitoring
* Power distribution analytics

### Smart Buildings

* HVAC monitoring
* Energy management
* Building automation

---

## Deployment Flow

```text
Install CAPI Edge
 ↓
Connect PLC
 ↓
Auto Discover Tags
 ↓
Generate APIs
 ↓
Connect Application
 ↓
Monitor and Control Equipment
```

Typical setup time: less than 10 minutes.

---

## Long-Term Goal

CAPI aims to become the standard API layer for industrial machines.

Just as developers use cloud services without understanding database internals, CAPI enables developers to build industrial software without learning industrial communication protocols.

### Mission

"Connect Any Machine. Build Any Application."
