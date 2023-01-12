---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# Practical Vim のメモ - Part II. Files

## 6. Manage Multiple Files
Vimで開かれたファイルをバッファと呼ぶ。
ファイルをディスク上に保存されているものだと考えた場合、バッファはメモリ上に保存されていると考えることができる。

### Tip 37 Track Open Files with the Buffer List

```
# ファイルを開く
$ cd code/files
$ vim *.txt

# Vimのバッファに２つのファイルがロードされる
# バッファリストを表示する
:ls
  1 %a   "a.txt"                        line 1  # %記号は、現在のウィンドウに表示されていることを意味する
  2 #    "b.txt"                        line 1  # #記号は、表示されていないスイッチ対象のファイルを意味する
:bnext
:ls
  1 #    "a.txt"                        line 1
  2 %a   "b.txt"                        line 1
```
バッファリストの選択
|コマンドライン|意味|
----|----
|:ls|バッファリストを表示する|
|:bnext(bn)|次のバッファに切り替える|
|:bprev(bp)|前のバッファに切り替える|
