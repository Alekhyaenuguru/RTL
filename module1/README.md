============================================================
                    MODULE 1
============================================================

# Module 1 — RTL Design, Simulation and Synthesis

## Overview

Module 1 introduces the basic RTL-to-hardware design flow using a
2:1 multiplexer as the primary design example.

The module covers RTL coding, testbench development, simulation,
VCD generation, waveform analysis and synthesis using Yosys.

The practical flow followed in this module is:

    RTL Design
        ↓
    Testbench
        ↓
    Icarus Verilog
        ↓
    VCD Generation
        ↓
    GTKWave
        ↓
    Yosys Synthesis
        ↓
    SKY130 Technology Mapping

---

## Table of Contents

1. Module Objectives
2. Introduction to RTL Design
3. 2:1 Multiplexer
4. RTL Implementation
5. Testbench Development
6. Icarus Verilog Simulation
7. VCD Generation
8. GTKWave Analysis
9. RTL Synthesis
10. SKY130 Standard-Cell Mapping
11. Experimental Observations
12. Conclusion


============================================================
1. MODULE OBJECTIVES
============================================================

The main objectives of Module 1 are:

- Understand the basic RTL design methodology.
- Learn how a simple digital circuit is described using Verilog.
- Develop a testbench for functional verification.
- Compile and simulate Verilog using Icarus Verilog.
- Generate Value Change Dump files.
- Observe signal transitions using GTKWave.
- Understand the basic concept of RTL synthesis.
- Use Yosys for synthesis.
- Observe how RTL can be mapped to SKY130 standard cells.

This module establishes the foundation required for the
remaining RTL design and synthesis experiments.


============================================================
2. INTRODUCTION TO RTL DESIGN
============================================================

RTL stands for Register Transfer Level.

At RTL, a digital circuit is described in terms of:

- Inputs
- Outputs
- Combinational operations
- Registers
- Clocked behavior
- Control signals
- Data movement

The RTL description is written using hardware description
languages such as Verilog.

Unlike software programs, RTL code represents hardware
behavior that can eventually be synthesized into gates,
multiplexers, flip-flops and other standard cells.


============================================================
3. 2:1 MULTIPLEXER
============================================================

The main design used in this module is a 2:1 multiplexer.

A multiplexer selects one input from two available inputs
according to the value of a select signal.

The design contains four signals:

    i0  → First data input
    i1  → Second data input
    sel → Select signal
    y   → Output

The functional operation is:

    sel = 0 → y = i0

    sel = 1 → y = i1


The Boolean expression for the multiplexer is:

    y = (~sel & i0) | (sel & i1)


The basic conceptual structure is:

                 ┌───────────┐
    i0 ─────────►│           │
                 │   2:1     │────► y
    i1 ─────────►│   MUX     │
                 │           │
    sel ────────►│           │
                 └───────────┘


============================================================
4. RTL IMPLEMENTATION
============================================================

The multiplexer can be described using a combinational
always block.

Example RTL:

module good_mux (
    input i0,
    input i1,
    input sel,
    output reg y
);

always @(*)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end

endmodule


The conditional operator can also describe the same function:

assign y = sel ? i1 : i0;


The RTL should represent combinational behavior without
unintended storage elements.


============================================================
5. TESTBENCH DEVELOPMENT
============================================================

A testbench is used to verify the RTL design.

The testbench generates input stimulus and observes the
resulting output.

The basic verification relationship is:

    Testbench
        ↓
    Input Stimulus
        ↓
    MUX
        ↓
    Output
        ↓
    Verification


The testbench should apply different combinations of:

    i0
    i1
    sel

For example:

    i0 = 0, i1 = 1, sel = 0
    Expected y = 0

    i0 = 0, i1 = 1, sel = 1
    Expected y = 1

Additional combinations should also be tested to ensure
complete functional coverage.


============================================================
6. ICARUS VERILOG SIMULATION
============================================================

Icarus Verilog is used to compile and simulate the RTL
and testbench.

A typical simulation command is:

    iverilog -o mux_sim good_mux.v tb_good_mux.v

The generated simulation can then be executed using:

    ./mux_sim


If there are no compilation errors, the simulator executes
the testbench and produces the required simulation output.


============================================================
7. VCD GENERATION
============================================================

VCD stands for Value Change Dump.

A VCD file records signal changes occurring during simulation.

The testbench can contain:

    $dumpfile("good_mux.vcd");
    $dumpvars(0, tb_good_mux);


The resulting file can be used by GTKWave.

The flow is:

    Verilog RTL
         +
    Testbench
         ↓
    Icarus Verilog
         ↓
    Simulation
         ↓
    good_mux.vcd


The VCD file provides a record of signal activity during
the simulation.


============================================================
8. GTKWAVE ANALYSIS
============================================================

GTKWave is used to visually inspect the generated VCD file.

Important signals to observe include:

    i0
    i1
    sel
    y

The waveform should confirm:

    sel = 0 → y follows i0

    sel = 1 → y follows i1


Waveform analysis is important because it provides visual
evidence that the RTL design behaves according to its
functional specification.

The expected relationship can be represented as:

    i0 ───────┐
             │
             ├──► MUX ───► y
             │
    i1 ───────┘
                ▲
                │
               sel


============================================================
9. RTL SYNTHESIS
============================================================

After simulation, the RTL can be synthesized.

Synthesis converts the behavioral RTL description into a
structural representation of hardware.

The basic synthesis process is:

    RTL
     ↓
    Yosys
     ↓
    RTL Processing
     ↓
    Logic Optimization
     ↓
    Netlist


Yosys can analyze the MUX RTL and generate a corresponding
logic structure.

The synthesized representation allows the designer to
observe the hardware inferred from the RTL.


============================================================
10. SKY130 STANDARD-CELL MAPPING
============================================================

The design can further be mapped to the SKY130 standard-cell
library.

A standard-cell library contains pre-designed cells that
can implement common digital functions.

Examples include:

    INV
    NAND
    NOR
    AND
    OR
    XOR
    XNOR
    MUX
    DFF
    BUF


The technology mapping flow is:

    RTL
     ↓
    Yosys
     ↓
    Logic Optimization
     ↓
    SKY130 Library
     ↓
    Technology Mapping
     ↓
    Gate-Level Netlist


This stage connects the technology-independent RTL description
with a specific semiconductor technology.


============================================================
11. EXPERIMENTAL OBSERVATIONS
============================================================

| Stage | Observation |
|-------|-------------|
| RTL Design | 2:1 MUX behavior is described |
| Testbench | Input combinations are generated |
| Icarus Verilog | RTL functionality is simulated |
| VCD | Signal transitions are recorded |
| GTKWave | Simulation signals are viewed graphically |
| Yosys | RTL is converted into structural logic |
| SKY130 | Logic can be mapped to standard cells |
| Netlist | Synthesized hardware representation is obtained |


Important observations from the experiment:

1. RTL describes the intended hardware behavior.

2. The testbench allows the design to be verified without
   changing the actual RTL.

3. Icarus Verilog performs functional simulation.

4. VCD files preserve signal transitions for later analysis.

5. GTKWave provides a graphical representation of simulation
   activity.

6. Synthesis converts behavioral RTL into hardware structures.

7. Technology mapping selects cells from the target library.


============================================================
12. COMPLETE DESIGN FLOW
============================================================

The complete flow followed in Module 1 is:

             RTL CODE
                 │
                 ▼
            TESTBENCH
                 │
                 ▼
        ICARUS VERILOG
                 │
                 ▼
          SIMULATION
                 │
                 ▼
             VCD FILE
                 │
                 ▼
            GTKWAVE
                 │
                 ▼
             YOSYS
                 │
                 ▼
        LOGIC SYNTHESIS
                 │
                 ▼
        SKY130 LIBRARY
                 │
                 ▼
       TECHNOLOGY MAPPING
                 │
                 ▼
          FINAL NETLIST


============================================================
CONCLUSION
============================================================

Module 1 provides the basic foundation for RTL design and
digital hardware verification.

The 2:1 multiplexer is used to demonstrate the complete path
from writing RTL to obtaining a synthesized hardware
representation.

Through this module, the following concepts are introduced:

- RTL coding
- Combinational logic
- Testbench development
- Icarus Verilog simulation
- VCD generation
- GTKWave waveform analysis
- Yosys synthesis
- Standard-cell libraries
- SKY130 technology mapping

The knowledge gained from this module forms the basis for
the sequential logic, optimization and gate-level simulation
experiments covered in the following modules.