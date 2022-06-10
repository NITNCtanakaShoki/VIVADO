# mux3

## 概要

Boutの最終的な出力の選択を行う選択器。

入力の選択(reg_sel3)から出力値(Bout)を入力(Ain, Cin, Din)から選ぶ。

| reg_sel3 | Bout |
| :------: | :--: |
|    00    | Ain  |
|    01    | Cin  |
|    10    | Din  |
|    11    |  X   |

## 引数

### input

- [7:0] Ain
- [7:0] Cin
- [7:0] Din
- [1:0] reg_sel3

### output

- [7:0] Bout

## 依存

なし

## Code

```verilog
module mux3(
    input [7:0] Ain,
    input [7:0] Cin,
    input [7:0] Din,
    input [1:0] reg_sel3,
    output [7:0] Bout
    );
    
    reg [7:0] Bout;
    always @( Ain, Cin, Din, reg_sel3 )
    begin
        case (reg_sel3)
            2'b00: Bout = Ain;
            2'b01: Bout = Cin;
            2'b10: Bout = Din;
            default: Bout = 8'hxx;
        endcase
    end
        
endmodule
```

## Test

```verilog
module mux3_tb;

    reg assertion;
    reg [7:0] Ain, Cin, Din;
    reg [1:0] reg_sel3;
    wire [7:0] Bout;
    
    mux3 uut (
        .Ain(Ain),
        .Cin(Cin),
        .Din(Din),
        .reg_sel3(reg_sel3),
        .Bout(Bout)
    );
    
    initial begin
        
        assertion = 1;
        
        // when 00 -> Bin = Ain
        Ain = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel3 = 2'b00;
        #1; assertion = (Bout == 8'h00); #10
        
        // when 01 -> Bin = Cin
        Ain = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel3 = 2'b01;
        #1; assertion = (Bout == 8'h01); #10
        
        // when 10 -> Bin = Din
        Ain = 8'h00; Cin = 8'h01; Din = 8'h02; reg_sel3 = 2'b10;
        #1; assertion = (Bout == 8'h02); #10
        
        // Don't care cannot be compared
        
        $finish;
    end
endmodule
```
