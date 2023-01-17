---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# Practical Vim のメモ - Part II. Files

## 6. Manage Multiple Files
- Vimで開かれたファイルをバッファと呼ぶ。
- ファイルとバッファの違いを考えるとき、ファイルはディスク上に保存され、バッファはメモリ上に保存されていると考えるとわかりやすい。

### Tip 37 Track Open Files with the Buffer List

```
# バッファリストを表示する
# %記号: カレントウィンドウに表示されているアクティブバッファ
# #記号: オルタネートバッファ。<C-^>でトグル可能
:ls
  1 %a   "a.txt"                        line 1  
  2 #    "b.txt"                        line 1  
:bnext
:ls
  1 #    "a.txt"                        line 1
  2 %a   "b.txt"                        line 1
```

バッファの選択と削除

|コマンドライン|意味|
----|----
|:ls|バッファリストを表示する|
|<C-^>|オルタネートバッファとトグルする|
|:bnext (:bn)|次のバッファに切り替える|
|:bprev (:bp)|前のバッファに切り替える|
|:bfirst (:bf)|最初のバッファに切り替える|
|:blast (:bl)|最後のバッファに切り替える|
|:buffer NUM (:bN)|数字のバッファに切り替える|
|:bdelete (:bd)|アクティブなバッファを削除する|
|:bdelete N1 N2 .. (:bd N1 N2 ..)|バッファを削除する|
|:N1 N2 .. bdelete (:N1 N2 .. bd)|バッファを削除する|
|:N1,N5 bdelete (:N1,N5 bd)|連続するバッファ番号を範囲指定して削除する|
|:%bdelete (:%bd)|すべてのバッファを削除する|

### Tip 38 Group Buffers into a Collection with the Argument List

次はここからやる…
