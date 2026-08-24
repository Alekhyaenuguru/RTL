============================================================
                    MODULE 3
============================================================

# Module 3 — Combinational and Sequential Logic Optimization

## Overview

Module 3 focuses on the optimization capabilities of RTL
synthesis tools.

The main purpose of synthesis optimization is to obtain a
simpler and more efficient hardware implementation while
preserving the required functionality of the original RTL.

In this module, different RTL examples are analyzed to
understand how synthesis tools identify:

- Redundant logic
- Constant values
- Unnecessary conditions
- Unused sequential elements
- Unnecessary counter bits
- Simplifiable Boolean expressions

The experiments cover both combinational and sequential
optimization.

The overall concept is:

    RTL Description
          ↓
    RTL Analysis
          ↓
    Logic Optimization
          ↓
    Technology Mapping
          ↓
    Optimized Hardware


============================================================
TABLE OF CONTENTS
============================================================

1. Module Objectives
2. Introduction to Logic Optimization
3. Need for RTL Optimization
4. Combinational Logic Optimization
5. Optimization Check 1
6. Optimization Check 2
7. Optimization Check 3
8. Optimization Check 4
9. Sequential Logic Optimization
10. DFF Constant Optimization
11. Counter Optimization
12. Simulation and Verification
13. RTL-to-Optimized Hardware Flow
14. Experimental Observations
15. Key Learning Points
16. Conclusion


============================================================
1. MODULE OBJECTIVES
============================================================

The main objectives of this module are:

- Understand the concept of synthesis optimization.
- Study optimization of combinational RTL.
- Understand Boolean simplification performed by synthesis.
- Observe the effect of constant values in RTL.
- Study constant propagation.
- Understand optimization of flip-flop based logic.
- Analyze unnecessary sequential hardware.
- Study counter optimization.
- Compare RTL behavior with synthesized logic.
- Understand why the synthesized circuit may be simpler than
  the original RTL description.


============================================================
2. INTRODUCTION TO LOGIC OPTIMIZATION
============================================================

Logic optimization is an important stage of digital synthesis.

An RTL description may contain logic that is not actually
required in the final implementation.

A synthesis tool analyzes the design and attempts to remove
or simplify such logic while maintaining the same observable
functionality.

Examples of optimization include:

- Boolean simplification
- Constant propagation
- Common logic removal
- Redundant logic elimination
- Unused signal removal
- Sequential optimization
- State simplification
- Counter optimization


A simplified synthesis process is:

    RTL
     ↓
    Elaborate Design
     ↓
    Analyze Logic
     ↓
    Optimize
     ↓
    Technology Mapping
     ↓
    Final Netlist


The optimized design may contain fewer gates or fewer storage
elements than the original RTL description.


============================================================
3. NEED FOR RTL OPTIMIZATION
============================================================

Optimization is important because hardware resources are
limited.

A simpler implementation can provide advantages such as:

- Reduced area
- Lower power consumption
- Lower switching activity
- Reduced logic complexity
- Potentially improved timing
- Smaller synthesized netlist
- Easier physical implementation


For example, consider:

    y = a ? b : 0


The expression can be simplified because when `a` is zero,
the output is always zero.

The expression is equivalent to:

    y = a & b


Therefore, the synthesis tool does not necessarily need to
implement the original conditional structure exactly as
written.


============================================================
4. COMBINATIONAL LOGIC OPTIMIZATION
============================================================

Combinational logic produces outputs based only on the current
input values.

Examples include:

- AND gates
- OR gates
- Multiplexers
- Decoders
- Encoders
- Comparators


Synthesis tools can analyze combinational expressions and
replace complex logic with simpler equivalent structures.

The optimization process may include:

    Boolean Expression
           ↓
    Constant Analysis
           ↓
    Logic Simplification
           ↓
    Reduced Gate Structure


The following experiments demonstrate this behavior.


============================================================
5. OPTIMIZATION CHECK 1
============================================================

The first experiment uses a conditional expression containing
a constant value.

RTL:

    module opt_check (
        input a,
        input b,
        output y
    );

    assign y = a ? b : 0;

    endmodule


Functional behavior:

    a = 0 → y = 0

    a = 1 → y = b


The expression can be represented as:

    y = a & b


Therefore, synthesis can reduce the original conditional
description to simpler combinational logic.

### Important Observation

Although the RTL contains a conditional operator, the final
hardware does not necessarily require a complete MUX.

The constant branch allows the synthesis tool to simplify the
logic.


============================================================
6. OPTIMIZATION CHECK 2
============================================================

The second experiment uses a constant value in the true branch.

RTL:

    module opt_check2 (
        input a,
        input b,
        output y
    );

    assign y = a ? 1 : b;

    endmodule


The behavior is:

    a = 1 → y = 1

    a = 0 → y = b


This expression is equivalent to:

    y = a | b


Therefore, the synthesis tool can replace the conditional
expression with an equivalent OR-based implementation.


### Optimization Concept

Original RTL:

    a ? 1 : b


Equivalent logic:

    a | b


This experiment demonstrates Boolean simplification and
constant propagation.


============================================================
7. OPTIMIZATION CHECK 3
============================================================

The third experiment contains nested conditional expressions.

RTL:

    module opt_check3 (
        input a,
        input b,
        input c,
        output y
    );

    assign y = a ? (c ? b : 0) : 0;

    endmodule


The expression contains two levels of selection.

The synthesis tool examines the relationship between:

    a
    b
    c


The outer condition determines whether the output can be
non-zero.

When:

    a = 0

the output is immediately:

    y = 0


When:

    a = 1

the value of `c` determines whether `b` reaches the output.

This allows the synthesis tool to simplify the nested
conditional structure.


### Main Concept

Nested RTL conditions do not necessarily result in an equally
complex hardware implementation.

Synthesis analyzes the complete Boolean relationship and
produces an equivalent optimized circuit.


============================================================
8. OPTIMIZATION CHECK 4
============================================================

The fourth experiment contains a more complex conditional
expression.

RTL:

    module opt_check4 (
        input a,
        input b,
        input c,
        output y
    );

    assign y = a ? (b ? (a & c) : c) : (!c);

    endmodule


This example contains:

- Nested conditional operators
- AND operation
- NOT operation
- Multiple control conditions


The synthesis tool analyzes the different possible input
conditions and attempts to remove unnecessary logic.

The experiment demonstrates that optimization becomes more
important as RTL expressions become more complicated.


### Optimization Steps

    RTL Expression
          ↓
    Condition Analysis
          ↓
    Boolean Simplification
          ↓
    Redundant Logic Removal
          ↓
    Optimized Circuit


The final hardware represents the same required functionality
using a simplified implementation.


============================================================
9. SEQUENTIAL LOGIC OPTIMIZATION
============================================================

Sequential logic contains storage elements such as flip-flops.

Examples include:

- Registers
- Counters
- Shift registers
- State machines
- Pipeline registers


Synthesis tools can optimize sequential logic when the stored
information is constant, redundant or does not affect any
required output.

For example:

    always @(posedge clk)
        q <= 1'b0;


The register output is always zero.

If there is no requirement to preserve any other information,
the synthesis tool can identify that the flip-flop is not
providing useful variable state.


The general process is:

    Sequential RTL
          ↓
    Constant Analysis
          ↓
    State Analysis
          ↓
    Redundant Logic Removal
          ↓
    Optimized Sequential Hardware


============================================================
10. DFF CONSTANT OPTIMIZATION
============================================================

Several experiments in this module demonstrate optimization
of D flip-flop based circuits.

The important concept is constant propagation.

Consider:

    always @(posedge clk)
    begin
        q <= 1'b0;
    end


The value stored in the flip-flop never changes from zero.

The synthesis tool can therefore propagate the constant value
through the design.

If another register receives this constant:

    q1 <= 1'b0;
    q2 <= q1;


the constant value may propagate from:

    q1
     ↓
    q2


and simplify the sequential structure.


------------------------------------------------------------
DFF Constant Optimization — Experiment 1
------------------------------------------------------------

The first experiment demonstrates a register whose output is
forced to a constant value.

The simulation verifies that the output remains constant.

The synthesized circuit can remove unnecessary storage when
the constant behavior makes the flip-flop redundant.


------------------------------------------------------------
DFF Constant Optimization — Experiment 2
------------------------------------------------------------

The second experiment uses another constant-driven DFF
configuration.

The waveform is examined before synthesis to confirm the
expected reset and clock behavior.

The synthesized result is then inspected to determine how the
constant condition affects the hardware.


------------------------------------------------------------
DFF Constant Optimization — Experiment 3
------------------------------------------------------------

This experiment introduces an intermediate register.

Conceptually:

    Constant
       ↓
      DFF
       ↓
      q1
       ↓
      DFF
       ↓
      q


The synthesis tool can analyze the constant propagation
through the sequential path.

If the intermediate register does not contribute variable
information, optimization can simplify the structure.


------------------------------------------------------------
DFF Constant Optimization — Experiment 4
------------------------------------------------------------

This experiment demonstrates another relationship between
reset conditions, internal registers and output behavior.

The important point is to compare:

    Original RTL

with:

    Optimized hardware


The synthesis result shows which storage elements are actually
necessary for producing the required output.


------------------------------------------------------------
DFF Constant Optimization — Experiment 5
------------------------------------------------------------

The fifth experiment contains another constant-related
sequential configuration.

Simulation verifies:

- Clock behavior
- Reset behavior
- Output response


Synthesis then analyzes whether the internal sequential
elements are required.

This experiment reinforces the idea that RTL registers do not
always remain as physical flip-flops in the final optimized
netlist.


============================================================
11. COUNTER OPTIMIZATION
============================================================

Counters are sequential circuits whose values change with
clock events.

A simple counter can be described as:

    reg [2:0] count;


and updated using:

    count <= count + 1;


A three-bit counter has eight possible states:

    000
    001
    010
    011
    100
    101
    110
    111


However, the entire counter may not always be required by the
observable output.


------------------------------------------------------------
COUNTER OPTIMIZATION — EXPERIMENT 1
------------------------------------------------------------

Consider:

    module counter_opt (
        input clk,
        input reset,
        output q
    );

    reg [2:0] count;

    assign q = count[0];

    always @(posedge clk, posedge reset)
    begin
        if (reset)
            count <= 3'b000;
        else
            count <= count + 1;
    end

    endmodule


Only the least significant bit is connected to the output.

The output therefore depends on:

    count[0]


The synthesis tool analyzes whether all three counter bits
are necessary for producing the required output.

This demonstrates optimization based on observable behavior.


### Counter Operation

Starting from:

    000

the counter progresses as:

    000
    001
    010
    011
    100
    101
    110
    111


The least significant bit follows:

    0
    1
    0
    1
    0
    1
    0
    1


Therefore, the output has a simple repeating behavior.


------------------------------------------------------------
COUNTER OPTIMIZATION — EXPERIMENT 2
------------------------------------------------------------

The second experiment compares the counter value with a
constant.

Example:

    module counter_opt (
        input clk,
        input reset,
        output q
    );

    reg [2:0] count;

    assign q = (count[2:0] == 3'b100);

    always @(posedge clk, posedge reset)
    begin
        if (reset)
            count <= 3'b000;
        else
            count <= count + 1;
    end

    endmodule


Here, the output becomes active when:

    count = 100


The synthesis tool analyzes both:

    Counter Logic

and:

    Comparison Logic


and produces the required optimized structure.


============================================================
12. SIMULATION AND VERIFICATION
============================================================

Simulation is essential for verifying the original RTL before
examining synthesis optimization.

The verification process is:

    RTL
     ↓
    Testbench
     ↓
    Simulation
     ↓
    Waveform
     ↓
    Synthesis
     ↓
    Optimized Logic
     ↓
    Comparison


For combinational experiments, the output waveform is checked
against the input conditions.

For sequential experiments, the following signals are
observed:

    CLK
    RESET
    D
    Q


For counter experiments:

    CLK
    RESET
    COUNT
    OUTPUT


The waveform provides evidence that the RTL is operating as
expected.


============================================================
13. RTL-TO-OPTIMIZED HARDWARE FLOW
============================================================

The complete optimization flow studied in this module is:

                RTL
                 │
                 ▼
          RTL Elaboration
                 │
                 ▼
           Logic Analysis
                 │
                 ▼
       Constant Propagation
                 │
                 ▼
        Boolean Simplification
                 │
                 ▼
       Redundant Logic Removal
                 │
                 ▼
        Sequential Optimization
                 │
                 ▼
        Technology Mapping
                 │
                 ▼
        Optimized Netlist


This flow demonstrates why the final synthesized design may
look significantly different from the original RTL.


============================================================
14. EXPERIMENTAL OBSERVATIONS
============================================================

| Experiment | Main Concept |
|------------|--------------|
| Optimization Check 1 | Constant branch simplification |
| Optimization Check 2 | Boolean OR simplification |
| Optimization Check 3 | Nested condition optimization |
| Optimization Check 4 | Complex Boolean optimization |
| DFF Optimization 1 | Constant-driven register |
| DFF Optimization 2 | Sequential constant propagation |
| DFF Optimization 3 | Internal register optimization |
| DFF Optimization 4 | Reset/output optimization |
| DFF Optimization 5 | Further constant propagation |
| Counter Optimization 1 | Unused counter state |
| Counter Optimization 2 | Counter comparison optimization |


The important observations are:

1. Synthesis does not blindly reproduce the RTL structure.

2. Constant values can simplify combinational expressions.

3. Nested conditions can be reduced to simpler logic.

4. Redundant hardware can be removed.

5. Registers driven by constants may become unnecessary.

6. Counter logic can be optimized according to the required
   output behavior.

7. The synthesized netlist may contain fewer cells than the
   original RTL might suggest.


============================================================
15. KEY LEARNING POINTS
============================================================

The major concepts learned in Module 3 are:

### Combinational Optimization

- Boolean simplification
- Constant propagation
- Conditional optimization
- Redundant logic elimination


### Sequential Optimization

- DFF constant optimization
- Register simplification
- Constant propagation through registers
- Removal of unnecessary sequential logic


### Counter Optimization

- State-dependent logic
- Unused counter bits
- Constant comparisons
- Optimization of observable state


### Verification

- RTL simulation
- Waveform inspection
- Synthesis analysis
- Structural comparison


============================================================
16. CONCLUSION
============================================================

Module 3 demonstrates the ability of synthesis tools to
transform RTL into a more efficient hardware implementation.

The combinational experiments show how constant values,
Boolean relationships and nested conditions can be simplified.

The sequential experiments demonstrate that registers and
flip-flops can also be optimized when their stored values are
constant or unnecessary.

The counter experiments further show that synthesis considers
which parts of the internal state actually affect the required
outputs.

The complete concept can be summarized as:

    RTL
     ↓
    Analyze
     ↓
    Simplify
     ↓
    Optimize
     ↓
    Map
     ↓
    Hardware


The main lesson from this module is:

    "RTL describes functionality, while synthesis determines
     an efficient hardware implementation of that functionality."

Understanding synthesis optimization is important for
developing RTL that produces efficient area, power and timing
characteristics in the final digital design.