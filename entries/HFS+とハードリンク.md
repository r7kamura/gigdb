Macでls -lというコマンドを実行したとき、ファイルパーミッションの欄に@というのが表示されるのが気になったので調べてみた。

```
$ ls -l
-rw-r--r--  1 r7kamura  staff       240  1 20 18:52 foo.md
-rw-r--r--  1 r7kamura  staff      5380  1  3 02:23 bar.md
-rw-r--r--@ 1 r7kamura  staff    163048  2 10 14:04 baz.md
```

### HFS+
何も考えずにGoogleで「Macでls -lしたときにパーミッションの部分の末尾についてる @ の記号の意味」と検索したらヒットした。
どうやらMacの採用しているHFS+というファイルシステムによって拡張されたものらしい。
http://okwave.jp/qa/q5511482.html

### TimeMachineとハードリンク
更に調べると、HFS+とハードリンクの関係性がTimeMachineに関連して紹介されていた。
http://appleinsider.com/articles/07/10/12/road_to_mac_os_x_leopard_time_machine/page/3

* 通常ディレクトリへのハードリンクは扱いにくい為サポートされることは少ない
* MacではTimeMachineの為にHFS+というファイルシステム上でこれを実装している
* ハードリンクにより、無駄にデータを複製することなくバックアップが実現されている