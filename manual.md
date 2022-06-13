# Vivado

## プロジェクト作成方法

※作成物一つにつき、必ず一つのプロジェクトを作成する

1. Create New Project
2. Next
3. Project Name入力→Next
4. RTL Project でDo not specify sourcdes at this timeチェック
5. Next
6. Zynq7000
7. Next
8. Finish

## 実行ファイル作成

1. Add Sources
2. Add or Create Design Sources
3. Next
4. Create File
5. ファイル名入力
6. Finish
7. Port Name

## 実行ファイル記述方法

```verilog
module ファイル名 (
    input 型 ポート名,
    input 型 ポート名,
    output 型 ポート名,
    output 型 ポート名
    );

    処理;
    処理;

endmodule
```

## 実行処理

### asign outputとinputを接続する

XOR演算^

```verilog
assign S = A ^ B;
```

AND演算&

```verilog
assign Y = A & B;
```

OR演算|

```verilog
assign Y = A | B;
```

加算+

```verilog
assign Y = A + B;
```

減算-

```verilog
assign Y = A - B;
```

否定~

```verilog
assign Y = ~ A
```

比較

```verilog
assign r0 = 2'd1 < 2'd3; // 1
assign r1 = 2'd1 <= 2'd3; // 1
assign r2 = 2'd1 <= 2'd1; // 1
assign r3 = 2'd1 > 2'd3; // 0
assign r4 = 2'd1 >= 2'd3; // 0
assign r5 = 2'd1 >= 2'd1; // 1 
```

等号

```verilog
assign r0 = 2'd1 == 2'd1; // 1
assign r1 = 2'd1 != 2'd1; // 0
```

論理演算

```verilog
assign r0 = 1'b1 && 1'b1; // 1
assign r1 = 1'b1 || 1'b0; // 1
assign r2 = !1'b1; // 0
```

ビット演算

```verilog
A = 4'b0110;
B = 4'b1100;

Y = ~A;    //否定演算　結果は1001
Y = A & B;　 //AND演算　結果は0100
Y = A | B;　　//OR演算　結果は1110
Y = A ^ B;　　//XOR演算　結果は1010
Y = A ~^ B;　 //XNOR演算　結果は0101
```

リダクション

```verilog
wire [3: 0] A = 4'b0010;

X = &A; //AND演算、結果は0
X = ~&A; //NAND演算、結果は1
X = |A;　　//OR演算、結果は1
X = ~|A;　　//NOR演算、結果は0
X = ^A;　　//XOR演算、結果は1
X= ~^A;　　//XNOR演算、結果は0
```

シフト

```verilog
信号 >> シフト幅 // 符号なし右シフト
信号 << シフト幅 // 符号なし左シフト
信号 >>> シフト幅 // 符号有り右シフト
信号 <<< シフト幅 // 符号有り左シフト
```

連接演算子

```verilog
{信号, 信号,...,信号} = {信号, 信号,...,信号};
```

### wire

wireは変数同士の値の同期用

```verilog
// Cというワイヤ変数を定義
wire c
module1 m1(.output(C));
module2 m2(.input(C));
```

モジュール1での変更がモジュール2の引数の値に伝播する

### 複数bit

```verilog
型: [ビット数-1:0]
```

取得、変更方法

```verilog
// 4bit
input [3:0] array = 0'b0101;

array[0]; // 1
array[1]; // 0
array[2]; // 1
array[3]; // 0
```

値での変更方法;

```verilog
input [1:0] array;

array = 2'b00;
array = 2'b01;
array = 2'b10;
array = 2'b11;
array = 2'bxx;
```

```verilog
input [3:0] array;

array = 4'h1;
array = 4'h2;
array = 4'hd;
array = 4'he;
array = 4'hf;
array = 4'hx;
```

```verilog
input [15:0] array;

array = 16'h0;
array = 16'h1;
array = 16'hfffd;
array = 16'hfffe;
array = 16'hffff;
array = 16'hxxxx;
```

### IF

1. regで出力変数を再宣言
2. always @()で使用input変数監視指定
3. begin ~ endの間にif文

```verilog
reg 型 変数;
always @( 変数 or 変数 or 変数) // セミコロン不要
begin
    if (変数 == 値)
        処理;
    else if (変数 == 値)
        処理;
    else
        処理;
end
```

複数処理もできる

```verilog
reg 型 変数;
always @( 変数 or 変数 or 変数) // セミコロン不要
begin
    if (変数 == 値)
        begin
            処理;
            処理;
        end
end
```

### CASE

1. regで出力変数を再宣言
2. always @()で使用input変数監視指定
3. begin ~ endの間にcase文

```verilog
reg 型 変数;
always @( 変数 or 変数 or 変数) // セミコロン不要
begin
    case (変数)
        値: 処理;
        値: 処理;
        default: 処理;
    endcase
end
```

### 三項演算子

always不要

```verilog
assign Y = ( 条件式 ) ? TRUE時の値 : FALSE時の値
```

## テストファイル作成

1. Add Sources
2. add or create simulation sources
3. Next
4. Create File
5. テストファイル名入力
6. Finish
7. ポート入力せずOK
8. YES

## テストの書き方

```verilog
module ファイル名;
    // インプットをregで定義
    reg 型 入力変数名;
    reg 型 入力変数名;

    // outputをwireで定義
    wire 型 変数名;
    wire 型 変数名;

    テスト対象モジュール名 uut (
        .引数名(変数名),
        .引数名(変数名),
        .引数名(変数名),
        .引数名(変数名)
    );

    initial begin

        // 入力初期値を定義
        入力変数名 = 値;
        入力変数名 = 値;

        // 入力が初期値の状態で100ms待機
        #100;

        // テストパターンの入力値指定して100ms待機を繰り返す
        #100 入力変数名 = 値; 入力変数名 = 値;
        #100 入力変数名 = 値; 入力変数名 = 値;
        #100 入力変数名 = 値; 入力変数名 = 値;
        #100 入力変数名 = 値; 入力変数名 = 値;
        #100 $finish;
    end
endmodule
```

## 回路検証

1. Run Simulation
2. Run Behavioral Simulation
3. save

## コードの再利用 import

1. Add Sources
2. Add or create design sources
3. add files
4. projectname/projectname.srcs/sources_1/new/filename.v
5. Finish

## importしたモジュールの利用

```verilog
モジュール名 モジュール個体名 (.引数(変数), .引数(変数));
```
