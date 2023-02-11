---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# Mastering Linux Shell Scripting 

## 1.4.1 コマンドの種類

typeでコマンドの種類を表示する。

```
$ type ls
ls は `ls --color=auto' のエイリアスです

# 特定のコマンドにマッチする全てを表示させる
$ type -a ls
ls は `ls --color=auto' のエイリアスです
ls は /bin/ls です

# 種類だけ表示させる
$ type -t ls
alias

# 引数を複数与える
$ type ls pwd quote pwd do id
ls は `ls --color=auto' のエイリアスです
pwd はシェル組み込み関数です
quote は関数です
quote ()
{
    local quoted=${1//\'/\'\\\'\'};
    printf "'%s'" "$quoted"
}
pwd はシェル組み込み関数です
do はシェルの予約語です
id は /usr/bin/id です
```

コマンドの種類

- エイリアス（alias）
- 関数（function）
- シェルの組み込みコマンド（shell builtin）
- キーワード（keyword）
- ファイル（file）


## 1.6.1 Hello World!

hello1.sh

```
#!/bin/bash
echo "Hello World!"
exit 0
```

## 1.6.3 コマンドの終了ステータスをチェックする

```
$ hello1.sh && echo $?
Hello World!
0
```

### 1.6.4 一意の名前を確保する

コマンドの種類を確認する。

```
$ type hello1.sh
hello1.sh はハッシュされています (/home/takaaki/bin/hello1.sh)

$ type -a hello1.sh
hello1.sh は /home/takaaki/bin/hello1.sh です

$ type -t hello1.sh
file
```

### 1.6.5.1 引数をつけてスクリプトを実行する

|引数の識別子|説明|
|---|---|
|$0|スクリプトそのものの名前。使い方の説明文の中でよく使われる|
|$1|位置引数。スクリプトに渡された最初の引数。${1}も可|
|${10}|位置引数。2桁以上の書き方|
|$#|引数の数|
|$\*|全ての引数を参照する|

hello2.sh

```
#!/bin/bash
echo "Hello ${1}"
exit 0
```

```
$ hello2.sh takaaki
Hello takaaki
```

### 1.6.5.2 正しい引用符の重要性

スペースは通常、シェルによって区切られるデフォルトのフィールドとして解釈されるため、誤った解釈を防ぐため引用符で保護する。


```
# 単一引用符（シングルクォーテーション）
$ echo '$USER earns $4'
$USER earns $4

# 二重引用符（ダブルクォーテーション）+ エスケープなし
$ echo "$USER earns $4"
takaaki earns 

# 二重引用符（ダブルクォーテーション）+ エスケープあり
$ echo "$USER earns \$4"
takaaki earns $4

# 引用符なし
$ echo $USER earns $4
takaaki earns
```

### 1.6.6 スクリプト名を表示する

Shell変数

|変数|説明|
|---|---|
|$#|引数の数|
|$\*|$0以外の引数|
|$0|スクリプト名|

hello2.sh
```
#!/bin/bash
echo "\$0: $0"
echo "\$(basename \$0): $(basename $0)"
echo "\`basename \$0\`: `basename $0`"
echo "\$*: $*"
echo "\$\#: $#"
exit 0
```

```
$ hello2.sh
$0: /home/takaaki/bin/hello2.sh
$(basename $0): hello2.sh
`basename $0`: hello2.sh
$*: takaaki yamada osaka
$\#: 3
```

### 1.7.1 ユーザー定義変数

配列

```
myarr=(one two three four five)
echo ${myarr[1]} # twoが表示される
echo ${myarr[*]} # すべて表示される

unset myarr[1] # twoを削除する
echo ${myarr[*]} # one three four five と表示される

unset myarr # 配列が初期化される
```

### 1.7.2 環境変数

```
$ printenv | grep "^USER" -C 3
SSH_AUTH_SOCK=/tmp/ssh-wFzZIU0TLi/agent.18576
COLORS=256
XDG_SESSION_ID=1064
USER=takaaki
PWD=/home/takaaki/bin
LINES=55
HOME=/home/takaaki

$ printenv HOME
/home/takaaki
```

### 1.8 変数のスコープ

- 変数のスコープは、変数を作成したプロセスに限られる。
- 同一プロセスの他ファイルから参照するには、exportコマンドでエクスポートする必要がある。
- 尚、他のファイルから変更することはできない。

### 1.9 コマンド置換

- バッククォート文字（`）
- $()のように、ドル記号の形式を使用する

```
cur_dir=`pwd`
cur_dir=$(pwd)
```



