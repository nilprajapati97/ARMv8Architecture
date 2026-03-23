# ARMv8 Architecture - Complete Reference Guide

A comprehensive guide to ARMv8 AArch64 architecture covering execution states registers exception levels memory management SIMD NEON virtualization security extensions and more.

---

## Table of Contents

- [1 Overview](#1-overview)
- [2 Execution States](#2-execution-states)
- [3 Register File](#3-register-file)
- [4 Exception Levels](#4-exception-levels)
- [5 TrustZone Security](#5-trustzone-security)
- [6 Instruction Set Architecture A64](#6-instruction-set-architecture-a64)
- [7 NEON SIMD and Floating Point](#7-neon-simd-and-floating-point)
- [8 Memory Management](#8-memory-management)
- [9 Virtualization Support](#9-virtualization-support)
- [10 Cryptographic Extensions](#10-cryptographic-extensions)
- [11 Exception and Interrupt Handling](#11-exception-and-interrupt-handling)
- [12 GIC Generic Interrupt Controller](#12-gic-generic-interrupt-controller)
- [13 ARMv8 Sub Versions and Extensions](#13-armv8-sub-versions-and-extensions)
- [14 LSE Atomics ARMv8 1](#14-lse-atomics-armv8-1)
- [15 Pointer Authentication ARMv8 3](#15-pointer-authentication-armv8-3)
- [16 Memory Tagging Extension ARMv8 5](#16-memory-tagging-extension-armv8-5)
- [17 SVE Scalable Vector Extension](#17-sve-scalable-vector-extension)
- [18 Debug Architecture](#18-debug-architecture)
- [19 ARMv8 vs ARMv7 Comparison](#19-armv8-vs-armv7-comparison)
- [20 Notable Processor Implementations](#20-notable-processor-implementations)
- [21 Boot Sequence](#21-boot-sequence)
- [22 System Control Registers](#22-system-control-registers)
- [23 Cache Architecture](#23-cache-architecture)
- [24 Power Management](#24-power-management)
- [25 Programming Examples](#25-programming-examples)
- [26 Quick Reference Summary](#26-quick-reference-summary)
- [27 Useful Resources](#27-useful-resources)

---

## 1 Overview

| Parameter | Details |
|-----------|---------|
| Released | October 2011 |
| Developer | ARM Holdings now Arm Ltd |
| Key Change | First ARM architecture with 64 bit support |
| Instruction Set | AArch64 64 bit plus AArch32 32 bit backward compatible |
| Used In | Smartphones Servers Embedded Systems HPC IoT |
| Notable Processors | Apple M1 M2 Qualcomm Snapdragon AWS Graviton Cortex A53 A57 A72 A76 A78 X1 |

### Architecture Profiles

| Profile | Name | Target |
|---------|------|--------|
| ARMv8-A | Application | Smartphones Servers PCs |
| ARMv8-R | Real-time | Automotive Industrial Storage |
| ARMv8-M | Microcontroller | IoT Embedded Sensors |

---

## 2 Execution States

ARMv8 introduces two execution states

| Feature | AArch64 | AArch32 |
|---------|---------|---------|
| Register Width | 64 bit | 32 bit |
| Instruction Set | A64 | A32 ARM plus T32 Thumb2 |
| GP Registers | 31 X0 to X30 | 15 R0 to R14 |
| PC Access | Not directly accessible | Directly accessible R15 |
| Address Space | 64 bit virtual | 32 bit virtual |
| Backward Compatible | New | ARMv7 compatible |
| Condition Codes | Conditional branch and select only | IT blocks and predicated instructions |
| Instruction Width | Fixed 32 bit | 32 bit A32 and 16 bit T32 |

### State Switching Rules

- Lower EL can be AArch32 if higher EL is AArch64
- Lower EL CANNOT be AArch64 if higher EL is AArch32
- State change only happens at exception level change
- AArch64 to AArch32 on exception return to lower EL
- AArch32 to AArch64 on exception entry to higher EL

---

## 3 Register File

### 3.1 General Purpose Registers

| Register Type | Count | Size | Purpose |
|---------------|-------|------|---------|
| General Purpose | 31 X0 to X30 | 64 bit | Data operations |
| Zero Register | 1 XZR WZR | 64 or 32 bit | Hardwired zero |
| Stack Pointer | 1 SP | 64 bit | Stack management |
| Program Counter | 1 PC | 64 bit | Instruction address |
| SIMD and FP | 32 V0 to V31 | 128 bit | Floating point and NEON |
| System Registers | Multiple | Various | Control and status |

### 3.2 AAPCS64 Calling Convention

| Register | Role | Saved By |
|----------|------|----------|
| X0-X7 | Arguments and Return values | Caller |
| X8 | Indirect result location | Caller |
| X9-X15 | Temporary scratch | Caller |
| X16-X17 | Intra procedure call linker | Caller |
| X18 | Platform register | Platform specific |
| X19-X28 | Callee saved | Callee |
| X29 | Frame pointer | Callee |
| X30 | Link register | Callee |
| SP | Stack pointer | N/A |

### 3.3 Special Registers

| Register | Purpose |
|----------|---------|
| SP_EL0 | Stack Pointer for EL0 |
| SP_EL1 | Stack Pointer for EL1 |
| SP_EL2 | Stack Pointer for EL2 |
| SP_EL3 | Stack Pointer for EL3 |
| ELR_EL1 | Exception Link Register for EL1 |
| ELR_EL2 | Exception Link Register for EL2 |
| ELR_EL3 | Exception Link Register for EL3 |
| SPSR_EL1 | Saved Program Status Register for EL1 |
| SPSR_EL2 | Saved Program Status Register for EL2 |
| SPSR_EL3 | Saved Program Status Register for EL3 |
| VBAR_EL1 | Vector Base Address Register for EL1 |
| VBAR_EL2 | Vector Base Address Register for EL2 |
| VBAR_EL3 | Vector Base Address Register for EL3 |

### 3.4 Process State PSTATE

| Flag | Name | Description |
|------|------|-------------|
| N | Negative | Result is negative |
| Z | Zero | Result is zero |
| C | Carry | Carry or borrow occurred |
| V | Overflow | Signed overflow occurred |
| D | Debug | Debug exception mask |
| A | SError | SError interrupt mask |
| I | IRQ | IRQ interrupt mask |
| F | FIQ | FIQ interrupt mask |
| nRW | Execution State | 0 equals AArch64 and 1 equals AArch32 |
| EL | Exception Level | Current exception level 0 to 3 |
| SP | Stack Pointer | 0 equals SP_EL0 and 1 equals SP_ELn |

---

## 4 Exception Levels

| Exception Level | Purpose | Example | Privilege |
|-----------------|---------|---------|-----------|
| EL0 | User Application | Apps Programs | Lowest Unprivileged |
| EL1 | OS Kernel | Linux Windows RTOS | Privileged |
| EL2 | Hypervisor | KVM Xen VMware | Higher Privilege |
| EL3 | Secure Monitor | ARM Trusted Firmware TrustZone | Highest Privilege |

### Exception Level Rules

- Exception Entry always moves to same or higher EL
- Exception Return always moves to same or lower EL
- Each EL has own SP SPSR ELR registers
- AArch32 and AArch64 switch can happen at EL change
- Secure and Non Secure controlled by EL3
- EL2 and EL3 are implementation defined and optional

### Exception Level Register Access

---

## 5 TrustZone Security

| Feature | Details |
|---------|---------|
| Hardware Isolation | Separate secure and normal worlds |
| Memory Protection | Secure memory not accessible from normal world |
| Peripheral Protection | Secure peripherals isolated via TZPC and TZASC |
| Use Cases | DRM Payments Biometrics Key Storage Secure Boot |
| Transition | Via SMC Secure Monitor Call instruction |
| Bus Signal | NS bit on AXI bus determines secure or non secure access |

### TrustZone Components

| Component | Purpose |
|-----------|---------|
| TZASC | TrustZone Address Space Controller for memory partitioning |
| TZPC | TrustZone Protection Controller for peripheral protection |
| TZMA | TrustZone Memory Adapter for on chip ROM and RAM protection |
| GIC Security | Interrupt groups Group 0 equals Secure and Group 1 equals Non secure |

---

## 6 Instruction Set Architecture A64

All A64 instructions are fixed 32 bit width

### 6.1 Arithmetic Operations

```asm
ADD   X0, X1, X2           ; X0 = X1 + X2
ADDS  X0, X1, X2           ; X0 = X1 + X2 and set flags
SUB   X0, X1, X2           ; X0 = X1 - X2
SUBS  X0, X1, X2           ; X0 = X1 - X2 and set flags
MUL   X0, X1, X2           ; X0 = X1 * X2
SDIV  X0, X1, X2           ; X0 = X1 / X2 signed
UDIV  X0, X1, X2           ; X0 = X1 / X2 unsigned
MADD  X0, X1, X2, X3       ; X0 = X3 + X1 * X2
MSUB  X0, X1, X2, X3       ; X0 = X3 - X1 * X2
SMADDL X0, W1, W2, X3      ; X0 = X3 + W1 * W2 signed long
UMADDL X0, W1, W2, X3      ; X0 = X3 + W1 * W2 unsigned long
SMULH X0, X1, X2           ; X0 = X1 * X2 upper 64 bits signed
UMULH X0, X1, X2           ; X0 = X1 * X2 upper 64 bits unsigned
NEG   X0, X1               ; X0 = -X1
ADC   X0, X1, X2           ; X0 = X1 + X2 + Carry
SBC   X0, X1, X2           ; X0 = X1 - X2 - NOT Carry

6.2 Logical Operations
AND   X0, X1, X2           ; Bitwise AND
ANDS  X0, X1, X2           ; Bitwise AND and set flags
ORR   X0, X1, X2           ; Bitwise OR
EOR   X0, X1, X2           ; Bitwise XOR
BIC   X0, X1, X2           ; Bit Clear AND NOT
ORN   X0, X1, X2           ; OR NOT
EON   X0, X1, X2           ; XOR NOT
MVN   X0, X1               ; Bitwise NOT
AND   X0, X1, X2           ; Bitwise AND
ANDS  X0, X1, X2           ; Bitwise AND and set flags
ORR   X0, X1, X2           ; Bitwise OR
EOR   X0, X1, X2           ; Bitwise XOR
BIC   X0, X1, X2           ; Bit Clear AND NOT
ORN   X0, X1, X2           ; OR NOT
EON   X0, X1, X2           ; XOR NOT
MVN   X0, X1               ; Bitwise NOT
