# README — 45 nm Cadence Virtuoso Circuit Library

**Project:** Transistor-level logic primitives & combinational circuits (Cadence Virtuoso, 45 nm)

**Author:** Gokul Krish

**Target technology:** 45 nm CMOS process 



---

## 1. Project Overview

This repository contains transistor‑level implementations of fundamental logic gates and arithmetic circuits designed in Cadence Virtuoso using a 45 nm CMOS process. The focus is on understanding transistor‑level behavior, logic functionality, and circuit efficiency.

Below is a concise explanation of every circuit included in this project:

### Circuits Included

1. `2T_AND` — Two-transistor implementation of AND (minimal transistor count, meant for demonstration/low-power tradeoffs).
2. `2T_XNOR` — Two-transistor XNOR variant (compact logic cell).
3. `AND` — Standard complementary CMOS AND gate implementation.
4. `Full Adder` — 1-bit full adder (transistor-level implementation; includes sum and carry outputs).
5. `4x4 Multiplier using MGDI` — 4-bit by 4-bit array multiplier implemented using MGDI (Modified Gate Diffusion Input) cells for reduced transistor count.
6. `Half Adder` — 1-bit half adder (sum and carry).
7. `Inverter` — Basic CMOS inverter (used as building block and sizing reference).
8. `OR` — Standard complementary CMOS OR gate implementation.
9. `XNOR` — Standard complementary CMOS XNOR gate implementation.
10. `XOR` — Standard complementary CMOS XOR gate implementation.

---

## 2. Circuit Explanations

### **1. 2T AND Gate**

A minimal two‑transistor AND implementation using pass‑transistor logic. It reduces transistor count but may not provide full‑rail output. Useful for low‑power, compact logic demonstrations.

### **2. 2T XNOR Gate**

A two‑transistor implementation of XNOR, typically using pass‑logic or transmission‑gate style behavior. Extremely compact but may suffer from degraded voltage levels.

### **3. AND Gate (Standard CMOS)**

A conventional CMOS AND gate built using a NAND followed by an inverter or using series‑connected NMOS and parallel PMOS. Provides strong logic levels and reliable performance.

### **4. Full Adder**

A 1‑bit arithmetic circuit producing SUM and CARRY outputs. Implemented using XOR, AND, and OR structures at the transistor level. Key building block in multipliers and ALUs.

### **5. 4×4 Multiplier using MGDI**

A 4‑bit by 4‑bit parallel multiplier built using MGDI (Modified Gate Diffusion Input) logic. MGDI significantly reduces transistor count and power compared to CMOS while maintaining acceptable logic levels. Produces an 8‑bit product output.

### **6. Half Adder**

Computes SUM (A ⊕ B) and CARRY (A · B). Used as the first level of larger arithmetic circuits. Implemented using XOR and AND transistor structures.

### **7. Inverter**

A standard CMOS inverter using one PMOS and one NMOS transistor. Acts as the core building block for almost all other logic cells and sets baseline delay and power values.

### **8. OR Gate**

Implemented using parallel NMOS and series PMOS (or using NOR + inverter). Produces logical OR of two inputs.

### **9. XNOR Gate**

A full‑rail CMOS XNOR implementation providing complementary output of XOR. Often used in adders and comparison circuits.

### **10. XOR Gate**

Implemented using 8–12 transistors depending on topology. Computes logical exclusive OR, essential for adders, parity circuits, and MGDI blocks.

---

## 3. How to Run Simulations Repository / Library structure (suggested)

```
/ (project root)
├── README.md                      <- This file
├── cells/                          <- Cadence library cell views (schematic & layout)
│   ├── 2T_AND/
│   │   ├── schematic.view
│   │   └── layout.view
│   ├── 2T_XNOR/
│   ├── AND/
│   ├── Full_Adder/
│   ├── Multiplier_4x4_MGDI/
│   ├── Half_Adder/
│   ├── Inverter/
│   ├── OR/
│   ├── XNOR/
│   └── XOR/
├── testbenches/                    <- Cadence testbenches and stimulus
│   ├── tb_2T_AND.scs
│   ├── tb_2T_XNOR.scs
│   └── ...
├── netlists/                        <- Exported spectre/netlist files (optional)
├── layouts/                         <- GDS or layout exports (optional)
├── scripts/                         <- Run scripts / batch commands
└── docs/                            <- Additional documentation, timing tables, results
```

> Note: The exact file naming and cellview names depend on how you organize Cadence libraries. The above is a recommended structure for packaging the work.

---

## 3. Simulation environment & recommended settings

**Cadence tools:** Virtuoso Schematic Editor + ADE (Analog Design Environment). Simulation using Spectre (or the PDK provided simulator).

**Default power rails / operating points:**

* `VDD` : 1.0 V (common for 45 nm experiments). You can change to 1.2 V or as required by your PDK.
* `GND` : 0 V

**Analyses recommended:**

* **Transient (time-domain)** — for functional verification; apply input vectors and observe outputs.

  * Setup: `tran` analysis with total run time large enough to capture input transitions (e.g. `tran 100ns` or `tran 10ns` depending on vector speed).
* **DC operating point** — to inspect node voltages and device biasing.
* **Parametric/PVT corners** — run at different process corners (typical, fast, slow), temperatures (0°C, 27°C, 85°C), and supply variations to verify robustness.

**Testbench stimulus:**

* Use ideal voltage sources or digital stimulus sources available in the PDK.
* Apply all possible input vector combinations when feasible (for n-bit modules, use exhaustive or representative vectors). For example: for Full Adder (3 inputs), apply 8 vectors.
* For the 4x4 multiplier, verify with a set of representative vectors including: `0x0`, `0x1`, `0xF`, `random pairs`, and boundary cases.

**Measurement probes:**

* Probe `SUM`, `CARRY`, `PRODUCT[7:0]` etc. as node names in schematic.
* Use `measure` statements in ADE to capture propagation delay (e.g., `t_rise`, `t_fall`), delay from inputs to outputs, and static power consumption.

---

## 4. How to run simulations (basic steps)

1. Open your Cadence environment and load the library where cellviews are stored.
2. Open the schematic for the circuit (e.g., `2T_AND`), or open the testbench schematic (e.g., `tb_2T_AND`).
3. Launch ADE (ADE L or ADE XL depending on your license).
4. Set supply (`VDD`) and analysis type (Transient).
5. Add waveform probes to the outputs you care about.
6. Run the simulation, view waveforms in the Waveform Viewer (e.g., ViVA/MW or Waveform Viewer provided by Cadence).

cd netlists
spectre tb_2T_AND.scs > tb_2T_AND.out

```

(Exact commands depend on your PDK and simulator configuration.)

---

## 5. Verification checklist & expected behavior

For each module verify the following truth table or properties:

- **Inverter:** Input `0 -> output = VDD`; Input `VDD -> output = 0`.
- **AND (standard):** `A•B`.
- **2T_AND:** Check differences from standard AND (may rely on charge sharing or pass logic); examine for non-ideal rails and ensure correct functionality at chosen operating point.
- **OR:** `A + B`.
- **XOR / XNOR / 2T_XNOR:** Verify all input combos (2-input gates: 4 combos).
- **Half Adder:** `SUM = A xor B`; `CARRY = A & B`.
- **Full Adder:** `SUM = A xor B xor Cin`; `Cout = majority(A,B,Cin)`.
- **4x4 Multiplier (MGDI):** Multiply 4-bit operands to produce 8-bit product; compare output against behavioral expected results for a suite of tests.

Document and save waveform screenshots for at least: inverter response, representative gate transitions, full adder input combinations, and sample multiplier multiplication.

---



## 7. Performance/characterization notes (suggested)

- Measure **propagation delay** (input transition to stable output) for each gate.
- Measure **power** (static leakage and dynamic switching) for each block under typical toggling patterns.
- For the multiplier, measure area (transistor count), max operating frequency (via delay), and power at representative workloads.

Record these values in `docs/characterization.md` or a CSV under `docs/results.csv`.

---

## 8. File naming & cellview conventions (recommended)

- Use clear cell names, e.g., `inv_45nm`, `nand_cmos_45nm`, `fulladder_45nm`.
- Keep testbench names prefixed with `tb_` and indicate the stimulus type: `tb_fulladder_tran.scs`.
- Export netlists using the cell name and date for traceability: `Full_Adder_20251117.scs`.

---



## 10. Contact & support

If you need help running the simulations or want the README tailored to the exact file names and testbench scripts in your uploaded folder, tell me the library name and a short listing of the files (or allow me to inspect the folder structure) and I will update this README with exact commands and file references.


---

### Quick reference — Truth tables

- **XOR**

| A | B | XOR |
|---:|---:|:---:|
| 0 | 0 |  0  |
| 0 | 1 |  1  |
| 1 | 0 |  1  |
| 1 | 1 |  0  |

- **XNOR** is complement of XOR.

- **AND**

| A | B | AND |
|---:|---:|:---:|
| 0 | 0 |  0  |
| 0 | 1 |  0  |
| 1 | 0 |  0  |
| 1 | 1 |  1  |

(Use these tables to write expected waveforms in testbenches.)

---

```
