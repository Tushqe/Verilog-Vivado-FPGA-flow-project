# Verilog-Vivado-FPGA-flow-project

# 8-bit ALU in Verilog

This project implements a structural **8-bit combinational ALU** in **Verilog** and verifies it through simulation in **Xilinx Vivado**. The ALU supports eight logic and arithmetic operations and was synthesized and implemented on a Xilinx FPGA target to study resource utilization and digital design flow.

## Project Overview

The ALU accepts two 8-bit operands, `A` and `B`, along with a 3-bit control input formed by `K2`, `K1`, and `K0`. Internally, the design is built from repeated **bit-slice modules**, with separate **logic** and **arithmetic** paths. A multiplexer selects between the two paths.

This project was used to practice:

- structural Verilog design
- hierarchical digital design
- waveform-based simulation and debugging
- synthesis and implementation in Vivado
- FPGA resource utilization analysis

## Top-Level Interface

### Inputs
- `A[7:0]` — 8-bit operand A
- `B[7:0]` — 8-bit operand B
- `K2, K1, K0` — ALU control bits

### Outputs
- `Y[7:0]` — 8-bit ALU result
- `Cout` — carry-out from the arithmetic path

## ALU Operation Table

| K2 | K1 | K0 | Operation |
|----|----|----|-----------|
| 0  | 0  | 0  | `~A`      |
| 0  | 0  | 1  | `A ^ B`   |
| 0  | 1  | 0  | `A & B`   |
| 0  | 1  | 1  | `A \| B`  |
| 1  | 0  | 0  | `A + B`   |
| 1  | 0  | 1  | `A`       |
| 1  | 1  | 0  | `A - B`   |
| 1  | 1  | 1  | `A + 1`   |

## Design Architecture

The ALU is constructed hierarchically:

- **ALU_8b**
  - Instantiates 8 copies of `BitSlice`
  - Connects the carry chain across all bits
- **BitSlice**
  - Contains:
    - `LogicBlk`
    - `ArithBlk`
    - `MUX_21`
- **LogicBlk**
  - Generates one of four logic functions:
    - NOT A
    - XOR
    - AND
    - OR
- **ArithBlk**
  - Uses a full adder and control muxes to generate arithmetic operations
- **MUX_21 / MUX_41**
  - Select between candidate outputs
- **FullAdder**
  - Performs 1-bit arithmetic with carry propagation

## Verification

The design was verified using behavioral simulation in Vivado.

### Simulation Strategy
Testbenches were used for:
- `FullAdder`
- `MUX_21`
- `MUX_41`
- `LogicBlk`
- `ArithBlk`
- top-level `ALU_8b`

### Verified Behaviors
Top-level simulation confirmed correct operation for all 8 control modes. Example test cases included:

- `A = 0x5A`, `B = 0x3C`
- logic mode checks:
  - `~A = 0xA5`
  - `A ^ B = 0x66`
  - `A & B = 0x18`
  - `A | B = 0x7E`
- arithmetic mode checks:
  - `A + B = 0x96`
  - `A = 0x5A`
  - `A - B = 0x1E`
  - `A + 1 = 0x5B`

### Edge Cases Verified
- `0xFF + 0x01 = 0x00`, `Cout = 1`
- `0x10 - 0x01 = 0x0F`
- `0xFF + 1 = 0x00`, `Cout = 1`

These tests confirmed correct output selection and carry propagation across the full 8-bit datapath.

## Synthesis and Implementation Results

The design was synthesized and implemented in **Xilinx Vivado** targeting:

- **Device:** `xc7z007sclg400-1`

### Resource Utilization
- **Slice LUTs:** 19
- **Slice Registers:** 0
- **Slices Used:** 6
- **Bonded IOBs:** 28

### Notes
- The design is **purely combinational**, so no flip-flops or registers were used.
- Timing summary fields were reported as `N/A` because no user-defined timing constraints were applied in this version of the project.

## Files


```text
ALU_8b.v
BitSlice.v
LogicBlk.v
ArithBlk.v
FullAdder.v
MUX_21.v
MUX_41.v
ALU_8b_tb.v


