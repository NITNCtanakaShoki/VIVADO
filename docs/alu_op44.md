# alu_op44

## 概要

与えられた8bitの2つのデータ(Ain, Bin)と1bitのデータ(Carryin)から指示（op_sel)に合わせて演算した結果を返す。

| op_sel | process                     |
| :----: | --------------------------- |
|  000   | Aout <- Ain + Bin           |
|  001   | Aout <- Ain + Bin + Carryin |
|  010   | Aout <- Ain - Bin           |
|  011   | Aout <- Ain * Bin           |
|  100   | Aout <- Ain and Bin         |
|  101   | Aout <- Ain or Bin          |
|  110   | Aout <- not Ain             |
|  111   | Aout <- Ain xor Bin         |

## 引数

### input

- [7:0] Ain
- [7:0] Bin
- Carryin
- [2:0] op_sel

### output

- Carryout
- [7:0] alu_out

## 依存

なし

## Code

```verilog
module alu_op44(
    input [7:0] Ain,
    input [7:0] Bin,
    input Carryin,
    input [2:0] op_sel,
    output Carryout,
    output [7:0] alu_out
    );
    
    reg Carryout;
    reg [7:0] alu_out;
    
    always @( Ain or Bin or Carryin or op_sel )
    begin
        case (op_sel)
            3'b000: {Carryout, alu_out} = Ain + Bin;
            3'b001: {Carryout, alu_out} = Ain + Bin + Carryin;
            3'b010: begin Carryout = 2'bx; alu_out = Ain - Bin; end
            3'b011: {Carryout, alu_out} = Ain * Bin;
            3'b100: begin Carryout = 2'bx; alu_out = Ain & Bin; end
            3'b101: begin Carryout = 2'bx; alu_out = Ain | Bin; end
            3'b110: begin Carryout = 2'bx; alu_out = ~Ain; end
            3'b111: begin Carryout = 2'bx; alu_out = Ain ^ Bin; end
        endcase
    end
    
endmodule
```

## Test

```verilog
module alu_op44_tb;

    reg assertion;
    
    reg [7:0] Ain, Bin;
    reg Carryin;
    reg [2:0] op_sel;
    
    wire Carryout;
    wire [7:0] alu_out;
    
    
    alu_op44 uut (
        .Ain(Ain),
        .Bin(Bin),
        .op_sel(op_sel),
        .Carryin(Carryin),
        .Carryout(Carryout),
        .alu_out(alu_out)
    );
    
    initial begin
        assertion = 1;
        
        // half adder test
        Ain = 8'h01; Bin = 8'h02; Carryin = 0; op_sel = 3'b000;
        #1; assertion = (Carryout == 0) && (alu_out == 8'h03); #10
        
        
        Ain = 8'h0f; Bin = 8'h0e; Carryin = 1; op_sel = 3'b000;
        #1 assertion = (Carryout == 0) && (alu_out == 8'h1d); #10
        
        Ain = 8'h7b; Bin = 8'h84; Carryin = 0; op_sel = 3'b000;
        #1 assertion = (Carryout == 0) && (alu_out == 8'hff); #10
        
        Ain = 8'h7c; Bin = 8'h84; Carryin = 1; op_sel = 3'b000;
        #1 assertion = (Carryout == 1) && (alu_out == 8'h00); #10
        
        // full adder test
        Ain = 8'h01; Bin = 8'h02; Carryin = 0; op_sel = 3'b001;
        #1; assertion = (Carryout == 0) && (alu_out == 8'h03); #10
        
        Ain = 8'h0f; Bin = 8'h0e; Carryin = 1; op_sel = 3'b001;
        #1 assertion = (Carryout == 0) && (alu_out == 8'h1e); #10
        
        Ain = 8'h7b; Bin = 8'h83; Carryin = 1; op_sel = 3'b001;
        #1 assertion = (Carryout == 0) && (alu_out == 8'hff); #10
        
        Ain = 8'h7c; Bin = 8'h83; Carryin = 1; op_sel = 3'b001;
        #1 assertion = (Carryout == 1) && (alu_out == 8'h00); #10
        
        // sub
        Ain = 8'h00; Bin = 8'h01; Carryin = 0; op_sel = 3'b010;
        #1 assertion = (alu_out == 8'hff); #10
        
        Ain = 8'hff; Bin = 8'h02; Carryin = 0; op_sel = 3'b010;
        #1 assertion = (alu_out == 8'hfd); #10
       
        // multiple
        Ain = 8'h03; Bin = 8'h02; Carryin = 0; op_sel = 3'b011;
        #1 assertion = (alu_out == 8'h06); #10
        
        Ain = 8'h0b; Bin = 8'h0c; Carryin = 0; op_sel = 3'b011;
        #1 assertion = (alu_out == 8'h84); #10
        
        // and
        Ain = 8'b10101010; Bin = 8'b11110000; Carryin = 0; op_sel = 3'b100;
        #1 assertion = (alu_out == 8'b10100000); #10
        
        Ain = 8'b10101010; Bin = 8'b00001111; Carryin = 0; op_sel = 3'b100;
        #1 assertion = (alu_out == 8'b00001010); #10
        
        // or
        Ain = 8'b10101010; Bin = 8'b11110000; Carryin = 0; op_sel = 3'b101;
        #1 assertion = (alu_out == 8'b11111010); #10
        
        Ain = 8'b10101010; Bin = 8'b00001111; Carryin = 0; op_sel = 3'b101;
        #1 assertion = (alu_out == 8'b10101111); #10
        
        // not
        Ain = 8'b10101010; Bin = 8'b11110000; Carryin = 0; op_sel = 3'b110;
        #1 assertion = (alu_out == 8'b01010101); #10
        
        // xor
        Ain = 8'b10101010; Bin = 8'b00001111; Carryin = 0; op_sel = 3'b111;
        #1 assertion = (alu_out == 8'b10100101); #10
     
        $finish;
    end
endmodule
```
