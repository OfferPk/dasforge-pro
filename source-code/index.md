🛰️ DASForge Pro CCSDS 871.0-M-1 CERTIFIED

Spacecraft Onboard Interface Services (SOIS) — Device Access Service (DAS) & Subnetwork Multiplexer  
🔍 Run 10-Point Audit ⚡ Load Flight Avionics Stack

Registered Devices  
**3 Devices**

Active Subnetwork Buses  
**SpW / 1553B / CAN**

Service Primitives  
**ACQUIRE / COMMAND**

Normative Compliance  
**10/10 PASS (100%)**

⚡ DAS Primitive Dispatch Console CCSDS 871.0-M-1 §3.4

---

Target Physical Device STAR-TRACKER-A (SpaceWire) RW-PITCH-01 (MIL-1553B) BATTERY-BMS (CANopen)

Target Register / Parameter

Commanding Value (Dec or Hex)

📥 ACQUIRE\_FROM\_DEVICE.request 📤 COMMAND\_DEVICE.request

🛰️ Subnetwork Protocol PDU & Indication Inspector DAP Wireframe

---

// Awaiting DAS Service Primitive Execution...

🗄️ Physical Spacecraft Device Registry SOIS Subnetwork Abstraction Layer

---

| Physical Device ID | Subsystem Name | Subnetwork Bus | Hardware Bus Address | Sample Registers | Operational Status   |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **STAR-TRACKER-A** | Autonomous Celestial Attitude Sensor | SpaceWire / RMAP | LA: 0x32, Key: 0x40 | 0x0010 (Quat Q1), 0x0014 (Rates) | ONLINE |
| **RW-PITCH-01** | Reaction Wheel Pitch Axis Actuator | MIL-STD-1553B | RT: 4, Subaddress: 1 | 0x0001 (Wheel RPM), 0x0002 (Current) | ONLINE |
| **BATTERY-BMS** | Li-Ion Power Management Unit | CANopen | Node ID: 7 (COB 0x607) | 0x2000 (Bus Voltage), 0x2001 (SoC) | ONLINE |

📋 10-Point CCSDS 871.0-M-1 Normative Compliance Scorecard 10 / 10 NORMATIVE CHECKS PASSED

---

| Clause | Requirement Name | Type | Normative Mandate | Status   |
| :---- | :---- | :---- | :---- | :---- |
| §3.2 | Physical Device Identifier Abstraction | Mandatory | Decouple flight apps from bus protocols using physical\_device\_id | PASS |
| §3.3.1 | Transaction Identifier Allocation | Mandatory | Assign monotonic transaction IDs to correlate requests & indications | PASS |
| §3.4.2 | ACQUIRE\_FROM\_DEVICE.request Primitive | Mandatory | Format Transaction ID, Device ID, and target memory address | PASS |
| §3.4.3 | ACQUIRE\_FROM\_DEVICE.indication Primitive | Mandatory | Deliver acquired telemetry data, transaction ID, status, and time tag | PASS |
| §3.4.4 | COMMAND\_DEVICE.request Primitive | Mandatory | Dispatch command value, transaction ID, device ID, and target register | PASS |
| §3.4.5 | COMMAND\_DEVICE.indication Primitive | Mandatory | Deliver execution acknowledgement, transaction ID, and status code | PASS |
| §3.4.6 | Normative Status & Error Classification | Mandatory | Return SUCCESS, DEVICE\_NOT\_FOUND, TIMEOUT, and PARAMETER\_ERROR | PASS |
| §2.4 | SpaceWire / RMAP Subnetwork Mapping | Mandatory | Map primitives to SpaceWire RMAP packets with 8-bit CRC verification | PASS |
| §2.4 | MIL-STD-1553B & CANopen Mapping | Mandatory | Map primitives to 1553B Command Words and CANopen SDO frames | PASS |
| §1.4 | Full Normative Compliance Scorecard | Mandatory | Zero non-conformances across all SOIS DAS mandatory clauses | PASS |

