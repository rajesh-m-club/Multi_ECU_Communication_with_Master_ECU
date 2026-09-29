# Multi-ECU Communication with Master ECU

A software-simulated automotive ECU network implemented in **Embedded C**, demonstrating how multiple Electronic Control Units (ECUs) exchange sensor data over a shared **CAN (Controller Area Network) bus** and how a central master node processes incoming messages to trigger vehicle-level actions.

The project models four vehicle functions—airbag, obstacle detection, anti-theft, and fuel monitoring—and includes a test suite for validating the system's responses to different operating conditions.

## Project Overview

In a vehicle, individual ECUs handle dedicated functions and communicate with other controllers over in-vehicle networks. This project demonstrates that architecture in a PC-based simulation:

- Four independent ECU modules evaluate their respective sensor inputs.
- Each ECU creates a CAN message when its configured condition is met.
- CAN frames are collected by the master node.
- The master processes received frames according to CAN identifier priority.
- Based on the decoded messages, the master generates actions and warnings.

> **Scope:** This repository is a software simulation. The results demonstrate the ECU logic and simulated CAN-message flow; they do not represent validation on a physical vehicle or hardware CAN bus.

## System Architecture

```text
                    +---------------------------+
                    |       Master ECU          |
                    |  Receive and process CAN  |
                    |  Prioritize frames        |
                    |  Trigger vehicle actions |
                    +-------------+-------------+
                                  |
=============================== CAN BUS ===============================
          |               |              |               |
          |               |              |               |
  +-------+------+ +------+-------+ +----+---------+ +---+----------+
  |   Airbag ECU | |  Obstacle ECU| |   Anti-Theft | |    Fuel ECU  |
  | Impact input | | Distance input| | Tamper input| | Fuel input   |
  +--------------+ +--------------+ +--------------+ +--------------+
```

## ECU Functions and CAN Message IDs

The project uses standard 11-bit CAN identifiers in the simulated message format. A lower CAN ID represents a higher arbitration priority.

| ECU | CAN ID | Priority | Example trigger | Master action |
|---|---:|---|---|---|
| Airbag ECU | `0x100` | Highest | Impact `> 80` | Deploy airbag |
| Obstacle ECU | `0x110` | High | Distance `< 20 cm` | Emergency brake |
| Anti-Theft ECU | `0x120` | Medium | Tamper status `== 1` | Activate alarm |
| Fuel ECU | `0x130` | Lowest | Fuel level `< 15%` | Low-fuel warning |

The identifiers establish the priority order used by the master node when processing the simulated frames.

## Features

- **Modular ECU design:** Separate Embedded C modules for airbag, obstacle detection, anti-theft, and fuel monitoring.
- **Master-node processing:** Centralized reception, decoding, and handling of ECU messages.
- **CAN frame handling:** Simulated transmission and reception using CAN IDs, data length, and payload data.
- **Priority-based processing:** Lower CAN identifiers are handled as higher-priority messages.
- **Threshold-based alerts:** Sensor values are checked against configurable conditions to generate vehicle actions.
- **Multi-ECU integration:** Sensor events from multiple ECU modules are processed in one vehicle-level simulation.
- **Automated regression tests:** A Python test script checks expected system actions across multiple input scenarios.
- **Test reporting:** Test results are available in a generated HTML report.

## Example Scenarios

| Input condition | Expected system response |
|---|---|
| Impact value is above `80` | `DEPLOY AIRBAG` |
| Obstacle distance is below `20 cm` | `EMERGENCY BRAKE` |
| Tamper status is `1` | `ALARM ON` |
| Fuel level is below `15%` | `LOW FUEL WARNING` |
| No alert condition is met | `ALL SYSTEMS NORMAL` |

The exact output depends on the input values supplied to the simulation.

## CAN Message Priority

The simulated CAN IDs are assigned in ascending order of priority:

```text
0x100  Airbag ECU       Highest priority
0x110  Obstacle ECU
0x120  Anti-Theft ECU
0x130  Fuel ECU         Lowest priority
```

In CAN arbitration, a lower numerical identifier has higher priority when competing frames are transmitted. The project uses this relationship when ordering frames for master-node processing.

## Repository Structure

```text
Multi_ECU_Communication_with_Master_ECU/
├── firmware/
│   ├── common/                 # Shared CAN definitions and communication logic
│   ├── sensor_ecu/             # Sensor ECU implementation(s)
│   └── ...                     # Other ECU and master-node source files
├── tests/
│   ├── test_vehicle_system.py  # Automated vehicle-system tests
│   └── ...                     # Test inputs and supporting test files
├── docs/
│   ├── architecture.png        # System architecture diagram
│   ├── html_report.png         # Automated test report screenshot
│   ├── program_output.png      # Example simulation output
│   ├── test_results.png        # Test summary screenshot
│   └── priority_and_impact.png # CAN ID priority reference
└── README.md
```

*File and folder names above describe the main project areas and documented artifacts; consult the repository for the current exact tree.*

## Running the Tests

The automated test suite can be run with Python from the repository root:

```bash
python tests/test_vehicle_system.py
```

On systems where Python 3 is invoked as `python3`, use:

```bash
python3 tests/test_vehicle_system.py
```

The test report shown for the current test run contains **20 tests passed and 0 failed**. Re-run the suite after making code changes to verify the current state.

## Example Output

A representative run reports sensor readings and the corresponding CAN transmissions, followed by master-node actions. For example:

```text
[ECU: Airbag] Impact sensor: 90
[ECU: Airbag] Crash detected!
[CAN TX] ID: 0x100 | DLC: 1 | Data: 90

[ECU: Obstacle] Distance sensor: 10 cm
[ECU: Obstacle] Obstacle too close!
[CAN TX] ID: 0x110 | DLC: 1 | Data: 10

--- Processing CAN Frames ---
*** ACTION: DEPLOY AIRBAG ***
*** ACTION: EMERGENCY BRAKE ***
*** ACTION: ALARM ON ***
*** ACTION: LOW FUEL WARNING ***
```

This is illustrative output; exact formatting may vary with the implementation.

## Technologies and Concepts

- **C / Embedded C** — modular ECU logic and sensor-condition handling
- **CAN protocol concepts** — message identifiers, payloads, and priority
- **Python** — automated test execution
- **GCC** — C compilation and testing
- **Automotive embedded systems** — distributed controllers and centralized vehicle-level response

## Possible Extensions

- Add message timeout detection and ECU communication-loss handling.
- Introduce CAN error and frame-loss simulation.
- Add message validation and diagnostic fault logging.
- Extend the model with engine, transmission, or dashboard ECUs.
- Port the ECU modules to a microcontroller and validate them using a CAN transceiver and physical CAN bus.

## Disclaimer

This project is intended for educational purposes. It is not a production automotive control system and must not be used to control safety-critical vehicle functions.

## Author

**Rajesh Kumar Reddy**

Repository: [Multi_ECU_Communication_with_Master_ECU](https://github.com/rajesh-m-club/Multi_ECU_Communication_with_Master_ECU)
