# FF_blocking_non-blocking
# EXPERIMENT 3A: Simulation of All Flip-Flops using Blocking Statement
# AIM
To design and simulate basic flip-flops (SR, D, JK, and T) using blocking statements in Verilog HDL, and verify their functionality through simulation in Vivado 2023.1.

# APPARATUS REQUIRED
Vivado 2023.1
Computer with HDL Simulator
# DESCRIPTION
Flip-flops are the basic memory elements in sequential circuits.
In this experiment, different types of flip-flops (SR, D, JK, T) are modeled using behavioral modeling with blocking assignment (=) inside the always block.
Blocking assignments execute sequentially in the given order, which makes it easier to describe simple synchronous circuits.

# PROCEDURE
Open Vivado 2023.1.
Create a New RTL Project (e.g., FlipFlop_Simulation).
Add Verilog source files for each flip-flop (SR, D, JK, T).
Add a testbench file to verify all flip-flops.
Run Behavioral Simulation.
Observe waveforms of inputs and outputs for each flip-flop.
Verify that outputs match the truth table.
Save results and capture simulation screenshots.
# VERILOG CODE
# SR Flip-Flop (Non Blocking)
```
module sr_ff (
    input wire S, R, clk,
    output reg Q
);
    always @(posedge clk) begin
        case ({S, R})
            2'b00: Q <= Q;       // No change (Hold)
            2'b01: Q <= 1'b0;    // Reset
            2'b10: Q <= 1'b1;    // Set
            2'b11: Q <= 1'bx;    // Invalid / Undefined state
        endcase
    end
endmodule
```
# SR Flip-Flop Test bench
```
module tb_sr_ff;
    reg S, R, clk;
    wire Q;

    // Instantiate the Unit Under Test (UUT)
    sr_ff uut (
        .S(S), 
        .R(R), 
        .clk(clk), 
        .Q(Q)
    );

    // Clock generation: 10ns period (5ns high, 5ns low)
    always #5 clk = ~clk;

    initial begin
        // Initialize inputs
        clk = 0;
        S = 0; 
        R = 0;

        // Test all conditions synchronously
        #10; S = 0; R = 0; // Hold (Q stays 0)
        #10; S = 1; R = 0; // Set (Q becomes 1)
        #10; S = 0; R = 0; // Hold (Q stays 1)
        #10; S = 0; R = 1; // Reset (Q becomes 0)
        #10; S = 0; R = 0; // Hold (Q stays 0)
        #10; S = 1; R = 1; // Invalid / Undefined (Q becomes x)
        #10; S = 0; R = 0; 
        #10;
        $finish;
    end

    // Monitor changes
    initial begin
        $monitor("Time = %0t ns | S = %b | R = %b | Q = %b", $time, S, R, Q);
    end

endmodule
```
# SIMULATION OUTPUT
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d227d60b-e27c-4c5f-a879-ee505c3122d9" />


# JK Flip-Flop (Non Blocking)
```
module jk_ff (
    input wire J, K, clk,
    output reg Q
);
    always @(posedge clk) begin
        case ({J, K})
            2'b00: Q <= Q;       // Hold
            2'b01: Q <= 1'b0;    // Reset
            2'b10: Q <= 1'b1;    // Set
            2'b11: Q <= ~Q;      // Toggle
        endcase
    end
endmodule
```
# JK Flip-Flop Test bench
```
module tb_jk_ff;
    reg J, K, clk;
    wire Q;

    // Instantiate the Unit Under Test (UUT)
    jk_ff uut (
        .J(J), 
        .K(K), 
        .clk(clk), 
        .Q(Q)
    );

    // Clock generation: 10ns period (5ns high, 5ns low)
    always #5 clk = ~clk;

    initial begin
        // Initialize inputs
        clk = 0;
        J = 0; 
        K = 0;

        // Test all JK conditions synchronously
        #10; J = 0; K = 0; // Hold (Q stays 0)
        #10; J = 1; K = 0; // Set (Q becomes 1)
        #10; J = 0; K = 0; // Hold (Q stays 1)
        #10; J = 0; K = 1; // Reset (Q becomes 0)
        #10; J = 0; K = 0; // Hold (Q stays 0)
        #10; J = 1; K = 1; // Toggle (Q becomes 1)
        #10; J = 1; K = 1; // Toggle (Q becomes 0)
        #10; J = 0; K = 0; 
        #10;
        $finish;
    end

    // Monitor changes
    initial begin
        $monitor("Time = %0t ns | J = %b | K = %b | Q = %b", $time, J, K, Q);
    end

endmodule
```

# SIMULATION OUTPUT
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/91a7ab08-3100-406a-ae18-e58cb9625086" />


# D Flip-Flop (Non Blocking)
```
module d_ff (
    input wire d, clk,
    output reg Q
);
    always @(posedge clk) begin
        Q <= d;
    end
endmodule
```
# D Flip-Flop Test bench
```
module tb_d_ff;
    reg d, clk;
    wire Q;

    // Instantiate the Unit Under Test (UUT)
    d_ff uut (
        .d(d), 
        .clk(clk), 
        .Q(Q)
    );

    // Clock generation: 10ns period (5ns high, 5ns low)
    always #5 clk = ~clk;

    initial begin
        // Initialize inputs
        clk = 0;
        d = 0;

        // Test D flip-flop transitions synchronously
        #10; d = 1; // Q should become 1 on next posedge
        #10; d = 0; // Q should become 0 on next posedge
        #10; d = 1; // Q should become 1
        #10; d = 1; // Q stays 1
        #10; d = 0; // Q becomes 0
        #10;
        $finish;
    end

    // Monitor changes
    initial begin
        $monitor("Time = %0t ns | d = %b | Q = %b", $time, d, Q);
    end

endmodule
```

# SIMULATION OUTPUT
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7ceb7452-cc06-45de-9746-f6273bc2020f" />


# T Flip-Flop (Non Blocking)
```
module t_ff (
    input wire T, clk, rst,
    output reg Q
);
    always @(posedge clk or posedge rst) begin
        if (rst)
            Q <= 1'b0;    // Initialize/clear Q to 0 on reset
        else if (T)
            Q <= ~Q;   // Toggle
        else
            Q <= Q;    // Hold
    end
endmodule
```
# T Flip-Flop Test bench
```
module tb_t_ff;
    reg T, clk, rst;
    wire Q;

    // Instantiate the Unit Under Test
    t_ff uut (
        .T(T), 
        .clk(clk), 
        .rst(rst), 
        .Q(Q)
    );

    // Clock generation: 10ns period
    always #5 clk = ~clk;

    initial begin
        // Initialize inputs and assert reset
        clk = 0;
        rst = 1;
        T = 0;

        // Hold reset for 12ns to clear Q to 0
        #12;
        rst = 0; 

        // Test T flip-flop behavior synchronously
        #10; T = 1; // Toggle (Q goes from 0 to 1)
        #10; T = 1; // Toggle (Q goes from 1 to 0)
        #10; T = 0; // Hold (Q stays 0)
        #10; T = 1; // Toggle (Q goes from 0 to 1)
        #10;
        $finish;
    end

    // Monitor changes
    initial begin
        $monitor("Time = %0t ns | rst = %b | T = %b | Q = %b", $time, rst, T, Q);
    end

endmodule
```
# SIMULATION OUTPUT
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f6c29214-4f55-4797-a8b8-110e1e5840cb" />


# RESULT
All flip-flops (SR, D, JK, T) were successfully simulated using Non blocking statements in Verilog HDL. The outputs matched the expected truth table values, demonstrating correct sequential behavior.
