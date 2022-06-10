# mux4

## 概要

入力の選択(reg_sel4)から出力値(Aout)を入力(alu_out, Bin, Cin, Din)から選ぶ。

| reg_sel4 |  Aout   |
| :------: | :-----: |
|    00    | alu_out |
|    01    |   Bin   |
|    10    |   Cin   |
|    11    |   Din   |

## 引数

### input

- [7:0] alu_out
- [7:0] Bin
- [7:0] Cin
- [7:0] Din
- [1:0] reg_sel4

### output

- [7:0] Aout

## 依存

なし

## Code

```verilog
module mux4(
    input [7:0] alu_out,
    input [7:0] Bin,
    input [7:0] Cin,
    input [7:0] Din,
    input [1:0] reg_sel4,
    output [7:0] Aout
    );
    
    reg [7:0] Aout;
    always @( alu_out, Bin, Cin, Din, reg_sel4 )
    begin
        case (reg_sel4)
            2'b00: Aout = alu_out;
            2'b01: Aout = Bin;
            2'b10: Aout = Cin;
            2'b11: Aout = Din;
            default: Aout = 8'hxx;
        endcase
    end
        
endmodule
```

## Test

```verilog
module mux4_tb;

    reg assertion;
    reg [7:0] alu_out, Bin, Cin, Din;
    reg [1:0] reg_sel4;
    wire [7:0] Aout;
    
    mux4 uut (
        .alu_out(alu_out),
        .Bin(Bin),
        .Cin(Cin),
        .Din(Din),
        .reg_sel4(reg_sel4),
        .Aout(Aout)
    );
    
    initial begin
        
        assertion = 1;
        
        // when 00 -> Ain = alu_out
        alu_out = 8'hff; Bin = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel4 = 2'b00;
        #1; assertion = (Aout == 8'hff); #10
        
        
        // when 01 -> Ain = Bin
        alu_out = 8'hff; Bin = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel4 = 2'b01;
        #1; assertion = (Aout == 8'h00); #10
        
        // when 10 -> Ain = Cin
        alu_out = 8'hff; Bin = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel4 = 2'b10;
        #1; assertion = (Aout == 8'h01); #10
        
        // when 11 -> Ain = Din
        alu_out = 8'hff; Bin = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel4 = 2'b11;
        #1; assertion = (Aout == 8'h02); #10
        
        $finish;
    end
endmodule
```
