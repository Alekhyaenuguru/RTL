============================================================
                    MODULE 4
============================================================

# Module 4 — Gate-Level Simulation and RTL Coding Practices

## Overview

Module 4 focuses on important RTL coding and synthesis
concepts that affect both simulation behavior and the
resulting hardware implementation.

The module mainly studies:

- MUX coding styles
- Ternary operator implementation
- Combinational sensitivity lists
- Blocking assignments
- Non-blocking assignments
- RTL simulation
- Synthesis
- Gate-Level Simulation
- RTL versus synthesized behavior

The experiments demonstrate that two RTL descriptions that
appear similar can produce different simulation behavior
depending on the coding style.

The overall flow is:

    RTL Coding
        ↓
    RTL Simulation
        ↓
    Synthesis
        ↓
    Gate-Level Netlist
        ↓
    Gate-Level Simulation
        ↓
    Waveform Verification


============================================================
TABLE OF CONTENTS
============================================================

1. Module Objectives
2. Introduction
3. RTL Coding Styles
4. MUX Using Ternary Operator
5. MUX Using Procedural Logic
6. Incomplete Sensitivity List
7. Correct Combinational Sensitivity
8. Blocking Assignment
9. Non-Blocking Assignment
10. Blocking vs Non-Blocking
11. Gate-Level Simulation
12. RTL-to-GLS Verification Flow
13. Waveform Analysis
14. Experimental Observations
15. Key Learning Points
16. Conclusion


============================================================
1. MODULE OBJECTIVES
============================================================

The objectives of this module are:

- Understand different ways of describing combinational
  hardware in Verilog.
- Study the ternary operator for MUX implementation.
- Understand the importance of sensitivity lists.
- Identify simulation problems caused by incomplete
  sensitivity lists.
- Understand blocking assignments.
- Understand non-blocking assignments.
- Compare blocking and non-blocking procedural behavior.
- Understand the purpose of Gate-Level Simulation.
- Study the transition from RTL simulation to GLS.
- Compare RTL behavior with synthesized hardware.


============================================================
2. INTRODUCTION
============================================================

RTL is used to describe the required behavior of a digital
circuit.

After writing RTL, the design normally passes through
different stages:

    RTL Development
          ↓
    Functional Simulation
          ↓
    Synthesis
          ↓
    Netlist Generation
          ↓
    Gate-Level Simulation


RTL simulation verifies the behavior of the original
description.

Synthesis converts the RTL into hardware structures.

Gate-Level Simulation then verifies the synthesized
implementation.


This module is important because simulation behavior is
affected not only by the intended logic but also by the
way that logic is written in Verilog.


============================================================
3. RTL CODING STYLES
============================================================

Combinational logic can be described using several Verilog
coding styles.

Common approaches include:

1. Continuous assignment
2. Ternary operator
3. Procedural `always` block
4. Case-based logic


For example, a MUX can be written using:

    assign y = sel ? i1 : i0;


or:

    always @(*)
    begin
        if (sel)
            y = i1;
        else
            y = i0;
    end


Both descriptions represent the same basic MUX behavior
when written correctly.


============================================================
4. MUX USING TERNARY OPERATOR
============================================================

A 2:1 multiplexer can be described using the conditional
operator.

RTL:

    module ternary_operator_mux (
        input i0,
        input i1,
        input sel,
        output y
    );

    assign y = sel ? i1 : i0;

    endmodule


The operation is:

    sel = 0 → y = i0

    sel = 1 → y = i1


The ternary operator provides a compact way of describing
selection logic.

The corresponding conceptual structure is:

                 ┌───────────┐
    i0 ─────────►│           │
                 │   2:1     │────► y
    i1 ─────────►│    MUX    │
                 │           │
    sel ────────►│           │
                 └───────────┘


During synthesis, the RTL may be represented using a
technology-specific MUX cell or equivalent gate structure.


============================================================
5. MUX USING PROCEDURAL LOGIC
============================================================

The same MUX can be written using an `always` block.

Example:

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


The `if` statement describes the selection condition.

When `sel` is high:

    y = i1


When `sel` is low:

    y = i0


Because both branches assign the output, the design describes
pure combinational behavior.


============================================================
6. INCOMPLETE SENSITIVITY LIST
============================================================

Consider the following RTL:

    module bad_mux (
        input i0,
        input i1,
        input sel,
        output reg y
    );

    always @(sel)
    begin
        if (sel)
            y = i1;
        else
            y = i0;
    end

    endmodule


The block uses three input signals:

    i0
    i1
    sel


However, only `sel` is included in the sensitivity list:

    always @(sel)


This creates a simulation problem.

If `i0` changes while `sel` remains unchanged, the
`always` block is not triggered.

Similarly, if `i1` changes while `sel` remains unchanged,
the output may not update immediately in RTL simulation.


The problem can therefore be represented as:

    i0 ──────┐
             │
    i1 ──────┼──► MUX ───► y
             │
    sel ─────┘


All three signals affect the output, so the combinational
process must respond to changes in all of them.


============================================================
7. CORRECT COMBINATIONAL SENSITIVITY
============================================================

For combinational procedural logic, a common solution is:

    always @(*)
    begin
        if (sel)
            y = i1;
        else
            y = i0;
    end


The `@(*)` construct automatically includes the signals
read by the procedural block.

Therefore, changes in:

    i0
    i1
    sel


can trigger the block.


The improved flow is:

    Input Changes
         ↓
    always @(*)
         ↓
    Recalculate Logic
         ↓
    Update Output


This helps ensure that RTL simulation correctly represents
the intended combinational behavior.


============================================================
8. BLOCKING ASSIGNMENT
============================================================

Blocking assignment uses the `=` operator.

Example:

    always @(*)
    begin
        d = a & b;
        x = d & c;
    end


The first statement executes:

    d = a & b


The next statement then uses the newly assigned value of `d`:

    x = d & c


Blocking assignments execute sequentially within the
procedural block.


Conceptually:

    a ──┐
        AND ──► d ──┐
    b ──┘           AND ──► x
                    │
    c ──────────────┘


Blocking assignments are commonly used when describing
combinational logic.


============================================================
9. NON-BLOCKING ASSIGNMENT
============================================================

Non-blocking assignment uses the `<=` operator.

Example:

    always @(posedge clk)
    begin
        q <= d;
    end


The non-blocking assignment schedules the update rather
than immediately changing the variable during the current
procedural execution.

This behavior is useful for modeling sequential hardware.


A typical register description is:

    always @(posedge clk)
    begin
        q <= d;
    end


At the active clock edge:

    D
    ↓
    Flip-Flop
    ↓
    Q


The output changes according to the clocked behavior.


============================================================
10. BLOCKING VS NON-BLOCKING
============================================================

Blocking and non-blocking assignments have different
simulation semantics.

| Property | Blocking | Non-Blocking |
|----------|----------|--------------|
| Operator | `=` | `<=` |
| Update behavior | Immediate procedural update | Scheduled update |
| Common usage | Combinational logic | Sequential logic |
| Clocked logic | Generally avoided | Normally preferred |
| Procedural order | Important | Updates occur after evaluation |

A common RTL coding guideline is:

    Combinational Logic
            ↓
       Blocking `=`

    Sequential Logic
            ↓
      Non-Blocking `<=`


Example combinational block:

    always @(*)
    begin
        y = a & b;
    end


Example sequential block:

    always @(posedge clk)
    begin
        q <= d;
    end


Using the appropriate assignment style helps produce
predictable RTL simulation and clearer hardware intent.


============================================================
11. GATE-LEVEL SIMULATION
============================================================

Gate-Level Simulation, commonly called GLS, simulates the
synthesized gate-level netlist instead of the original RTL.

RTL simulation uses behavioral descriptions.

GLS uses the structural netlist generated after synthesis.


The difference can be represented as:

    RTL Simulation

    RTL
     ↓
    Testbench
     ↓
    Simulator
     ↓
    Waveform


    Gate-Level Simulation

    RTL
     ↓
    Synthesis
     ↓
    Gate-Level Netlist
     ↓
    Cell Models
     ↓
    Simulator
     ↓
    Waveform


GLS therefore provides an additional verification step
after synthesis.


============================================================
12. RTL-TO-GLS VERIFICATION FLOW
============================================================

The complete flow can be divided into several stages.


STEP 1 — RTL DEVELOPMENT

The required circuit functionality is written using
Verilog RTL.


STEP 2 — RTL SIMULATION

A testbench applies input stimulus and verifies the
expected behavior.


STEP 3 — SYNTHESIS

The RTL is processed by a synthesis tool such as Yosys.


STEP 4 — NETLIST GENERATION

The synthesis tool produces a gate-level representation
of the design.


STEP 5 — GATE-LEVEL SIMULATION

The generated netlist is simulated using appropriate
standard-cell models.


STEP 6 — WAVEFORM COMPARISON

The GLS waveform is compared with the expected RTL
behavior.


The complete process is:

    RTL
     ↓
    RTL Simulation
     ↓
    Synthesis
     ↓
    Gate-Level Netlist
     ↓
    GLS
     ↓
    Waveform
     ↓
    Verification


============================================================
13. WAVEFORM ANALYSIS
============================================================

Waveforms are important for verifying the behavior of
both RTL and gate-level designs.

For the MUX experiments, observe:

    i0
    i1
    sel
    y


The expected relationship is:

    sel = 0 → y = i0

    sel = 1 → y = i1


For the bad sensitivity-list experiment, the waveform can
show that the output does not always respond to changes
in `i0` or `i1` when `sel` remains unchanged.


For the GLS experiment, the waveform demonstrates the
behavior of the synthesized netlist.


The waveform analysis process is:

    Input Stimulus
          ↓
    Signal Transitions
          ↓
    Output Response
          ↓
    Expected vs Actual
          ↓
    Functional Verification


============================================================
14. EXPERIMENTAL OBSERVATIONS
============================================================

| Experiment | Main Concept |
|------------|--------------|
| Ternary MUX | Compact MUX description |
| Procedural MUX | Combinational `always` block |
| Bad MUX | Incomplete sensitivity list |
| Correct MUX | Complete combinational sensitivity |
| Blocking Assignment | Immediate procedural update |
| Non-Blocking Assignment | Scheduled sequential update |
| Synthesis | RTL converted to structural hardware |
| GLS | Synthesized netlist simulation |
| Waveform | Functional behavior verification |


Important observations:

1. A MUX can be described using different RTL styles.

2. Correct sensitivity lists are important for combinational
   simulation.

3. An incomplete sensitivity list can cause simulation
   behavior that does not represent the intended hardware.

4. Blocking assignments update procedural variables
   immediately.

5. Non-blocking assignments are generally used to model
   clocked sequential behavior.

6. Synthesis converts RTL into a structural implementation.

7. Gate-Level Simulation verifies the synthesized netlist.


============================================================
15. KEY LEARNING POINTS
============================================================

The major concepts covered in this module are:

### MUX Coding

- Ternary operator
- Procedural `if` implementation
- Combinational selection


### Sensitivity Lists

- Importance of complete sensitivity
- Problems caused by missing signals
- Use of `always @(*)`


### Procedural Assignments

- Blocking assignment
- Non-blocking assignment
- Simulation ordering
- Sequential modeling


### Gate-Level Verification

- Synthesized netlist
- Standard-cell models
- Gate-Level Simulation
- RTL versus GLS comparison


============================================================
16. CONCLUSION
============================================================

Module 4 demonstrates that RTL coding style has a direct
impact on simulation behavior and the reliability of the
synthesis flow.

The MUX experiments show multiple ways of describing the
same combinational function.

The sensitivity-list experiment demonstrates how an
incomplete sensitivity list can cause an RTL simulation
mismatch.

The blocking and non-blocking experiments explain why
different procedural assignment operators are normally
used for different types of logic.

Finally, Gate-Level Simulation extends verification beyond
RTL by checking the behavior of the synthesized hardware
representation.

The complete learning flow is:

    RTL Coding
        ↓
    RTL Simulation
        ↓
    Synthesis
        ↓
    Gate-Level Netlist
        ↓
    Gate-Level Simulation
        ↓
    Waveform Analysis
        ↓
    Final Verification


The key lesson from Module 4 is:

    "Correct RTL coding is essential for achieving
     predictable simulation results and reliable
     synthesized hardware."


============================================================
END OF MODULE 4
============================================================