# CCSDS 871.0-M-1 SOIS Device Access Service (DAS) Engineering Specification

## 1\. Executive Summary & Normative Baseline

- **Document Identifier**: CCSDS 871.0-M-1 (Magenta Book, Issue 1, Recommended Practice)  
- **Publication Date**: March 2013  
- **Title**: Spacecraft Onboard Interface Services (SOIS) — Device Access Service (DAS)  
- **Security & Architecture Working Group**: Consultative Committee for Space Data Systems (CCSDS)  
- **Implementation**: DASForge Pro (v1.0.0)

This document formalizes the architectural principles, service primitives, transaction models, address mappings, and error classifications defined by CCSDS 871.0-M-1 for spacecraft onboard software and subnetwork device interfaces.

---

## 2\. Architectural Role in SOIS Reference Model (§2)

In modern satellite avionics architectures, flight software applications (e.g., Attitude Determination and Control Systems \[ADCS\], Thermal Control, Power Management, and Scientific Payload processing) must acquire telemetry from and issue commands to diverse onboard sensors and actuators.

Without a standard abstraction layer, flight software applications become tightly coupled to specific hardware bus protocols (such as SpaceWire RMAP packet framing, MIL-STD-1553B command word timing, or CANopen SDO protocols). The SOIS Device Access Service (DAS) sits directly between user application entities and the underlying subnetwork layer:

  ┌─────────────────────────────────────────────────────────────┐

  │       Spacecraft Flight Software Application Entities       │

  │     (ADCS, FDIR, Thermal Control, Payload Data Processors)  │

  └──────────────────────────────┬──────────────────────────────┘

                                 │

                   DAS Service Primitives (§3.4)

           \[ACQUIRE\_FROM\_DEVICE / COMMAND\_DEVICE\]

                                 │

  ┌──────────────────────────────▼──────────────────────────────┐

  │         SOIS Device Access Service (DAS) Provider           │

  │   • Physical Device Identifier Resolution (§3.2)            │

  │   • Monotonic Transaction Correlation (§3.3.1)              │

  │   • Status Classification & Timeout Enforcement (§3.4.6)    │

  └──────────────────────────────┬──────────────────────────────┘

                                 │

                   Device Access Protocol (DAP)

                                 │

  ┌──────────────────────────────▼──────────────────────────────┐

  │                 SOIS Subnetwork Layer (§2.4)                │

  ├──────────────────────┬──────────────────────┬───────────────┤

  │   SpaceWire / RMAP   │    MIL-STD-1553B     │    CANopen    │

  │  (ECSS-E-ST-50-52C)  │ (RT / Subaddress)    │  (SDO Frames) │

  └──────────────────────┴──────────────────────┴───────────────┘

---

## 3\. Physical Device Identifiers & Naming (§3.2)

- **Definition**: An abstract identifier (`physical_device_id`) uniquely assigned to each physical device attached to the spacecraft onboard subnetwork.  
- **Decoupling Mandate**: Flight applications address devices solely by their `physical_device_id` (e.g., `STAR-TRACKER-A`, `RW-PITCH-01`, `BATTERY-BMS`) rather than physical bus addresses (e.g., SpaceWire logical address `0x32`, MIL-1553 RT `4`, or CAN node ID `7`).  
- **Dynamic Reconfiguration**: If a sensor is rerouted to a backup subnetwork bus or swapped for an alternate model, only the DAS configuration table is updated; application code remains untouched.

---

## 4\. Service Primitives & Parameters (§3.4)

### 4.1 ACQUIRE\_FROM\_DEVICE (§3.4.2 & §3.4.3)

Acquires a data value or telemetry block from a specified physical device.

#### 4.1.1 ACQUIRE\_FROM\_DEVICE.request (§3.4.2)

ACQUIRE\_FROM\_DEVICE.request (

    Transaction Identifier,

    Physical Device Identifier,

    \[Device Address / Register\],

    \[Data Length\]

)

- **Transaction Identifier**: Integer uniquely identifying the transaction to correlate with the corresponding indication primitive.  
- **Physical Device Identifier**: Logical identifier of the target physical device.  
- **Device Address / Register**: Target register, memory offset, or telemetry channel within the device.

#### 4.1.2 ACQUIRE\_FROM\_DEVICE.indication (§3.4.3)

ACQUIRE\_FROM\_DEVICE.indication (

    Transaction Identifier,

    Physical Device Identifier,

    Status,

    \[Data Value\],

    \[Timestamp\]

)

- **Status**: Outcome of the operation (`SUCCESS`, `DEVICE_NOT_FOUND`, `DEVICE_TIMEOUT`, `PARAMETER_ERROR`, `SUBNETWORK_ERROR`).  
- **Data Value**: Raw or calibrated telemetry bytes returned from the physical device.  
- **Timestamp**: Optional time tag identifying when the data was sampled or received.

---

### 4.2 COMMAND\_DEVICE (§3.4.4 & §3.4.5)

Sends a command value, configuration parameter, or operational trigger to a physical device.

#### 4.2.1 COMMAND\_DEVICE.request (§3.4.4)

COMMAND\_DEVICE.request (

    Transaction Identifier,

    Physical Device Identifier,

    \[Device Address / Register\],

    Command Value

)

- **Command Value**: Command parameter, actuator setpoint (e.g., wheel speed in RPM), or register data to be written.

#### 4.2.2 COMMAND\_DEVICE.indication (§3.4.5)

COMMAND\_DEVICE.indication (

    Transaction Identifier,

    Physical Device Identifier,

    Status,

    \[Timestamp\]

)

- Acknowledges command execution or reports transmission failure.

---

## 5\. Subnetwork Bus Protocol Mappings (§2.4)

DAS service providers translate abstract primitives into concrete subnetwork protocols:

1. **SpaceWire / RMAP (ECSS-E-ST-50-52C)**:  
   - Maps read/write primitives to Remote Memory Access Protocol (RMAP) packets.  
   - Computes 8-bit RMAP header and data CRCs using the generator polynomial \$G(x) \= x^8 \+ x^2 \+ x \+ 1\$ (`0x07`).  
2. **MIL-STD-1553B**:  
   - Synthesizes 16-bit Command Words containing 5-bit Remote Terminal (RT) address, Transmit/Receive (T/R) bit, 5-bit Subaddress, and 5-bit Word Count.  
3. **CANopen**:  
   - Formats Service Data Object (SDO) expedited upload/download request frames referencing 16-bit Object Dictionary indexes and 8-bit subindexes.