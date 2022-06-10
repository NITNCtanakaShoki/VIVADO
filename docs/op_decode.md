# op_decode

## 概要

機械語命令(op)を受け取り、Ainの選択信号(reg_sel4)とBinの選択信号(reg_sel3)を決め、aluへの命令(op_sel)を返す。

|  op  | op_sel | reg_sel4 | regsel3 |
| :--: | :----: | :------: | :-----: |
| 0000 |  000   |    00    |    X    |
| 0001 |  001   |    00    |    X    |
| ...  |  ...   |   ...    |   ...   |
| 0111 |  111   |    00    |    X    |
| 1000 |   X    |    01    |    X    |
| 1001 |   X    |    10    |    X    |
| 1010 |   X    |    11    |    X    |
| 1011 |   X    |    X     |   00    |
| 1100 |   X    |    X     |   01    |
| 1101 |   X    |    X     |   10    |
| 1110 |   X    |    X     |    X    |
| 1111 |   X    |    X     |    X    |

## 引数

### input

- [3:0] op

### output

- [2:0] op_sel
- [1:0] reg_sel4
- [1:0] reg_sel3

## 依存

なし

## Code

```verilog
module op_decode(
    input [3:0] op,
    output [2:0] op_sel,
    output [1:0] reg_sel4,
    output [1:0] reg_sel3
    );
    
    reg [2:0] op_sel;
    reg [1:0] reg_sel4, reg_sel3;
    
    always @( op )
    
    begin
        if ((op >> 3) == 0)
            begin
                op_sel = op;
                reg_sel4 = 2'b00;
                reg_sel3 = 2'bxx;
            end
        else if (op == 4'b1000)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'b01;
                reg_sel3 = 2'bxx;
            end
        else if (op == 4'b1001)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'b10;
                reg_sel3 = 2'bxx;
            end
        else if (op == 4'b1010)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'b11;
                reg_sel3 = 2'bxx;
            end
        else if (op == 4'b1011)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'bxx;
                reg_sel3 = 2'b00;
            end
        else if (op == 4'b1100)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'bxx;
                reg_sel3 = 2'b01;
            end
        else if (op == 4'b1101)
            begin
                op_sel = 3'bxxx;
                reg_sel4 = 2'bxx;
                reg_sel3 = 2'b10;
            end
        else
            {op_sel, reg_sel4, reg_sel3} = 7'bxxxxxxx;
    end
    
endmodule
```

## Test

```verilog
module op_decode_tb;

    reg assertion;
    reg [3:0] op;
    wire [2:0] op_sel;
    wire [1:0] reg_sel4, reg_sel3;
    
    op_decode uut (
        .op(op),
        .op_sel(op_sel),
        .reg_sel4(reg_sel4),
        .reg_sel3(reg_sel3)
    );
    
    initial begin
        
        assertion = 1;
        
        // when 0000 ~ 0111 is using ALU
        op = 4'b0000;
        #1; assertion = (op_sel == 3'b000) && (reg_sel4 == 2'b00); #10
        
        op = 4'b0111;
        #1; assertion = (op_sel == 3'b111) && (reg_sel4 == 2'b00); #10
        
        // when 1000 is from B to A
        op = 4'b1000;
        #1; assertion = reg_sel4 == 2'b01; #10
        
        // when 1001 is from C to A
        op = 4'b1001;
        #1; assertion = reg_sel4 == 2'b10; #10
        
        // when 1010 is from D to A
        op = 4'b1010;
        #1; assertion = reg_sel4 == 2'b11; #10
        
        // when 1011 is from A to B
        op = 4'b1011;
        #1; assertion = reg_sel3 == 2'b00; #10
        
        // when 1100 is from C to B
        op = 4'b1100;
        #1; assertion = reg_sel3 == 2'b01; #10
        
        // when 1101 is from D to B
        op = 4'b1101;
        #1; assertion = reg_sel3 == 2'b10; #10

        $finish;
    end
endmodule
```
