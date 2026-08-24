============================================================
                    MODULE 5
============================================================

# Module 5 — Synthesis-Oriented RTL Coding and Hardware Inference

## Overview

Module 5 deals with RTL coding techniques that directly affect
the hardware inferred by synthesis tools.

The experiments in this module mainly focus on combinational
logic descriptions using `if`, `else if`, `case`, `generate`
constructs, multiplexers, demultiplexers and a ripple-carry
adder.

A major concept studied in this module is the difference
between complete and incomplete combinational descriptions.

When a combinational output is not assigned for every possible
condition, synthesis may infer a latch to retain the previous
value.

The main design flow is:

    RTL Coding
        ↓
    RTL Simulation
        ↓
    Synthesis
        ↓
    Hardware Inference
        ↓
    Netlist Inspection
        ↓
    Verification


============================================================
TABLE OF CONTENTS
============================================================

1. Module Objectives
2. Introduction to Synthesis-Oriented RTL
3. Combinational Logic and Latch Inference
4. Incomplete IF Statement
5. Incomplete IF-ELSE IF Statement
6. Incomplete CASE Statement
7. Complete CASE Description
8. MUX Implementation
9. DEMUX Using CASE
10. DEMUX Using Generate
11. Generate-Based Hardware Structures
12. Ripple-Carry Adder
13. Simulation and Waveform Verification
14. Synthesis Analysis
15. Experimental Observations
16. Key Learning Points
17. Conclusion


============================================================
1. MODULE OBJECTIVES
============================================================

The main objectives of Module 5 are:

- Understand synthesis-oriented RTL coding.
- Identify incomplete combinational descriptions.
- Understand why incomplete assignments can infer latches.
- Study the difference between incomplete and complete
  `if` statements.
- Understand incomplete and complete `case` statements.
- Learn how MUX hardware can be inferred from RTL.
- Study DEMUX implementation using `case`.
- Understand the use of `generate` constructs.
- Describe repeated hardware structures using generate loops.
- Study a generate-based ripple-carry adder.
- Compare RTL simulation with synthesized hardware.
- Understand how coding style influences hardware inference.


============================================================
2. INTRODUCTION TO SYNTHESIS-ORIENTED RTL
============================================================

RTL is not only a description of functionality.

The way the RTL is written can also influence the hardware
that synthesis tools infer.

For example, a combinational block should normally assign
an output for every possible input condition.

Consider:

    always @(*)
    begin
        if (sel)
            y = a;
    end


When `sel` is zero, the output `y` is not assigned.

The hardware must somehow preserve the previous value of `y`.

Therefore, synthesis may infer a latch.

This can be represented as:

    Incomplete RTL
         ↓
    Missing Assignment
         ↓
    Storage Required
         ↓
       LATCH


If combinational behavior is intended, the RTL should provide
a value for every possible condition.


============================================================
3. COMBINATIONAL LOGIC AND LATCH INFERENCE
============================================================

Combinational logic should depend only on present input values.

Examples include:

- Multiplexers
- Demultiplexers
- Decoders
- Encoders
- Arithmetic combinational blocks
- Comparators


A correctly described combinational block follows:

    Inputs
      ↓
    Combinational Logic
      ↓
    Output


There should be no dependency on a previous output value.

An incomplete assignment changes the behavior:

    Inputs
      ↓
    Conditional Logic
      ↓
    Output
      ↑
      │
    Previous Value


This feedback-like behavior represents storage.

Therefore, an incomplete combinational description can lead
to latch inference.


============================================================
4. INCOMPLETE IF STATEMENT
============================================================

Consider the following RTL:

    module incomp_if (
        input i0,
        input i1,
        input i2,
        output reg y
    );

    always @(*)
    begin
        if (i0)
            y = i1;
    end

    endmodule


Here:

    i0 → control signal
    i1 → data signal
    y  → output


When:

    i0 = 1

the output becomes:

    y = i1


However, when:

    i0 = 0

there is no assignment to `y`.

Therefore, the output must retain its previous value.

This behavior can result in latch inference during synthesis.


The intended combinational implementation would normally
require an explicit assignment for the other condition.


For example:

    always @(*)
    begin
        if (i0)
            y = i1;
        else
            y = 0;
    end


Now every condition produces an output.


============================================================
5. INCOMPLETE IF-ELSE IF STATEMENT
============================================================

A second example contains an `if` followed by an
`else if`.

Example:

    module incomp_if2 (
        input i0,
        input i1,
        input i2,
        input i3,
        output reg y
    );

    always @(*)
    begin
        if (i0)
            y = i1;
        else if (i2)
            y = i3;
    end

    endmodule


The first condition is:

    i0 = 1 → y = i1


If `i0 = 0`, the second condition is evaluated:

    i2 = 1 → y = i3


But when:

    i0 = 0
    i2 = 0


there is no assignment to `y`.

Therefore, the previous value of `y` must be retained.

Synthesis can infer a latch to represent this behavior.


A complete version can provide a final `else`:

    always @(*)
    begin
        if (i0)
            y = i1;
        else if (i2)
            y = i3;
        else
            y = 0;
    end


This removes the incomplete assignment and describes
combinational behavior.


============================================================
6. INCOMPLETE CASE STATEMENT
============================================================

A `case` statement can also become incomplete.

Example:

    module incomp_case (
        input i0,
        input i1,
        input i2,
        input [1:0] sel,
        output reg y
    );

    always @(*)
    begin
        case (sel)
            2'b00: y = i0;
            2'b01: y = i1;
        endcase
    end

    endmodule


The selector has four possible values:

    00
    01
    10
    11


But the RTL only specifies:

    00 → i0
    01 → i1


The following cases are missing:

    10
    11


When either missing condition occurs, `y` is not assigned.

Therefore, synthesis can infer storage behavior.


This is an example of how incomplete RTL can directly affect
the synthesized hardware.


============================================================
7. COMPLETE CASE DESCRIPTION
============================================================

A complete `case` statement assigns the output for every
required condition.

Example:

    module comp_case (
        input i0,
        input i1,
        input i2,
        input [1:0] sel,
        output reg y
    );

    always @(*)
    begin
        case (sel)
            2'b00: y = i0;
            2'b01: y = i1;
            default: y = i2;
        endcase
    end

    endmodule


The `default` branch handles all selector values that are not
explicitly listed.

Therefore:

    sel = 00 → y = i0

    sel = 01 → y = i1

    sel = 10 → y = i2

    sel = 11 → y = i2


The output receives a value for every selector combination.

This allows the circuit to be implemented as combinational
logic without requiring an unintended latch.


============================================================
8. MUX IMPLEMENTATION
============================================================

A multiplexer selects one input from several available inputs.

A MUX can be implemented using:

- Conditional operators
- `if` statements
- `case` statements
- Generate structures


In this module, a generate-based MUX is studied.

The basic concept is:

             ┌──────────┐
    Inputs ─►│          │
             │   MUX    │────► Output
    Select ─►│          │
             └──────────┘


The selector determines which input reaches the output.

For a 2:1 MUX:

    sel = 0 → y = i0

    sel = 1 → y = i1


For a larger MUX, multiple selection conditions may be
required.


============================================================
9. DEMUX USING CASE
============================================================

A demultiplexer performs the opposite routing operation of a
multiplexer.

It takes one input and directs it to one of several outputs.

For an 8-output DEMUX:

    Data Input
         │
         ▼
      DEMUX
         │
    ┌────┼────┬────┬────┬────┬────┬────┬────┐
    ▼    ▼    ▼    ▼    ▼    ▼    ▼    ▼    ▼
   y0   y1   y2   y3   y4   y5   y6   y7


A 3-bit selector can select one of eight outputs.

Possible selector values are:

    000
    001
    010
    011
    100
    101
    110
    111


A `case` statement can be used to describe the selection.


Conceptually:

    case(sel)

        3'b000 → y0 = d
        3'b001 → y1 = d
        3'b010 → y2 = d
        ...
        3'b111 → y7 = d

    endcase


All unused outputs should normally be assigned appropriate
default values so that unintended storage is not inferred.


============================================================
10. DEMUX USING GENERATE
============================================================

The same type of repeated hardware can also be described
using a generate structure.

Generate constructs are useful when the same hardware pattern
must be repeated multiple times.

Instead of writing each output condition separately, a loop
can describe the repeated structure.

Conceptually:

    for each output:

        output[i] = data & selection_condition


The generate structure allows the RTL to represent a larger
hardware arrangement in a compact and scalable form.


Advantages include:

- Reduced repetitive code
- Easier modification
- Better scalability
- Clear structural representation
- Useful for parameterized designs


============================================================
11. GENERATE-BASED HARDWARE STRUCTURES
============================================================

The `generate` construct is evaluated during elaboration.

It is commonly used to create repeated hardware structures.

Typical syntax:

    genvar i;

    generate
        for (i = 0; i < N; i = i + 1)
        begin
            // Repeated hardware
        end
    endgenerate


Generate constructs do not represent software loops that
execute during circuit operation.

Instead, they describe multiple instances of hardware.


For example:

    generate
        for (i = 0; i < 8; i = i + 1)
        begin
            // hardware instance
        end
    endgenerate


This results in multiple hardware structures after
elaboration.


============================================================
12. RIPPLE-CARRY ADDER
============================================================

The module also demonstrates an 8-bit ripple-carry adder
using a generate structure.

A ripple-carry adder is constructed by connecting several
full adders together.

Each full adder receives:

    A
    B
    Carry-In


and produces:

    Sum
    Carry-Out


The structure is:

        FA0
         │
    C0 ──┤
         └── C1
             │
            FA1
             │
             └── C2
                  .
                  .
                  .
                 FA7
                  │
                  ▼
                 C8


For an 8-bit design:

    A[7:0]
    B[7:0]
    Cin


produce:

    Sum[7:0]
    Cout


The carry propagates from the least significant bit toward
the most significant bit.

Hence the name:

    Ripple-Carry Adder


A typical structural arrangement contains intermediate
signals such as:

    int_sum
    int_co


The generate loop can instantiate the repeated full-adder
stages.


Conceptually:

    FA0 → FA1 → FA2 → FA3 → FA4 → FA5 → FA6 → FA7

      Carry propagates from one stage to the next.


The main advantage of using `generate` is that the repeated
structure can be described without manually writing every
full-adder connection.


============================================================
13. SIMULATION AND WAVEFORM VERIFICATION
============================================================

Simulation is used to verify the functionality of each RTL
design.

The basic verification flow is:

    RTL
     ↓
    Testbench
     ↓
    Input Stimulus
     ↓
    Simulation
     ↓
    Waveform
     ↓
    Functional Verification


For incomplete combinational examples, the waveform can
demonstrate the effect of missing assignments.

For MUX experiments, observe:

    Input signals
    Select signal
    Output signal


For DEMUX experiments, observe:

    Data input
    Select lines
    Output lines


For the ripple-carry adder, observe:

    A
    B
    Cin
    Sum
    Cout


The waveform should match the expected logical operation.


============================================================
14. SYNTHESIS ANALYSIS
============================================================

After simulation, the RTL can be synthesized.

The synthesis tool examines the RTL and determines the
hardware required to implement the described behavior.

The flow is:

    RTL
     ↓
    Elaboration
     ↓
    Logic Inference
     ↓
    Optimization
     ↓
    Technology Mapping
     ↓
    Netlist


For incomplete combinational RTL:

    Incomplete Assignment
          ↓
       Latch
          ↓
    Synthesized Storage


For complete combinational RTL:

    Complete Assignment
          ↓
    Combinational Logic
          ↓
    No Unintended Latch


For generate-based structures:

    Generate RTL
          ↓
    Elaboration
          ↓
    Multiple Hardware Instances
          ↓
    Synthesized Structure


============================================================
15. EXPERIMENTAL OBSERVATIONS
============================================================

| Experiment | Main Observation |
|------------|------------------|
| Incomplete IF | Missing assignment can infer latch |
| Incomplete IF-ELSE IF | Missing final branch can create storage |
| Incomplete CASE | Unspecified selector values can infer latch |
| Complete CASE | Default branch provides complete assignment |
| MUX Generate | Repeated selection logic can be generated |
| DEMUX CASE | Selector routes input to selected output |
| DEMUX Generate | Repeated DEMUX logic can be generated |
| Ripple Carry Adder | Full adders can be connected using generate |
| Simulation | Confirms RTL functionality |
| Synthesis | Shows the hardware inferred from RTL |


Important observations:

1. RTL coding style affects synthesized hardware.

2. Incomplete combinational descriptions can produce latches.

3. A complete assignment prevents unintended storage.

4. `default` is useful when all remaining `case` conditions
   should receive a defined output.

5. Generate constructs simplify repetitive structural RTL.

6. MUX and DEMUX hardware can be inferred from behavioral
   descriptions.

7. Ripple-carry adders are naturally represented as repeated
   full-adder structures.

8. Simulation should be performed before relying on synthesis
   results.


============================================================
16. KEY LEARNING POINTS
============================================================

The major concepts covered in Module 5 are:

### Combinational RTL

- Complete assignments
- Incomplete assignments
- Latch inference
- `if` statements
- `else if` statements
- `case` statements
- `default` branches


### MUX and DEMUX

- Multiplexer operation
- Demultiplexer operation
- Selection logic
- Case-based implementation
- Generate-based implementation


### Generate Constructs

- `genvar`
- Generate loops
- Repeated hardware structures
- Structural RTL
- Scalable hardware descriptions


### Arithmetic Hardware

- Full-adder structure
- Carry propagation
- Ripple-carry addition
- Intermediate carry signals


### Synthesis

- RTL elaboration
- Logic inference
- Optimization
- Technology mapping
- Netlist generation


============================================================
17. CONCLUSION
============================================================

Module 5 demonstrates how RTL coding decisions influence the
hardware generated by synthesis.

The incomplete `if` and `case` experiments show that failing
to assign an output under every required condition can cause
the synthesis tool to infer a latch.

The complete `case` experiment demonstrates how providing
a `default` condition can describe a fully specified
combinational function.

MUX and DEMUX experiments show different methods of describing
selection and routing hardware.

Generate constructs provide an efficient method for creating
repeated hardware structures without writing every instance
manually.

The ripple-carry adder experiment further demonstrates how
generate loops can be used to construct a multi-stage
arithmetic circuit.

The complete learning process can be summarized as:

    RTL Coding
        ↓
    Simulation
        ↓
    Synthesis
        ↓
    Hardware Inference
        ↓
    Netlist Analysis
        ↓
    Verification


The key lesson from Module 5 is:

    "The RTL description determines not only the intended
     functionality, but also strongly influences the
     hardware structure inferred during synthesis."

Proper combinational coding, complete assignments and
appropriate use of structural constructs are therefore
essential for producing predictable and efficient digital
hardware.


============================================================
                  END OF MODULE 5
============================================================