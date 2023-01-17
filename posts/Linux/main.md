---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# Linuxコマンド

## 確認しながらスクリプトを書くときのTips

想定される入力を与えて出力を確認する

```
# 通常の動作
$ echo "鎌倉紀行" | awk '{if(/^$/){print "NO TITLE"}else{print}}END{if(NR==0){print "NO
 TITLE"}}'
鎌倉紀行

# 空行の場合
echo "" | awk '{if(/^$/){print "NO TITLE"}else{print}}END{if(NR==0){print "NO TITLE"}}'
NO TITLE

# 入力自体がない場合（「:」は何もしないコマンド）
: | awk '{if(/^$/){print "NO TITLE"}else{print}}END{if(NR==0){print "NO TITLE"}}'
NO TITLE
```


