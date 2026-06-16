# APB_protocol_using_SV
## Overview
This project implements a custom APB (Advanced Peripheral Bus) slave and a lightweight object-oriented SystemVerilog verification environment.

The environment verifies:
- Reset behavior
- APB write transactions
- APB read transactions
- Mixed read/write traffic
- Wait-state handling using PREADY
- Error response generation using PSLVERR

The testbench is built using mailbox-based communication between generator, driver, monitor, and scoreboard components.

---

## Project Structure

| File | Description |
|--------|-------------|
| apb_slave.sv | APB slave DUT implementation |
| interface.sv | APB interface and clocking blocks |
| trans.sv | Transaction class definition |
| generator.sv | Base virtual generator class |
| case1.sv | Reset test generator |
| case2.sv | Write-only generator |
| case3.sv | Read-only generator |
| case4.sv | Mixed read/write generator |
| driver.sv | Drives transactions onto APB |
| monitor.sv | Captures DUT activity |
| scoreboard.sv | Reference model and checking |
| env.sv | Verification environment |
| test.sv | Test selection logic |
| testbench.sv | Top-level testbench |
| define.sv | Global parameter definitions |

---

## DUT Description

### APB Slave Features
- 8-bit address bus
- 8-bit data bus
- Internal memory depth: 8 locations
- Programmable wait-state insertion
- Read and write support
- Error generation for illegal addresses

### APB Signals

| Signal | Direction | Description |
|----------|----------|-------------|
| PCLK | Input | APB clock |
| PRESETn | Input | Active-low reset |
| PSEL | Input | Peripheral select |
| PENABLE | Input | Enable phase indicator |
| PWRITE | Input | Read/Write control |
| PADDR | Input | Address bus |
| PWDATA | Input | Write data |
| PRDATA | Output | Read data |
| PREADY | Output | Transfer completion |
| PSLVERR | Output | Error response |

---

## Verification Architecture

Generator -> Driver -> DUT -> Monitor -> Scoreboard

### Generator
Creates randomized transactions and sends them through a mailbox.

### Driver
Implements APB protocol:
1. Setup phase
2. Enable phase
3. Wait for PREADY
4. Capture read data

### Monitor
Observes completed APB transfers and forwards them to the scoreboard.

### Scoreboard
Maintains a reference memory model and compares expected read data against DUT responses.

---

## Available Testcases

### Testcase 0 : Reset Check
Validates DUT reset behavior.

Run:
+TESTCASE=0

### Testcase 1 : Write Operations
Generates randomized write transactions.

Run:
+TESTCASE=1

### Testcase 2 : Read Operations
Generates randomized read transactions.

Run:
+TESTCASE=2

### Testcase 3 : Mixed Read/Write
Generates both reads and writes.

Run:
+TESTCASE=3

(Default testcase)

---

## Transaction Constraints

Address range:
0 - 10

Data range:
0 - 100

Randomized fields:
- PRESETn
- PWRITE
- PADDR
- PWDATA

---

## DUT Functional Behavior

### Write Transaction
When:
- PSEL = 1
- PENABLE = 1
- PWRITE = 1

The DUT writes PWDATA into internal memory.

### Read Transaction
When:
- PSEL = 1
- PENABLE = 1
- PWRITE = 0

The DUT returns data on PRDATA.

### Wait States
The slave inserts programmable wait cycles before asserting PREADY.

### Error Handling
PSLVERR is asserted for restricted addresses:
- 0x10
- 0x11

---

## Simulation Flow

1. Start clock generation
2. Create environment
3. Select testcase using plusargs
4. Generator creates transactions
5. Driver applies APB transfers
6. Monitor captures activity
7. Scoreboard checks correctness
8. Results printed to console

---

## Expected Console Messages

[GEN] Generated transaction
[DRV] Write Data sent
[DRV] Read Data from DUT
[MON] Captured Transaction
[SCB] PASS
[SCB] FAIL

---
