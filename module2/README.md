============================================================
                    MODULE 2
============================================================

# Module 2 — RTL Synthesis and Sequential Logic

## Overview

Module 2 explores how RTL descriptions are transformed into
synthesized hardware using Yosys and a standard-cell library.

The module moves beyond basic combinational simulation and
introduces sequential circuits, D flip-flops, asynchronous
controls, hierarchical RTL and arithmetic hardware.

The main flow studied in this module is:

    RTL Design
        ↓
    RTL Analysis
        ↓
    Yosys Synthesis
        ↓
    Standard Cell Library
        ↓
    Technology Mapping
        ↓
    Synthesized Netlist
        ↓
    Structural Hardware


============================================================
TABLE OF CONTENTS
============================================================

1. Module Objectives
2. Introduction to RTL Synthesis
3. Yosys Synthesis Tool
4. Standard-Cell Library
5. Liberty (.lib) File
6. RTL-to-Netlist Conversion
7. Synthesis Illustration
8. Sequential Logic
9. D Flip-Flop with Asynchronous Reset
10. D Flip-Flop with Asynchronous Set
11. Simulation and Waveform Verification
12. Hierarchical RTL Design
13. Multiplier Synthesis
14. Overall Synthesis Flow
15. Experimental Observations
16. Conclusion


============================================================
1. MODULE OBJECTIVES
============================================================

The main objectives of Module 2 are:

- Understand the purpose of RTL synthesis.
- Learn the basic operation of Yosys.
- Understand the role of a standard-cell library.
- Study the contents of a Liberty `.lib` file.
- Understand technology mapping.
- Study D flip-flop based sequential logic.
- Analyze asynchronous reset behavior.
- Analyze asynchronous set behavior.
- Verify sequential circuits using simulation waveforms.
- Understand hierarchical RTL structures.
- Observe synthesis of arithmetic operations.
- Relate RTL descriptions to synthesized gate-level hardware.


============================================================
2. INTRODUCTION TO RTL SYNTHESIS
============================================================

RTL synthesis is the process of converting an RTL description
into a structural representation of hardware.

The RTL describes what the circuit should do, while synthesis
determines how the required behavior can be implemented using
available hardware cells.

A simplified representation is:

    RTL Description
          ↓
       Synthesis
          ↓
    Logic Representation
          ↓
    Technology Mapping
          ↓
    Gate-Level Netlist


During synthesis, the tool can perform several operations such
as:

- RTL elaboration
- Process conversion
- Boolean optimization
- Constant propagation
- Logic simplification
- Technology mapping
- Netlist generation


============================================================
3. YOSYS SYNTHESIS TOOL
============================================================

Yosys is an open-source RTL synthesis framework.

It can read Verilog designs, analyze the RTL and generate
structural representations of the required hardware.

A simplified Yosys flow is:

    Verilog RTL
         ↓
    Read Design
         ↓
    Elaborate Hierarchy
         ↓
    Process RTL
         ↓
    Optimize Logic
         ↓
    Technology Mapping
         ↓
    Generate Netlist


Typical synthesis operations may include:

    read_verilog
    hierarchy
    proc
    opt
    techmap
    abc
    write_verilog
    show


The synthesis result can also be visualized to understand the
hardware inferred from the RTL.


============================================================
4. STANDARD-CELL LIBRARY
============================================================

A standard-cell library contains pre-designed logic cells that
can be used to implement digital circuits.

Common cells include:

    INV
    BUF
    NAND
    NOR
    AND
    OR
    XOR
    XNOR
    MUX
    DFF


The library can contain multiple versions of the same logical
function with different drive strengths.

For example:

    INV_X1
    INV_X2
    INV_X4


The logical operation is similar, but the electrical
characteristics can differ.

The synthesis tool selects suitable cells according to the
required logic and target technology.


============================================================
5. LIBERTY (.LIB) FILE
============================================================

A Liberty file normally uses the `.lib` extension.

It contains information describing the behavior and
characteristics of standard cells.

Typical information includes:

- Cell names
- Pin names
- Input and output directions
- Logic functions
- Timing information
- Area
- Power information
- Sequential behavior
- Operating conditions


For example, a cell description can specify that an output is
the logical AND of two inputs.

The synthesis tool uses this information when converting the
technology-independent logic into technology-specific cells.

The relationship is:

    RTL
     ↓
    Logical Function
     ↓
    .lib Information
     ↓
    Standard Cell
     ↓
    Technology-Mapped Netlist


============================================================
6. RTL-TO-NETLIST CONVERSION
============================================================

The main purpose of synthesis is to obtain a netlist from the
RTL description.

For example:

    assign y = a & b;


may eventually become an AND standard cell.

Similarly:

    assign y = a | b;


may be represented using an OR cell.

A clocked register may be mapped to a D flip-flop cell.

Therefore:

    RTL Construct
         ↓
    Logical Hardware
         ↓
    Standard Cell
         ↓
    Netlist


The netlist provides a structural description of the hardware.


============================================================
7. SYNTHESIS ILLUSTRATION
============================================================

A typical synthesized design can contain both combinational
and sequential elements.

For example:

    A ──────┐
            │
            ▼
        Combinational
           Logic
            │
    B ──────┘
            │
            ▼
          DFF
            │
            ▼
            Q


Control signals such as:

    SELECT
    CLOCK
    RESET


may also affect the structure.

This demonstrates an important distinction:

    Combinational Logic
        ↓
    Current input dependent

    Sequential Logic
        ↓
    Previous state dependent


============================================================
8. SEQUENTIAL LOGIC
============================================================

Sequential circuits contain storage elements.

Unlike combinational circuits, their outputs can depend on
previously stored information.

The most common storage element introduced in this module is
the D flip-flop.

Basic behavior:

    At active clock edge:

        Q ← D


Conceptually:

             ┌─────────┐
    D ──────►│   DFF   │──────► Q
             │         │
    CLK ────►│         │
             └─────────┘


D flip-flops are widely used in:

- Registers
- Counters
- Pipelines
- Shift registers
- State machines
- Data storage


============================================================
9. D FLIP-FLOP WITH ASYNCHRONOUS RESET
============================================================

An asynchronous reset can force the output to a known value
without waiting for a clock edge.

Example RTL:

```verilog
module dff_async_reset (
    input clk,
    input reset,
    input d,
    output reg q
);

always @(posedge clk or posedge reset)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end

endmodule
The operation is:

reset = 1
    ↓
q = 0

When reset is inactive:

Rising edge of CLK
    ↓
q receives d

The reset therefore has priority over normal data capture.

The conceptual structure is:

             RESET
               │
               ▼
D ─────────► DFF ─────────► Q
              ▲
              │
             CLK

During synthesis, the RTL can be mapped to a standard-cell
flip-flop that supports asynchronous reset.

============================================================
10. D FLIP-FLOP WITH ASYNCHRONOUS SET

An asynchronous set forces the output to logic 1.

Example RTL:

module dff_async_set (
    input clk,
    input set,
    input d,
    output reg q
);

always @(posedge clk or posedge set)
begin
    if (set)
        q <= 1'b1;
    else
        q <= d;
end

endmodule

The operation is:

set = 1
   ↓
q = 1

When set is inactive, the flip-flop operates normally.

Rising edge of CLK
    ↓
q receives d

The asynchronous set therefore provides a direct control over
the stored output.

============================================================
11. SIMULATION AND WAVEFORM VERIFICATION

Simulation is used to verify the sequential designs before
and after synthesis.

Important signals include:

CLK
D
RESET
SET
Q

For an asynchronous-reset DFF, the waveform should show:

RESET active
    ↓
Q becomes 0

When reset is released:

CLK edge
    ↓
Q captures D

For an asynchronous-set DFF:

SET active
   ↓
Q becomes 1

Waveforms provide visual evidence of the expected behavior.

The verification process is:

RTL
 ↓
Testbench
 ↓
Simulation
 ↓
Waveform
 ↓
Functional Verification
============================================================
12. HIERARCHICAL RTL DESIGN

Large digital systems are usually divided into smaller
modules.

Instead of writing the entire design in one block, individual
functions can be separated into submodules.

Example:

              TOP MODULE
                  │
         ┌────────┴────────┐
         │                 │
         ▼                 ▼
    SUB MODULE 1      SUB MODULE 2
         │                 │
         └────────┬────────┘
                  │
                  ▼
                OUTPUT

A hierarchical design provides:

Better organization
Reusable modules
Easier debugging
Independent verification
Improved readability
Scalable design development

Example structure:

module sub_module1 (
    input a,
    input b,
    output y
);

assign y = a & b;

endmodule

Another module can use its output:

module sub_module2 (
    input x,
    input c,
    output y
);

assign y = x | c;

endmodule

A top-level module can connect these blocks together.

This structure demonstrates how multiple RTL modules form one
larger hardware system.

============================================================
13. MULTIPLIER SYNTHESIS

Arithmetic operators can also be described at RTL.

For example:

module multiplier (
    input  [3:0] a,
    input  [3:0] b,
    output [7:0] product
);

assign product = a * b;

endmodule

The multiplication operator describes the required arithmetic
function.

During synthesis, the tool converts this behavior into an
appropriate hardware implementation.

Conceptually:

   A
   │
   ▼
Multiplier
   ▲
   │
   B
   │
   ▼
Product

The resulting structure can contain:

AND operations
Partial-product logic
Addition structures
Carry logic
Standard cells

This experiment demonstrates that arithmetic RTL can also be
converted into structural hardware.

============================================================
14. OVERALL SYNTHESIS FLOW

The experiments in Module 2 can be combined into one general
flow:

         RTL DESIGN
              │
              ▼
         RTL ANALYSIS
              │
              ▼
            YOSYS
              │
              ▼
         RTL PROCESSING
              │
              ▼
          OPTIMIZATION
              │
              ▼
    STANDARD CELL LIBRARY
              │
              ▼
    TECHNOLOGY MAPPING
              │
              ▼
        NETLIST
              │
              ▼
    STRUCTURAL ANALYSIS

For sequential designs:

         RTL DFF
            ↓
      RTL Simulation
            ↓
         Synthesis
            ↓
      Standard DFF Cell
            ↓
         Netlist
            ↓
      Gate-Level View
============================================================
15. EXPERIMENTAL OBSERVATIONS
Topic	Observation
RTL Synthesis	Converts behavioral RTL into structural hardware
Yosys	Performs synthesis and optimization
Liberty File	Describes standard-cell characteristics
Standard Cells	Provide technology-specific implementations
DFF	Stores one bit of sequential information
Async Reset	Forces a known reset state independently of clock
Async Set	Forces a known set state independently of clock
Waveform	Shows the behavior of sequential signals
Hierarchy	Divides a large design into smaller modules
Multiplier	Demonstrates synthesis of arithmetic logic

Important points learned from the experiments:

RTL describes the intended functionality.
Synthesis determines a hardware implementation for that
functionality.
Standard-cell libraries provide the available hardware
building blocks.
Liberty files provide information needed for cell mapping.
Sequential logic requires storage elements such as DFFs.
Asynchronous controls can operate independently of the
clock.
Hierarchical design makes complex RTL easier to manage.
Arithmetic operators can also be synthesized into hardware.
============================================================
16. CONCLUSION

Module 2 provides a deeper understanding of RTL synthesis and
sequential hardware.

The module explains how Yosys processes RTL and how a standard
cell library is used to transform logical descriptions into
technology-specific implementations.

The D flip-flop experiments introduce important sequential
concepts including asynchronous reset and asynchronous set.

Hierarchical RTL demonstrates how multiple modules can be
combined into a larger system, while multiplier synthesis
shows how arithmetic operations can be converted into hardware.

The main learning flow is:

RTL
 ↓
Yosys
 ↓
RTL Processing
 ↓
Optimization
 ↓
Standard Cell Library
 ↓
Technology Mapping
 ↓
Synthesized Netlist