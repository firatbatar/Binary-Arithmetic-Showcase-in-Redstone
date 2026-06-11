# Binary Arithmetic Unit — Minecraft Redstone Showcase

A fully functional binary arithmetic unit (BAU) implemented entirely in Minecraft redstone, built as part of the **ENS 491–492 Graduation Project** at Sabancı University. The showcase accepts decimal input from an in-game numeric keypad, converts it to binary, performs one of nine arithmetic or bitwise operations via an 8-bit ALU, and displays the result in binary on a static ROM-driven screen — all running in redstone logic with no mods affecting behaviour.

> **Part of a larger project:** *LLM Simulation on Minecraft with Redstone: Text-to-Image*
> — Berat Karataşoğlu, Bilge Kaan Yıldırım, Emre Üte, Fırat Batar
> Advisors: Onur Varol, Kamer Kaya — Sabancı University, 2026

---

## What It Does

The user enters two decimal numbers (each in the range 0–255) on an in-game numeric keypad, selects an operation, and the result appears instantly on the binary output screen.

**Supported operations (9 total):**

| Operation | Opcode |
|-----------|--------|
| ADD       | addition |
| SUB       | subtraction (two's complement) |
| AND       | bitwise AND |
| NAND      | bitwise NAND |
| OR        | bitwise OR |
| NOR       | bitwise NOR |
| XOR       | bitwise XOR |
| XNOR      | bitwise XNOR |
| SHR       | arithmetic right shift by 1 bit |

---

## Pipeline Overview

```
Numeric Keypad (decimal)
        │
        ▼
BCD Serialisation  ──→  BCD-to-Binary Converter  ──→  8-bit ALU  ──→  Static ROM Screen (binary)
```

1. **Numeric keypad** — Ten pressure-plate keys in a standard 4×3 layout encode each digit as a 4-bit BCD nibble via static comparator cells. Pressing a key fires a pulse generator that serialises the nibble onto a shared input wire.
2. **BCD-to-binary converter** — An extended carry-cancel adder with 8-bit register locks accumulates three BCD digits into a single 8-bit binary value (0–255). Two converters run in parallel (one per ALU operand), selected by an operand-select lever.
3. **8-bit ALU** — Built on top of a carry-cancel adder (CCA) architecture. The CCA resolves all carry states in parallel and completes an 8-bit addition in **5 redstone ticks**. Extended with controlled inverters, a flood-carry signal, and an XOR-to-OR converter to support all nine operations.
4. **Static ROM-driven screen** — A matrix of redstone lamps arranged as 2×2 super-pixels, each driven by a double-torch pixel driver. The ROM layers behind the screen are decoded directly from the ALU input and operation-select lines, so the display updates in real time.

---

## Technical Highlights

- **Carry-Cancel Adder (CCA):** A redstone-specific adder architecture that propagates and cancels carries in parallel using glass-tower structures, avoiding the ticking latency of ripple-carry designs. End-to-end latency: 5 redstone ticks. Verified against all 65,536 possible 8-bit input pairs.
- **Torch-tower serialisation:** A walking-clock signal propagates upward through a vertical torch column at 1 bit per 2 ticks, driving comparator cells to serialise BCD nibbles onto a single wire. The clock travels alongside the data so no external synchronisation is needed.
- **Modular design:** Every sub-module (CCA, BCD converter, serialiser–deserialiser, screen) is built to the edge-only I/O principle — all inputs and outputs sit on the outer face of the module — so integration required no internal modifications to any component.
- **Minecraft version:** Java Edition 1.20.4
---

## Download & Running

The Minecraft world file is available in the [**Releases**](../../releases) section of this repository.

### Requirements

- Minecraft: Java Edition **1.20.4**
- The world file is a standard single-player world; no server is required to load and explore it.

## How to Use the Showcase

1. Stand in front of the numeric keypad.
2. Use the **operand-select lever** to choose operand A or B.
3. Step on digit keys in sequence to enter a decimal number (up to three digits, 0–255). The screen updates after each key-press.
4. Flip the operand-select lever to switch to the other operand and repeat.
5. Select the desired operation using the operation-select levers above the keypad.
6. The result appears on the binary output screen automatically.

---

## Project Context

This showcase is one of two self-contained demonstrations produced by the graduation project *LLM Simulation on Minecraft with Redstone: Text-to-Image*. The broader goal of the project is to simulate the inference of a small transformer-based text-to-image model entirely in Minecraft redstone, targeting the [Quick, Draw!](https://www.tensorflow.org/datasets/catalog/quickdraw_bitmap) dataset for 16×16 binary sketch generation.

The BAU was built to validate the core integer-arithmetic modules — the carry-cancel adder, the BCD-to-binary converter, and the serialiser–deserialiser — that are reused directly in the larger logistic regression and (eventually) LLM pipelines. It also serves as a standalone demonstration that the modular redstone methodology works end-to-end.

The companion showcase — a **multi-class logistic regression classifier** for Quick, Draw! images (Banana, Baseball, Crown) — is available in a separate repository.

---

## Related Repositories

| Repository | Contents |
|---|---|
| MCHPRS Fork | Custom MCHPRS server with `///build_rom` and `///image_place` commands |
| Logistic Regression Showcase | MCHPRS world + PyTorch training script + fixed-point emulator |
| LLM Training Script | GPT-style decoder-only transformer trained on Quick, Draw! |
| Minecraft LLM Main Codebase | Redstone circuit schematics and build pipeline |

---

## References

- mattbatwings, "Redstone Binary Addition," YouTube. https://www.youtube.com/watch?v=Hl1dHFOl3Zo
- mattbatwings, YouTube channel. https://www.youtube.com/@mattbatwings
- MCHPRS — Minecraft High Performance Redstone Server. https://github.com/MCHPR/MCHPRS
- sammyuri, CraftGPT. https://github.com/sammyuri/craftgpt
