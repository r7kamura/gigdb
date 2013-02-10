LLVMやりまーす。

## このエントリでやること
1. Mac用の環境構築
1. C言語で単純なコードを書く
1. llvm-gccで人間が読める形式の中間コードに変換する
1. lliというツールで中間コードを実行する
1. 中間コードを読んでみる
1. 自分で中間コードを書いて実行してみる


## Mac用の環境構築
```
$ brew update
$ brew install llvm
```


## C言語で単純なコードを書く
```
$ vi hello_world.c
$ ls
hello_world.c
```

```c
#include <stdio.h>

// Print "Hello, world!\n", and exit.
int main() {
  printf("Hello, world!\n");
  return 0;
}
```


## llvm-gccで人間が読める形式の中間コードに変換する
```
$ llvm-gcc -o hello_world.s -S
$ ls
hello_world.c
hello_world.s
```


## lliというツールで中間コードを実行する
```
$ which lli
/usr/local/bin/lli

$ lli --version
LLVM (http://llvm.org/):
  LLVM version 3.2svn
  Optimized build with assertions.
  Built Dec 22 2012 (01:05:07).
  Default target: x86_64-apple-darwin11.4.2
  Host CPU: corei7-avx

$ lli hello_world.s
Hello, world!
```


## 中間コードを読んでみる
ここで中間コードと呼んでいるものは、llvm-gccの生成したアセンブリ言語hello_world.sのことである。
アセンブリ言語とは、機械語を人間に分かりやすい形で記述した言語である。
人間の理解しやすい高水準言語であるhello_world.c、プロセッサが直接実行できる機械語、
その中間表現であるアセンブリ言語を指して中間コードと呼んでいる。
以降、LLVMの生成する中間コードのことを指してLLVMアセンブリコードと呼ぶ。

```
$ cat hello_world.s
```

```
; ## コメント
; 適宜コメントを入れて説明する。
; LLVMアセンブリコード内のコメントはセミコロンで始まり、行末まで続く。
;
; ModuleID = 'hello_world.c'

target datalayout = "e-p:64:64:64-i1:8:8-i8:8:8-i16:16:16-i32:32:32-i64:64:64-f32:32:32-f64:64:64-v64:64:64-v128:128:128-a0:0:64-s0:64:64-f80:128:128-n8:16:32:64"
target triple = "x86_64-apple-darwin11.4"

; ## @
; グローバルID、グローバル変数、関数は先頭に"@"を付ける。
;
; ## 配列
; ベクトル型または配列型は [<要素数> x <各要素のサイズ>] として宣言する。
; 文字列"Hello, world!"の場合、各文字を1byteとし、
; 終端記号のNULL文字用の1byteを追加して合計すると、その型は[14 x i8]となる。
;
; ## 定数
; "Hello, world!\n"のような文字列リテラルを利用したいときは、グローバル変数として定数を宣言する。
; constantキーワードにより定数として宣言し、その後に型と値を続ける。
; 例えば @hello = constant [14 x i8] c"Hello, world!\00" のように記述する。
;
; ## 文字列リテラル
; 文字列の値を記述するには、接頭辞cの後に二重引用符で囲んだ文字列を続ける。
; 二重引用符の末尾には、NULL文字\0と0を記述する必要がある。
@.str = private constant [14 x i8] c"Hello, world!\00", align 1 ; <[14 x i8]*> [#uses=1]

; ## 型
; LLVMは強い型付けを利用するシステムである。
; LLVMは整数型をiNとして定義する。
; iNのNはその整数のビット幅であり、1から222までの任意のビット幅を指定できる。
;
; ## 関数定義
; LLVMアセンブリコードでは、関数を宣言して定義できる。
; 関数を定義するには、defineキーワードの後に戻り値の型、関数名を続ける。
; 前述した通り、関数名の先頭には"@"が付く。
; 例えば define i32 @main() { ... } とした場合、この関数はi32型の値を返す。
define i32 @main() nounwind ssp {

; ## ローカルID
; LLVMアセンブリコードのローカルID、即ちローカル変数名は、"%"で始まる。
; また、IDに使用できる正規表現は [%@][a-zA-Z$._][a-zA-Z$._0-9]* である。
; 即ち、%または@で始まり、a-zA-Z$._が使用可能で、先頭以外であれば数字も使用可能である。
;
; ## 関数呼出
; 関数を呼び出すには、call <関数の戻り値の型> <関数名> [<引数リスト>] のように記述する。
; それぞれの引数の前には、その値の型を指定する必要がある。
; 6bitの整数を返し、36bitの整数を引数に取るtest日間数の場合、
; call i6 @test(i36 %arg1) となる。
;
; # getelementptr
; LLVMの命令の1つであり、配列からポインタへの型キャストを正しく行うために必要である。
; 第1引数にはpointer、第2引数には先頭アドレスからのオフセット、第3引数には要素のインデックスを与える。
; 下記の例では64bit OSで実行しているため、ポインタのサイズは8byte、即ちi64となる。
; 第3引数のi64は、文字列の0番目の要素を選択するために使用され、この要素がputsに引数として渡される。
entry:
  %retval = alloca i32                            ; <i32*> [#uses=2]
  %0 = alloca i32                                 ; <i32*> [#uses=2]
  %"alloca point" = bitcast i32 0 to i32          ; <i32> [#uses=0]
  %1 = call i32 @puts(i8* getelementptr inbounds ([14 x i8]* @.str, i64 0, i64 0)) nounwind ; <i32> [#uses=0]
  store i32 0, i32* %0, align 4
  %2 = load i32* %0, align 4                      ; <i32> [#uses=1]
  store i32 %2, i32* %retval, align 4
  br label %return

; ## 復帰命令
; それぞれの関数は復帰命令で終わる。
; 復帰命令には、ret <型> <値>とret voidの2つの形がある。
; 下記の例の場合、i32型の値%retval1が戻り値として利用される。
; コードを追えば分かるが、%retval1はこの時点では値0を指している。
return:                                           ; preds = %entry
  %retval1 = load i32* %retval                    ; <i32> [#uses=1]
  ret i32 %retval1
}

; ## 関数宣言
; 関数宣言はdeclareキーワードで始まり、その後に戻り値の型、関数名、そしてオプションで関数の引数リストが続く。
; 関数宣言は、グローバルスコープで行う必要がある。
; 下記の関数宣言では、i8*型の引数を取りi32型の戻り値を返す関数putsを宣言している。
; LLVMアセンブリコード内での関数putsは、C言語における関数printfと同等である。
declare i32 @puts(i8*)
```


## 自分で中間コードを書いて実行してみる
```
$ vi my_assembly_code.s
$ lli my_assembly_code.s
Hello, world!
```

```
; my_assembly_code.s
declare i32 @puts(i8*)

@str = constant [14 x i8] c"Hello, world!\00"

define i32 @main() {
  %pointer = getelementptr [14 x i8]* @str, i64 0, i64 0
  call i32 @puts(i8* %pointer)
  ret i32 0
}
```
