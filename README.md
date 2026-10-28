# DASForge Pro (v1.0.0)

### Spacecraft Onboard Interface Services (SOIS) — Device Access Service (DAS) & Subnetwork Multiplexing Studio

**Normative Reference Standard**: [CCSDS 871.0-M-1 (Magenta Book, Issue 1, March 2013\)](https://ccsds.org/Pubs/871x0m1s.pdf)  
**Industry Baselines**: ECSS-E-ST-50-52C (SpaceWire RMAP), MIL-STD-1553B, CiA 301 (CANopen), and [SANA Space Registry](https://sanaregistry.org/)  
**Certification Verdict**: `CCSDS_871_0_DAS_CERTIFIED` (10/10 Normative Mandates Passed — 100.0%)

---

## 1\. Product Overview

DASForge Pro is an aerospace-grade software engine, multi-subcommand Python 3 CLI suite, and glass-cockpit flight avionics operations console implementing the official Consultative Committee for Space Data Systems (CCSDS) Recommended Practice for Spacecraft Onboard Interface Services (SOIS) Device Access Service (CCSDS 871.0-M-1).

Spacecraft flight software applications (ADCS guidance, power management, thermal control, payload instruments) frequently suffer from tight, brittle coupling to physical subnetwork buses. When an avionics sensor or actuator is replaced (e.g., swapping a SpaceWire star tracker for a MIL-STD-1553B model or CANopen unit), mission software typically requires extensive, high-risk rewrites. DASForge Pro operationalizes the SOIS Device Access Service (DAS), establishing a hardware-independent service layer that isolates flight software applications from physical subnetwork details.

---

## 2\. Core Capabilities & Architectural Pillars

### 2.1 Physical Device Identifier Abstraction (§3.2)

- Flight software entities access onboard instruments strictly using logical Physical Device Identifiers (`physical_device_id`, such as `STAR-TRACKER-A`, `RW-PITCH-01`, `BATTERY-BMS`).  
- Decouples application software from physical subnetwork addresses (SpaceWire logical addresses, 1553B Remote Terminal subaddresses, CAN node IDs).  
- Supports dynamic rerouting and physical device replacement without modifying flight application code.

### 2.2 Monotonic Transaction Correlation (§3.3.1)

- Allocates unique, monotonically increasing Transaction Identifiers for every request.  
- Correlates asynchronous indication responses with causal requests across multi-threaded flight software architectures.

### 2.3 Normative Service Primitives (§3.4)

- **`ACQUIRE_FROM_DEVICE` (§3.4.2 & §3.4.3)**: Issues telemetry acquisition requests and delivers acquired telemetry bytes, completion status, and time tags.  
- **`COMMAND_DEVICE` (§3.4.4 & §3.4.5)**: Dispatches commanding values, setpoints, or configuration parameters to target device registers and returns execution acknowledgements.  
- **Normative Status Codes (§3.4.6)**: Comprehensive error classification including `SUCCESS`, `DEVICE_NOT_FOUND`, `DEVICE_TIMEOUT`, `PARAMETER_ERROR`, and `SUBNETWORK_ERROR`.

### 2.4 Subnetwork Bus Protocol Mappings (§2.4)

- **SpaceWire / RMAP (ECSS-E-ST-50-52C)**: Synthesizes Remote Memory Access Protocol non-incrementing read and write memory packets with 8-bit CRC polynomial \$G(x) \= x^8 \+ x^2 \+ x \+ 1\$ (`0x07`).  
- **MIL-STD-1553B**: Formats 16-bit Command Words containing Remote Terminal (RT) addresses, Transmit/Receive (T/R) mode bits, subaddresses, and word counts.  
- **CANopen (CiA 301\)**: Synthesizes Service Data Object (SDO) expedited upload/download request frames mapped to 16-bit Object Dictionary indexes.

---

## 3\. Directory Layout

das-forge-pro/

├── source-code/

│   ├── index.html            \# Responsive Glass-Cockpit Avionics Studio

│   ├── das-engine.js         \# Pure ES6 SOIS DAS & Subnetwork Adapter Engine

│   ├── das-auditor.js        \# 10-Point Normative Compliance Auditor

│   ├── das\_cli.py            \# Multi-subcommand Python 3 CLI & Dissector

│   └── test\_runner.py        \# 10 Automated Normative Unit Tests

├── documentation/

│   ├── CCSDS\_871.0\_DAS\_SPECIFICATION.md  \# Detailed Engineering Specification

│   └── DAS\_COMPLIANCE\_MATRIX.md          \# 10-Point Verification Matrix (100% PASS)

├── screenshots/

│   ├── das-architecture-diagram.svg      \# Vector Architecture Schematic

│   └── das-studio-preview.svg            \# Workstation Cockpit UI Mockup

└── build/

    └── das-forge-pro-v1.0.0.zip          \# Production Deployment Bundle

---

## 4\. Quickstart & Verification

### Running Automated Test Suite

python3 source-code/test\_runner.py

*Expected Result: 10/10 tests pass in \< 0.01s.*

### Command-Line Interface (CLI)

\# 1\. List registered onboard devices and bus mappings

python3 source-code/das\_cli.py list-devices

\# 2\. Acquire telemetry from SpaceWire Star Tracker

python3 source-code/das\_cli.py acquire \--device-id STAR-TRACKER-A \--address 0x0010

\# 3\. Dispatch actuator setpoint command to MIL-1553B Reaction Wheel

python3 source-code/das\_cli.py command \--device-id RW-PITCH-01 \--address 0x0001 \--value 4000

\# 4\. Dissect SpaceWire RMAP packet hex with CRC verification

python3 source-code/das\_cli.py dissect-bus \--hex 32014C40420100000010000004B7

\# 5\. Execute 10-Point Normative Compliance Audit

python3 source-code/das\_cli.py audit

### Launching the Web Console

Open `source-code/index.html` in any standard web browser to monitor live telemetry acquisitions, test command dispatching, inspect subnetwork wireframe packets, and verify normative compliance badges.