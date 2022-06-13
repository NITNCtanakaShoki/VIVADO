# Barrel Shifter

## 過去問コード

```verilog
module l_bshifter(A, C, Y);
    input[7:0] A;
    input[2:0] C;
    output[7:0] Y;

    wire [7:0] S4, S2, S1;
    wire [7:0] A4, A2;

    bitshift4 b4 (A, S4);
    mux2 M4 (A, S4, C[2], A4);

    bitshift2 b2 (A4, S2);
    mux2 M2 (A4, S2, C[1], A2);

    bitshift1 b1 (A2, S1);
    mux2 M1 (A2, S1, C[0], Y);

endmodule
```

```verilog
module bitshift1(
    input [7:0] A,
    output [7:0] Y
    );
    
    assign Y = A << 1;
endmodule
```

```verilog
module bitshift2(
    input [7:0] A,
    output [7:0] Y
    );
    
    assign Y = A << 2;
endmodule
```

```verilog
module bitshift4(
    input [7:0] A,
    output [7:0] Y
    );
    
    assign Y = A << 4;
endmodule
```

```verilog
module mux2(
    input [7:0] A,
    input [7:0] B,
    input C,
    output [7:0] Y
    );
    
    assign Y = C == 1'b0 ? A : B;
endmodule
```

## テスト

```verilog
module l_bshifter_tb;

    reg assertion;
    
    reg [7:0] A;
    reg[2:0] C;
    wire [7:0] Y;
    
    l_bshifter uut (
        .A(A),
        .C(C),
        .Y(Y)
    );
    
    initial begin
        
        assertion = 1;
        
        A = 8'b10110111; C = 3'b000;
        #1; assertion = Y == 8'b10110111; #10
        
        A = 8'b10110111; C = 3'b001;
        #1; assertion = Y == 8'b01101110; #10
        
        
        A = 8'b10110111; C = 3'b010;
        #1; assertion = Y == 8'b11011100; #10
        
        
        A = 8'b10110111; C = 3'b011;
        #1; assertion = Y == 8'b10111000; #10
        
        
        A = 8'b10110111; C = 3'b100;
        #1; assertion = Y == 8'b01110000; #10
        
        
        A = 8'b10110111; C = 3'b101;
        #1; assertion = Y == 8'b11100000; #10
        
        
        A = 8'b10110111; C = 3'b110;
        #1; assertion = Y == 8'b11000000; #10
        
        
        A = 8'b10110111; C = 3'b111;
        #1; assertion = Y == 8'b10000000; #10
        
        $finish;
    end
endmodule
```
