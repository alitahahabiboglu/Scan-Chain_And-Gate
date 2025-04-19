# Scan-Chain_Or-Gate
Exercise documents

`timescale 1ns / 1ps

// OR Gate Module
module ORGate (
input wire a,
input wire b,
output wire y
);
assign y = a | b; // OR logic
endmodule

module ScanFlipFlop (
    input wire clk,
    input wire reset,
    input wire d,
    input wire scan_in,
    input wire scan_enable,
    output reg q
);
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            q <= 0;
        end else if (scan_enable) begin
            q <= scan_in; // Shift in scan data
        end else begin
            q <= d; // Normal mode
        end
    end
endmodule 

module TopLevel (
    input wire clk,
    input wire reset,
    input wire d1,
    input wire d2,
    input wire scan_in1,
    input wire scan_enable,
    output wire or_output,
    output wire q1,
    output wire q2
);
    // Internal flip-flop outputs now become output ports
    ScanFlipFlop ff1 (
        .clk(clk),
        .reset(reset),
        .d(d1),
        .scan_in(scan_in1),
        .scan_enable(scan_enable),
        .q(q1)
    );

    ScanFlipFlop ff2 (
        .clk(clk),
        .reset(reset),
        .d(d2),
        .scan_in(q1),
        .scan_enable(scan_enable),
        .q(q2)
    );

    ORGate or1 (
        .a(q1),
        .b(q2),
        .y(or_output)
    );

endmodule





// Test Bench
`timescale 1ns / 1ps

module Testbench;

    reg clk;
    reg reset;
    reg d1, d2;
    reg scan_enable;
    reg scan_in1;
    wire or_output;
    wire q1, q2; // Add wires to monitor OR inputs

    TopLevel uut (
        .clk(clk),
        .reset(reset),
        .d1(d1),
        .d2(d2),
        .scan_in1(scan_in1),
        .scan_enable(scan_enable),
        .or_output(or_output),
        .q1(q1),
        .q2(q2) // Connect internal signals
    );

    // Clock generation
    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    // Test procedure
    initial begin
        $display("=== Starting Scan Chain OR Gate Test ===");
        reset = 1; scan_enable = 0; d1 = 0; d2 = 0; scan_in1 = 0;
        #40; reset = 0; #40;

        scan_enable = 0; d1 = 0; d2 = 0; #10;
        $display("1-Reset to initialize: OR Inputs a=%b, b=%b => OR Output: %b", q1, q2, or_output);

        scan_enable = 1; scan_in1 = 1; #10;
        $display("2-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 0; #10;
        $display("3-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 0; #10;
        $display("4-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 1; #10;
        $display("5-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 1; #10;
        $display("6-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 0; #10;
        $display("7-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 0; #10;
        $display("8-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 0; #10;
        $display("9-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        scan_in1 = 1; #10;
        $display("10-Scan Mode: scan_in1=%b => OR Inputs a=%b, b=%b => OR Output: %b", scan_in1, q1, q2, or_output);

        $display("=== End of Test ===");
        $finish;
    end
endmodule
