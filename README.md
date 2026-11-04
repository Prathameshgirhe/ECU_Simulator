# ECU_Simulator
<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=2,10,30&height=220&section=header&text=Automotive%20ECU%20Simulator&fontSize=42&fontColor=ffffff&fontAlignY=38&desc=STM32%20%E2%80%A2%20RTOS%20%E2%80%A2%20CAN%20Bus%20%E2%80%A2%20Bootloader%20%E2%80%A2%20Fault%20Injection&descAlignY=60&descSize=17&animation=twinkling" width="100%"/>

<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=20&pause=1000&color=FF8A00&center=true&vCenter=true&width=680&lines=Real-time+ECU+firmware+on+STM32;CAN+Bus+communication+%2B+diagnostics;Custom+bootloader+with+fast+flash+updates;Fault+injection+for+robust+diagnostics" alt="Typing SVG" /></a>

<br/>

![STM32](https://img.shields.io/badge/STM32-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white)
![C/C++](https://img.shields.io/badge/Embedded-C%2FC%2B%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)
![RTOS](https://img.shields.io/badge/RTOS-FreeRTOS-2EA44F?style=for-the-badge)
![CAN](https://img.shields.io/badge/CAN-Bus-E3000F?style=for-the-badge)
![Bootloader](https://img.shields.io/badge/Custom-Bootloader-FF8A00?style=for-the-badge)

</div>

---

## ✦ Overview

An **automotive Electronic Control Unit (ECU) simulator** built on an **STM32** microcontroller running an **RTOS**. It communicates over **CAN Bus**, supports firmware updates through a **custom bootloader**, and includes **embedded debugging** and **fault-injection testing** to validate diagnostic behavior the way real vehicle ECUs are tested.

> 💡 *Goal: replicate the core software stack of a real ECU: real-time tasks, CAN messaging, diagnostics and safe flashing.*

---

## ✦ Highlights

| | Feature | Details |
|---|---|---|
| 🧵 | **RTOS multitasking** | Separate tasks for CAN RX/TX, sensor simulation, diagnostics |
| 🚌 | **CAN Bus** | Frame transmit/receive with filtering and diagnostic messaging |
| 💾 | **Custom Bootloader** | Receives and flashes new firmware, then jumps to the application |
| 🐞 | **Embedded Debugging** | Debug support for diagnosing firmware and bus issues |
| 💥 | **Fault Injection** | Simulated faults to test detection and reporting |
| 📈 | **Diagnostic accuracy** | Improved by **30%** *(vs. **[baseline]**)* |
| ⚡ | **Flash updates** | Accelerated by **40%** *(vs. **[baseline]**)* |

---

## ✦ System Architecture

```mermaid
flowchart TB
    subgraph PC["🖥️ Host / Diagnostic Tool"]
        TOOL[CAN Analyzer / Diag Client]
    end

    subgraph ECU["🔧 STM32 ECU"]
        direction TB
        BL[[Bootloader]]
        subgraph APP["Application (RTOS)"]
            T1[CAN RX Task]
            T2[CAN TX Task]
            T3[Sensor Sim Task]
            T4[Diagnostics Task]
            T5[Fault Injection Task]
        end
        Q[(Message Queues)]
    end

    TOOL <== CAN Bus ==> T1
    T2 ==> TOOL
    T1 --> Q --> T4
    T3 --> Q
    T5 -.->|inject faults| T3
    T4 --> T2
    BL -->|jump after verify| APP

    style BL fill:#FF8A00,color:#000
    style T5 fill:#E3000F,color:#fff
    style T4 fill:#2EA44F,color:#fff
```

### Boot & update flow

```mermaid
sequenceDiagram
    participant H as Host
    participant B as Bootloader
    participant F as Flash
    participant A as Application
    Note over B: Reset
    B->>H: Ready for update?
    alt Update requested
        H->>B: Firmware chunks (CAN)
        B->>F: Erase + program
        B->>B: Verify (CRC)
    end
    B->>A: Jump to application
```

---

## ✦ Fault-Injection Testing

| Fault Type | How it is simulated | Expected ECU response |
|---|---|---|
| Sensor out-of-range | Force invalid value | DTC logged, flag raised |
| CAN message timeout | Stop periodic frame | Timeout detected |
| Corrupted frame / data | Bit flips in payload | Rejected, error counter increments |
| Flash/CRC failure | Corrupt image | Bootloader refuses to jump to app |

> Edit this table to match the faults you actually implemented.

---

## ✦ Output & Screenshots

> 📸 Add your captures to `docs/images/` with the names below.

<div align="center">

| Hardware Setup | CAN Bus Trace |
|:---:|:---:|
| <img src="docs/images/hardware_setup.svg" width="420"/> | <img src="docs/images/can_trace.svg" width="420"/> |
| *STM32 board + CAN transceiver* | *Frames captured on the bus* |

| Serial / Debug Log | Bootloader Flashing |
|:---:|:---:|
| <img src="docs/images/debug_log.svg" width="420"/> | <img src="docs/images/bootloader_flash.svg" width="420"/> |
| *Task output and diagnostic messages* | *Firmware update in progress* |

| Fault Injection Result | Oscilloscope / Logic Analyzer |
|:---:|:---:|
| <img src="docs/images/fault_injection.svg" width="420"/> | <img src="docs/images/logic_analyzer.svg" width="420"/> |
| *DTC raised after injected fault* | *CAN waveform at bit level* |

</div>

Example log *(replace with your real output)*:

```text
[ECU] Boot OK  | FW v1.0.3
[CAN] RX id=0x7E0 dlc=8  data=02 01 0C 00 00 00 00 00
[DIAG] Request: Engine RPM
[CAN] TX id=0x7E8 dlc=8  data=04 41 0C 1A F8 00 00 00
[FAULT] Injected: SENSOR_OUT_OF_RANGE
[DIAG] DTC P0xxx stored
```

---

## ✦ Repository Structure

```text
ecu-simulator/
├── bootloader/
│   ├── src/
│   └── linker/
├── app/
│   ├── Core/
│   │   ├── Inc/
│   │   └── Src/
│   ├── can/             # CAN driver + message handling
│   ├── diag/            # diagnostics + DTC logic
│   ├── fault_inject/    # fault injection module
│   └── rtos/            # task + queue configuration
├── tools/               # host-side scripts
├── docs/images/
└── README.md
```

---

## ✦ Getting Started

**Requirements:** STM32 board (`<fill part number>`), CAN transceiver, USB-CAN adapter, STM32CubeIDE, ST-Link.

```bash
git clone https://github.com/Prathameshgirhe/<repo-name>.git
```

1. Open the project in **STM32CubeIDE**
2. Build and flash the **bootloader** first
3. Build the **application** (linked at the app offset)
4. Connect the CAN adapter and open your analyzer at the configured baud rate
5. Send diagnostic requests and watch the responses 🎉

---

## ✦ Roadmap

- [ ] UDS (ISO 14229) service coverage
- [ ] Secure bootloader (signature check)
- [ ] CAN-FD support
- [ ] Hardware-in-the-loop test automation

---

<div align="center">

### 👤 Author

**Prathamesh Girhe**
B.E. Electronics & Telecommunication · GCOEARA, Avasari Khurd

[![GitHub](https://img.shields.io/badge/GitHub-Prathameshgirhe-181717?style=for-the-badge&logo=github)](https://github.com/Prathameshgirhe)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Prathamesh%20Girhe-0A66C2?style=for-the-badge&logo=linkedin)](https://linkedin.com/in/prathamesh-girhe-b6a09631a)

⭐ *If you like it, star it!*

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=2,10,30&height=100&section=footer" width="100%"/>

</div>
