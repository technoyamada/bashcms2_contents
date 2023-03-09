---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# Mastering Linux Shell Scripting 
# 1章　bashのスクリプトとは何か、なぜそれが必要なのか？
## 1.1　技術要件
## 1.2　Linuxのシェルの種類
## 1.3　bashスクリプトとは何か？
## 1.4　bashコマンドの階層
### 1.4.1　コマンドの種類
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
### 1.4.2　コマンドのパス
## 1.5　スクリプトのためのテキストエディターの準備
### 1.5.1　vimの設定
### 1.5.2　nanoの設定
### 1.5.3　geditの設定
## 1.6　スクリプトの作成と実行
### 1.6.1　Hello World!
hello1.sh

```
#!/bin/bash
echo "Hello World!"
exit 0
```
### 1.6.2　スクリプトを実行する
### 1.6.3　終了ステータスをチェックする
```
$ hello1.sh && echo $?
Hello World!
0
```
### 1.6.4　一意の名前を確保する
コマンドの種類を確認する。

```
$ type hello1.sh
hello1.sh はハッシュされています (/home/takaaki/bin/hello1.sh)

$ type -a hello1.sh
hello1.sh は /home/takaaki/bin/hello1.sh です

$ type -t hello1.sh
file
```
### 1.6.5　Hello Dolly!
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

### 1.6.6　スクリプト名を表示する
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
## 1.7　変数の宣言
### 1.7.1　ユーザー定義変数
配列

```
myarr=(one two three four five)
echo ${myarr[1]} # twoが表示される
echo ${myarr[*]} # すべて表示される

unset myarr[1] # twoを削除する
echo ${myarr[*]} # one three four five と表示される

unset myarr # 配列が初期化される
```
### 1.7.2　環境変数
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
## 1.8　変数のスコープ
- 変数のスコープは、変数を作成したプロセスに限られる。
- 同一プロセスの他ファイルから参照するには、exportコマンドでエクスポートする必要がある。
- 尚、他のファイルから変更することはできない。
## 1.9　コマンド置換
- バッククォート文字（`）
- $()のように、ドル記号の形式を使用する

```
cur_dir=`pwd`
cur_dir=$(pwd)
```
## 1.10　スクリプトのデバッグ
## 1.11　まとめ
## 1.12　練習問題

# 2章　インタラクティブなスクリプトの作成
## 2.1　オプション付きのechoの使用
echoでは最後に改行が追加されるが、インタラクティブにユーザーからの入力を待つ場合など、開業させたくないことがある。その方法は２つある。
```
# -n オプション
$ echo -n "Which directory do you want to use? "
# -e オプションでエスケープシーケンス「\c」を使う
$ echo -e "Which directory do you want to use? \c"
```
## 2.2　readを使った基本的なスクリプト
readに変数を与えないければ、入力された内容は$REPLYに入る。
## 2.3　スクリプトのコメント
- 先頭に「#」をつける。
- 複数行コメントアウトする場合は、便宜的に、ヒアドキュメントを用いる。
```
#!/bin/bash
# 変数展開が行われない（コロンは省略可能だが -x オプションでコマンドの実行履歴を残すには必要）
: <<END
abc
$(touch empty.txt)
xyz
END
# 変数展開が行われる
: <<"END"
abc
$(touch empty.txt)
xyz
"END"
# インデントが使えるようになる
: <<-"END"
  abc
  $(touch empty.txt)
  xyz
  "END"
```
## 2.4　readプロンプトを使ってスクリプトを拡張する
```
read -p "Enter your name: " name
```
## 2.5　入力文字数を制限する
```
read -n1 "Press any key to exit"
```
## 2.6　入力テキストの可視性を制御する
```
read -sn1 "Press any key to exit"
```
## 2.7　オプションの受け渡し
```
# $ ./script1.sh -a -b -c
while [ -n "$1" ]
do
  case "$1" in
    -a) echo "-a option used" ;;
    -b) echo "-b option used" ;;
    -c) echo "-c option used" ;;
     *) echo "Option $1 not an option" ;;
  esac
  shift
done
```
### 2.7.1　オプションとパラメーターの受け渡し
```
# $ ./script1.sh -a -b -c -- p1 p2 p3
while [ -n "$1" ]
do
  case "$1" in
    -a) echo "-a option used" ;;
    -b) echo "-b option used" ;;
    -c) echo "-c option used" ;;
    --) shift
        break ;;
     *) echo "Option $1 not an option" ;;
  esac
  shift
done

num=1
for param in $@
do
  echo "#$num: $param"
  num=$(( $num + 1 ))
done
```
### 2.7.2　オプションの値を読み取る
```
while [ -n "$1" ]
do
  case "$1" in
    -a) echo "-a option passed" ;;
    -b) param="$2"
        echo "-b option passed, with value $param" 
        shift ;;
    -c) echo "-c option passed" ;;
    --) shift
        break ;;
     *) echo "Option $1 not an option" ;;
  esac
  shift
done

num=1
for param in $@
do
  echo "#$num: $param"
  num=$(( $num + 1 ))
done
```
## 2.8　標準的であること
## 2.9　簡単なスクリプトを使って理解を深める
### 2.9.1　スクリプトを使ったバックアップ
```
#!/bin/bash
read -p "Which file types do you want to backup " file_suffix
read -p "Which directory do you want to backup to " dir_name
test -d $HOME/$dir_name || mkdir -m 700 $HOME/$dir_mode
find $HOME/bin -path $HOME/$dir_name -prune -o -name "*$file_suffix" -exec cp {} $HOME/$dir_name/ \;
exit 0
```
### 2.9.2　サーバーへの接続
### 2.9.3　バージョン1：ping
### 2.9.4　バージョン2：SSH
### 2.9.5　バージョン3：MySQL/MariaDB
### 2.9.6　ファイルの読み取り
```
while read line
do
  echo $line
done < ~/bin/script1.sh    
```
## 2.10　まとめ
## 2.11　練習問題

# 3章　条件の付加
## 3.1　コマンドラインリストを使ったシンプルな決定経路
- コマンドラインリストとは、ANDまたはORのいずれかの表記法を使って結合される、２つ以上の文のこと。
  - && -- AND
  - || -- OR
- 終了ステータス$0（正常終了は「0」）を用いて判定する  
```
$ test $PWD == $HOME || cd $HOME
```
## 3.2　リストを使ってユーザー入力を検証する
```
test -z "$1" || echo "Hello $1"
```
## 3.3　シェル組み込みコマンドのtestの使用
```
# 引数を与えないと終了ステータスはfalseになる
$ test; echo $?
1
# 基本的な構文
$ test EXPRESSION
# 複数の式を評価するとき
$ test EXPRESSION -a EXPRESSION
$ test EXPRESSION -o EXPRESSION
# 別の書き方
$ [ EXPRESSION ]
```
### 3.3.1　文字列のテスト
```
# 基本的な構文
test "$USER" = root
[ "$USER" = root ]
# 真偽を反転させる
test ! "$USER" = root
[ ! "$USER" = root ]
# 文字列の長さを用いた真偽判定
test -n $SSH_TTY # nonzero
test -z $SSH_TTY # zero
```
### 3.3.2　整数のテスト
```
test "$#" -gt 0
[ "$#" -gt 0 ]
```
|式|
|---|
|number1 -eq number2|
|number1 -ge number2|
|number1 -gt number2|
|number1 -le number2|
|number1 -lt number2|
|number1 -ne number2|
### 3.3.3　ファイルの種類のテスト
```
# シンボリックリンク
test -h /usr/bin/awk
# file1 is newer than file2?
test file1 -nt file2
```
|オプション|説明|
|---|---|
|-h|シンボリックリンクであるかどうか|
|-d|ディレクトリであるかどうか|
|-e|形式を問わずファイルがあるかどうか|
|-x|ファイルが実行可能かどうか|
|-f|ファイルが通常のファイルであるかどうか|
|-r|ファイルが読み取り可能かどうか|
|-p|ファイルが名前付きパイプかどうか|
|-b|ファイルがブロックデバイス（ハードディスクやUSBメモリなど）かどうか|
|file1 -nt file2|file1がfile2より新しいかどうか|
|file1 -ot file2|file1がfile2より古いかどうか|
|-0 file|ファイルの所有者がログイン中のユーザーであるかどうか|
|-c|ファイルがキャラクターデバイス（キーボードやマウスなど）であるかどうか|
## 3.4　ifを使って条件文を作成する
```
if [ "$#" -lt 1 ] ; then
  echo "Usage: $0 <name>"
  exit 1
fi
echo "Hello $1"
exit 0
```
## 3.5　elseを使ってifを拡張する
```
if [ "$#" -lt 1 ] ; then
  read -p "Enter a name: " name
else
  name="$1"
fi
echo "Hello ${name}"
exit 0
```
## 3.6　testコマンドを伴うif文
### 3.6.1　文字列のチェック
|式|説明|
|---|---|
|[ "string1" = "string2" ]|string1がstring2と同じかどうか|
|[ "string1" != "string2" ]|string1がstring2と同じでないかどうか|
|[ "string1" \\\< "string2" ]|string1がstring2より小さいかどうか|
|[ "string1" \\\> "string2" ]|string1がstring2より大きいかどうか|
|[ -n "string1" ]|string1がゼロより長いかどうか|
|[ -z "string1" ]|string1の長さがゼロかどうか|
### 3.6.2　ファイルやディレクトリーのチェック
```
if [ -d "$mydir" ]
```
### 3.6.3　数値のチェック
```
if [ 12 -gt 10 ]
```
### 3.6.4　テストの結合
```
if [ -d "$mydir" ] && [ -n "$name" ]; then
if [ -d "$mydir" ] || [ -n "$name" ]; then
```
## 3.7　elifを使って条件を増やす
### 3.7.1　elifを使ってbackup2.shを作成する
```
#!/bin/bash -xv
read -p "Choose H(gzip2), M(gzip) or L(tar) " file_compression
read -p "Which directory do you want to backup to " dir_name
test -d "$HOME/$dir_name" || mkdir -m 700 "$HOME/$dir_name"

backup_dir=$HOME/$dir_name

tar_h="-cjvf $backup_dir/b.tar.gzip2 --exclude $backup_dir $HOME"
tar_m="-czvf $backup_dir/b.tar.gz --exclude $backup_dir $HOME"
tar_l="-cvf $backup_dir/b.tar --exclude $backup_dir $HOME"

if [ "$file_compression" = "H" ]; then
  tar_opt=$tar_h
elif [ "$file_compression" = "M" ]; then
  tar_opt=$tar_m
else
  tar_opt=$tar_l
fi

tar $tar_opt
exit 0
```
## 3.8　case文の使用
```
#!/bin/bash
if [ ! "$#" -eq 2 ]; then
  echo "<学生> <成績>を指定してください"
  exit 2
fi

case "${2^^}" in # パラメータ展開
  [A-C]) echo "$1 は優れた生徒です";;
  [D])   echo "$1 はもう少し頑張る必要があります";;
  [E-F]) echo "$1 は来年もっと頑張りましょう";;
  *)     echo "$1 $2 について評価することができません"
esac
```
case文での文字列のマッピング
```
#!/bin/bash
case "$1" in
  orange)         echo "orangeという文字列にマッチ";;
  apple | banana) echo "appleまたはbananaという文字列にマッチ";;
  "*AT" | "*day") echo "*ATまたは*dayという文字列にマッチ。ワイルドカードは機能しない";;
  *AT | *day)     echo "ワイルカードが機能する";;
  [A-Z]*)         echo "先頭がアルファベットで始まる任意の長さの文字列にマッチ";;
  [^A-Z]*)        echo "先頭がアルファベット以外で始まる任意の長さの文字列にマッチ";;
  [!0-9]*)        echo "先頭が数字以外で始まる任意の長さの文字列にマッチ";;
  *)              echo "どれにもマッチしなかった場合";;
esac
```
変数を引用符で囲む場合の注意点
```
#!/bin/bash
pattern="*day"

case "$1" in
  '$pattern') echo '$patternという文字列にマッチ。変数は展開されない';;
  "$pattern") echo "*dayという文字列にマッチ。変数は展開されるがワイルドカードは機能しない";;
  $pattern)   echo "ワイルドカードが機能する";;
  *)          echo "どれにもマッチしなかった場合";;
esac
```
## 3.9　レシピ：grepのフロントエンドを作成する
```
#!/bin/bash
usage="usage: search.sh <file> <pattern> <operator>"

if [ ! "$#" -eq 3 ] ; then
  echo "${usage}"
  exit 2
fi

[ ! -f "$1" ] && echo "$1 が存在しません" && exit 3

case "$3" in
  [cC]) mesg="$1 の中で $2 にマッチする行数を数えます"
        opt="-c"
  ;;
  [pP]) mesg="$1 の中で $2 にマッチする行を表示します"
        opt=""
  ;;
  [dD]) mesg="$1 から $2 にマッチする行を除いて全て表示します"
        opt="-v"
  ;;
  *)    echo "$1 $2 $3 を評価できません"
        exit 1
  ;;
esac

echo ${mesg}
grep ${opt} $2 $1
exit 0
```
## 3.10　まとめ
## 3.11　練習問題

# 4章　コードスニペットの作成
## 4.1　短縮入力
.vimrc
```
abbr _bash #!/bin/bash
```
## 4.2　コードスニペットの使用
例えば Vim の場合、~/snippets/if に以下のスニペットを作成しておく。
```
if [ -z "$1" ] ; then
  echo "Usage: $0 <name>"
  exit 2
fi
```
Vim から呼び出す。
```
:r ~/snippets/if
```
### 4.2.1　ターミナルに色を導入する
例えば Vim の場合、~/snippets/color に以下のスニペットを作成しておく。
```
RED="\033[31m"
GREEN="\033[32m"
BLUE="\033[34m"
RESET="\033[0m"
```
スクリプト内で読み込む。
```
#!/bin/bash
source $HOME/snippets/color
if [ -z "$1" ] ; then
  echo -e "${RED}Usage: $0 <name>${RESET}"
  exit 2
fi
echo "Hello $1"
```
## 4.3　VS Codeを使ったスニペットの作成
## 4.4　まとめ
## 4.5　練習問題

# 5章　代替構文
## 5.1　testコマンドの要約
### 5.1.1　ファイルのテスト
```
test -f /etc/hosts
test -f /etc/hosts
```
### 5.1.2　ロジックの追加
```
test -f /etc/hosts -a -r /etc/hosts
```
### 5.1.3　角括弧の詳細
```
$ type -a [
[ はシェル組み込み関数です
[ は /usr/bin/[ です
```
## 5.2　パラメーターのデフォルト値の設定
### 5.2.1　変数
### 5.2.2　特殊パラメーター
```
$ echo "My shell is $0 and the shell options are: $-"
My shell is /bin/bash and the shell options are: himBHs
```
|オプション|説明|
|---|---|
|h|hashallの略。PATHパラメータを使ってプログラムが検索されるようにする|
|i|インタラクティブなシェルであることを示す|
|m|monitorの略。bgi や fg を使ってコマンドをバッググラウンドに移したり戻したりできる|
|B|ブレース展開（波括弧による展開）を可能にする|
|H|履歴からコマンドを繰り返すために、実行するコマンドの履歴展開（historyコマンドで確認後、!501 などのように実行）を可能にする|
|s|コマンドを標準入力から読み込む。インタラクティブなシェルを起動するとき、位置パラメータを利用を可能にする|
### 5.2.3　デフォルト値の設定
方法1
```
#!/bin/bash
name="$1"
[ -z "$1" ] && name="Anonymous"
echo "Hello ${name}"
exit 0
```
方法2（パラメータ置換）
```
#!/bin/bash
name=${1-"Anonymous"}
echo "Hello ${name}"
exit 0
```
## 5.3　疑わしいときは……引用符を！
※※※パラメータ展開は引用符で囲む！※※※
## 5.4　[[を使った高度なテスト
```
$ type [[
[[ はシェルの予約語です
```
### 5.4.1　空白文字
[[ は、キーワード（予約語）として、引数が bash によって展開される前に解析する。よって、[[ 内のパラメータを引用符で囲む必要がない。
### 5.4.2　その他の高度な機能
二重角括弧を使ったスクリプトはBourneシェルでは実行できない。
```
# パターンマッチング
$ [[ $FILE = *.pl ]] && cp "$FILE" scripts/
# 正規表現
$ [[ $FILE =~ \.pl$ ]] && cp "$FILE" scripts/
# 正規表現スクリプト
shopt -s nocasematch
if [[ $REPLY =~ colou?r ]] ; then
(省略)
shopt -u nocasematch
```
## 5.5　((を使った算術演算
### 5.5.1　単純な計算
bashでの二重丸括弧の構成体は，算術展開を可能にする。
```
$ (( a = 2 + 3 ))
$ echo $a
5
$ let a=2+3
$ echo $a
5
$ echo $((2+3))
5
```
### 5.5.2　パラメーター操作
二重括弧による算術展開は、カウンターをインクリメントするのに重宝する。
```
$ COUNT=1
$ ((COUNT++))
$ echo $COUNT
2
$ COUNT=10
$ ((COUNT=COUNT-1))
$ echo $COUNT
```
### 5.5.3　標準的な算術テスト
二重丸括弧内では、-gt の代わりに > が利用できる。
```
$ COUNT=10
$ ((COUNT--))
$ ((COUNT > 1)) && echo "Count is greater than 1"
Count is greater than 1
```
## 5.6　まとめ
## 5.7　練習問題

# 6章　ループを使った反復処理
## 6.1　forループ
あるリストについて繰り返し処理を行う。
for ループの右側のリストから順に値が読み込まれる。
```
$ for u in bob alex ; do echo "$u"; done
bob
alex
```
特殊変数 $@ を使って
```
#!/bin/bash
echo "You are using $(basename $0)"
for name in "$@"
do
  echo "Hello ${name}"
done
exit 0
```
リストを動的に生成することもでき、さまざまなファイル取得のテクニックを利用することができる。
```
$ for f in * ; do ls -l "$f"; done
-rwxrwxr-x 1 takaaki takaaki 299  2月 13 15:44 backup.sh
-rwxrwxr-x 1 takaaki takaaki 599  2月 14 15:56 search.sh
-rwxrwxr-x 1 takaaki takaaki 133  2月 15 07:09 test.sh
```
## 6.2　高度なforループ
## 6.3　IFS（内部フィールドセパレーター）
エスケープ文字として解釈させるにはドル記号と一緒に単一引用符を使う必要がある。二重引用符ではエスケープ文字として解釈されない。
```
#!/bin/bash
file="file1.txt"
IFS=$'\n'
for var in $(cat "${file}")
do
  echo " $var"
done
exit 0
```
## 6.4　ディレクトリーとファイルのチェック
```
#!/bin/bash
for path in ./\*
do
  if [ -d "$path" ]
  then
    echo "$path is a directory"
  elif [ -f "$path" ]
  then
    echo "$path is a file"
  fi
done
```
## 6.5　C言語スタイルのforループ
C言語の書き方
```
for (v = 0; v < 5; v++)
{
  print("Value is %d\n", v);
}    
```
bashでインクリメントするには、二重丸括弧で算術演算を利用する。
```
#!/bin/bash
for (( v=1; v <= 10; v++ ))
do
  echo "Value is $v"
done
```
## 6.6　ネストされたループ
```
#!/bin/bash
for (( v1 = 1; v1 <= 5; v1++ ))
do
  echo "First loop $v1: "
  for (( v2 = 1; v2 <= 3; v2++ ))
  do
    echo " Second loop: $v2"
  done
done
```
## 6.7　ループの出力結果のリダイレクト
```
#!/bin/bash
for (( v1 = 1; v1 <= 5; v1++ ))
do
  echo "First loop $v1: "
  for (( v2 = 1; v2 <= 3; v2++ ))
  do
    echo " Second loop: $v2"
  done
done > file
```
### 6.7.1　ループの制御
|コマンド|説明|
|---|---|
|break|それ以降のリストの項目を処理することなく、ループから抜け出す|
|continue|ループないの現在のリスト項目の処理を中止し、次のリスト項目を使って処理を再開する|
```
#!/bin/bash
for f in \*;
do
  [ -d "$f" ] && break
done
echo "We have found a directory: $f"
```
```
#!/bin/bash
for f in \*;
do
  [ -d "$f" ] || continue
  dir_name="$dir_name $f"
done
echo "directory name(s): $dir_name"
```
## 6.8　whileループとuntilループ
- forループがあるリストについて繰り返し処理を行うのに対して、whileループとuntilループは、条件が真または偽になるという事実に基づいてループ処理を行うことができる。
- whileループは条件が真である限り繰り返し、逆にuntilループは偽である限り繰り返す。
```
#!/bin/bash
COUNT=10
while (( COUNT >=0 ))
do
  echo -e "$COUNT \c" # \c: produce no further output（改行を抑制）
  (( COUNT-- ))
done
echo
```
## 6.9　ファイルからの入力の読み込み
```
#!/bin/bash
if [ ! -f "$1" ]; then
  echo "The input to $0 should be a filename"
  exit 1
fi
echo "The following servers are up on $(date +%x)" > server.out
while read server
do
  ping -c1 "$server" > /dev/null && echo "Server up: $server" >> server.out
done < "$1"
cat server.out
```
## 6.10　オペレーター用メニューの作成
```
#!/bin/bash
while true
do
  clear
  echo "Choose an item: a, b or c"
  echo "a: Backup"
  echo "b: Display Calendar"
  echo "c: Exit"
  read -sn1
  case "$REPLY" in
    a) tar -C $HOME/bin -czvf $HOME/bin/backup.tgz $HOME/bin/tmp;;
    b) cal;;
    c) exit 0;;
  esac
  read -n1 -p "Press any key to continue"
done
```
## 6.11　まとめ
## 6.12　練習問題

# 7章　関数の作成
## 7.1　関数の導入
自身のシェル環境内の存在する関数を表示する。
```
$ declare -F
```
関数の定義方法は２種類あるが、functionキーワードを使う方法は、POSIX仕様との移植性のため推奨されていない。
```
# 推奨
function_name() {
  ...
}

# 非推奨
function <function_name> {
  ...
}
```
## 7.2　関数へのパラメーターの引き渡し
```
#!/bin/bash
is_file() {
  if [ ! -f "$1" ] ; then
    echo "$1 does not seem to be a file"
    exit 2
  fi
}

clean_file() {
  is_file "$1"
  BEFORE=$(wc -l "$1")
  echo "The file $1 starts with $BEFORE"
  sed -i.bak '/^\s*#/d;/^$/d' "$1"
  AFTER=$(wc -l "$1")
  echo "The file $1 is now $AFTER"
}

read -p "Enter a file to clean: "
clean_file "$REPLY"
exit 1
```
### 7.2.1　配列の引き渡し
配列を渡すには $@ を用いる。$1 を用いると、最初の配列要素だけが渡される。
```
#!/bin/bash
my_func() {
  arr=$@
  echo "The array from inside the function: ${arr[*]}"
}

my_arr=(5 10 15)
echo "The original array is: ${my_arr[*]}"
my_func ${my_arr[*]}
```
## 7.3　変数のスコープ
デフォルトでは、関数内で宣言した変数もすべてグローバル変数。
```
#!/bin/bash
myvar=30
myfunc() {
  local myvar=10
}
myfunc
echo $myvar
```
## 7.4　関数から値を返す
関数から値を返す方法
- グローバル変数に代入する
- returnコマンドで終了ステータスを返す
- echoコマンドで標準出力に出力する
```
# returnを用いる方法
# $?の値は$変数$statusに代入して使用すること。終了ステータスは刻々と変化するため。
#!/bin/bash
check_file() {
  if [ -f "$1" ] ; then
    return 0
  elif [ -d "$1" ] ; then
    return 1
  else
    return 2
  fi
}

if [ -z "$1" ] ; then
  echo "Usage: $0 file"
  exit 1
fi

check_file "$1"
status=$?
if [ $status = 0 ] ; then
  echo "$1 is a regular file"
elif [ $status = 1 ] ; then
  echo "$1 is a directory"
else
  echo "$1 is a device file"
fi
```
```
# echoを用いる方法
#!/bin/bash
to_lower()
{
  input="$1"
  output=$(echo $input | tr [A-Z] [a-z])
  echo $output
}

while true
do
  read -p "Enter c to continue or q to exit: "
  REPLY=$(to_lower "$REPLY")
  if [ $REPLY = "q" ] ; then
    break
  fi
done
echo "Finished"
```
## 7.5　再帰関数
```
#!/bin/bash
calc_factorial() {
  if [ $1 = 1 ] ; then
    echo 1
  else
    local var=$(( $1 - 1 ))
    local res=$(calc_factorial $var)
    echo $(( $res * $1 ))
  fi
}

read -p "Enter a number: " val
factorial=$(calc_factorial $val)
echo "The factorial of $val is: $factorial"
```
## 7.6　メニューでの関数の使用
```
#!/bin/bash
to_lower() {
  input="$1"
  output=$(echo $input | tr [A-Z] [a-z])
  echo $output
}

do_backup() {
  tar -C $HOME/bin -czvf $HOME/bin//backup.tgz $HOME/bin/tmp
}

show_cal() {
  if [ -x /usr/bin/ncal ] ; then
    command="/usr/bin/ncal -w"
  else
    command="/usr/bin/cal"
  fi
  $command
}

while true
do
  clear
  echo "Choose an item: a, b or c"
  echo "a: Backup"
  echo "b: Display Calendar"
  echo "c: Exit"
  read -sn1
  REPLY=$(to_lower $REPLY)
  case "$REPLY" in
    a) do_backup;;
    b) show_cal;;
    c) exit 0;;
  esac
  read -n1 -p "Press any key to continue"
done
```
## 7.7　まとめ
## 7.8　練習問題

# 8章　ストリームエディターの導入
## 8.1　grepを使ってテキストを表示する
### 8.1.1　インターフェース上の受信データの表示
```
$ ifconfig ens3 | grep -i "rx packets"
        RX packets 125635299  bytes 8986671979 (8.9 GB)
```
### 8.1.2　ユーザーアカウントデータの表示
```
$ grep "$USER" /etc/passwd
takaaki:x:1000:1000:Takaaki Yamada,,,:/home/takaaki:/bin/bash
```
```
#!/bin/bash
read -p "Enter a user name: "
if (grep "$REPLY" /etc/passwd > /dev/null) ; then
  echo "The user $USER exists"
exit 0
fi
```
### 8.1.3　システム内のCPU数の表示
マッチする行を表示せず個数を返す。
```
$ cat /proc/cpuinfo | grep -c name
2
```
```
#!/bin/bash
source $HOME/bin/tmp/function.sh
check_core 4
read -p "Enter a user name: "
if (grep "$REPLY" /etc/passwd > /dev/null) ; then
  echo "The user $USER exists"
exit 0
fi
```
### 8.1.4　CSVファイルの解析
色を指定する場合は、「ESC[{色番号}m」と「ESC[0m or ESC[m」で囲む。\eと\033はESC文字を表す。
- \e[{色番号}m ... \e[0m
- \033[{色番号}m ... \033[0m
```
#!/bin/bash
OLDIFS="$IFS"
IFS=","
while read product price quantity
do
  echo -e "\e[1;34m${product} \
          =====\e[0m\n\
  price : \t ${price} \n\
  quantity : \t ${quantity}\n"
done < "$1"
IFS="$OLDIFS"
```
## 8.2　sedの基礎を理解する
```
$ sed -n 'p' /etc/passwd
$ sed -n '1,3 p' /etc/passwd
$ sed -n '/^takaaki/ p' /etc/passwd
```
### 8.2.1　置換コマンド
sed 's/パターン/置換文字列/フラグ'
|フラグ|内容|
|---|---|
|p|置換が行われた場合に痴漢語の文字列を出力する|
|g|すべての出現箇所を置換する|
|w|結果をファイルに書き出す|
### 8.2.2　グローバル置換
```
$ sed 's/sed/Linux sed/g' inputfile
$ sed 's/sed/Linux sed/gw outfile' inputfile
```
### 8.2.3　置換の限定
```
$ sed '2s/old/new/' inputfile

$ sed '3,5s/old/new/' inputfile

$ sed '2,$s/old/new' inputfile
```
### 8.2.4　ファイルの編集
- 結果をファイルに出力するには wフラグ を使うが、ファイルそのものを編集するには、-i オプションを使う。
- -nオプション や pコマンド は不要
```
$ cat inputfile 
takaaki:x:1000:1000:Takaaki Yamada,,,:/home/takaaki:/bin/bash
$ sed -i 's;/bin/bash;/bin/zsh;' inputfile 
$ cat inputfile 
takaaki:x:1000:1000:Takaaki Yamada,,,:/home/takaaki:/bin/zsh
```
- バックアップファイルを作成する場合は、-iオプションに適当なsuffixを指定すれば良い。
```
$ sed -i.bak 's;/bin/bash;/bin/zsh;' inputfile 
```
## 8.3　sedのその他のコマンド
### 8.3.1　削除コマンド
```
# 3行目を削除する
$ sed '3d' inputfile
# 3〜5行目を削除する
$ sed '3,5d' inputfile
# 4行目から最後まで削除する
$ sed '4,$d' inputfile
```
### 8.3.2　挿入コマンドと追加コマンド
```
$ cat sample-code/ch08/myfile3
First line
Second line
Third line
Fourth line
# iコマンド: 指定行の前に挿入する
$ sed '2i\*** inserted ***' sample-code/ch08/myfile3
First line
*** inserted ***
Second line
Third line
Fourth line
# aコマンド: 指定行の後ろに挿入する
$ sed '2a\*** inserted ***' sample-code/ch08/myfile3
First line
Second line
*** inserted ***
Third line
Fourth line
```
### 8.3.3　変更コマンド
行全体を置き換える。
```
$ sed '2c\*** modified ***' sample-code/ch08/myfile3 
First line
*** modified ***
Third line
Fourth line
```
### 8.3.4　変換コマンド
trコマンド のような働き。
```
$ echo 'This is an abc test' | sed 'y/b/B/'
This is an aBc test
```
## 8.4　sedの複数のコマンド
-eオプション を用いて、コマンドを ; で区切る。
```
$ sed -e 's/First/1st/; s/Second/2nd/' sample-code/ch08/myfile3
1st line
2nd line
Third line
Fourth line
```
## 8.5　まとめ
## 8.6　練習問題

# 9章　Apacheバーチャルホストの自動化
## 9.1　技術要件
## 9.2　Apacheの名前ベースのバーチャルホスト
### 9.2.1　バーチャルホストのテンプレートの作成
### 9.2.2　最初のステップ
行番号を表示して出力する。
```
$ sed = httpd.conf | sed 'N;s/\n/ /'
```
### 9.2.3　行の取り出し
行番号を指定する方法
```
$ sed -n '355,361 p' httpd.conf 
#<VirtualHost *:80>
#    ServerAdmin webmaster@dummy-host.example.com
#    DocumentRoot /www/docs/dummy-host.example.com
#    ServerName dummy-host.example.com
#    ErrorLog logs/dummy-host.example.com-error_log
#    CustomLog logs/dummy-host.example.com-access_log common
#</VirtualHost>
```
開始タグと終了タグを検索する方法
```
$ sed -n '/#<VirtualHost[^>]*>/,/^#<\/VirtualHost>/p' httpd.conf
#<VirtualHost *:80>
#    ServerAdmin webmaster@dummy-host.example.com
#    DocumentRoot /www/docs/dummy-host.example.com
#    ServerName dummy-host.example.com
#    ErrorLog logs/dummy-host.example.com-error_log
#    CustomLog logs/dummy-host.example.com-access_log common
#</VirtualHost>
```
### 9.2.4　sedスクリプトファイル
vh.sed
```
/#<VirtualHost[^>]*>/,/^#<\/VirtualHost>/ {
s/^#//
w template.txt
}
```
```
$ sed -nf vh.sed httpd.conf
$ cat template.txt 
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot /www/docs/dummy-host.example.com
    ServerName dummy-host.example.com
    ErrorLog logs/dummy-host.example.com-error_log
    CustomLog logs/dummy-host.example.com-access_log common
</VirtualHost>
```
## 9.3　バーチャルホストの作成の自動化
```
#!/bin/bash
WEBDIR=/www/docs
CONFDIR=/etc/httpd/conf.d
TEMPLATE=$HOME/template.txt
[ -d $CONFDIR ] || mkdir -p $CONFDIR
sed s/dummy-host.example.com/$1/ $TEMPLATE > $CONFDIR/$1.conf
mkdir -p $WEBDIR/$1
echo "New site for $1" > $WEBDIR/$1/index.html
```
### 9.3.1　サイト作成時にデータ入力を促す
- ${REPLY^^} で、$REPLY変数を大文字に変換している。
- sed -i で、マッチした行の前にテキストを追加している。
```
#!/bin/bash
WEBDIR=/www/docs/$1
CONFDIR=/etc/httpd/conf.d
CONFFILE=$CONFDIR/$1.conf
TEMPLATE=$HOME/template.txt
[ -d $CONFDIR ] || mkdir -p $CONFDIR
sed s/dummy-host.example.com/$1/ $TEMPLATE > $CONFDIR/$1.conf
mkdir -p $WEBDIR/$1
echo "New site for $1" > $WEBDIR/$1/index.html
read -p "Do you want to restrict access to this site? y/n "
[ ${REPLY^^} = 'N' ] && exit 0
read -p "Which network should we restrict access to: " NETWORK
sed -i "<\/VitualHost>/i <Directory $WEBDIR >\
  \n  Order allow,deny\
  \n  Allow from 127.0.0.1\
  \n  Allow from $NETWORK\
  \n</Directory>" $CONFFILE
```
## 9.4　まとめ
## 9.5　練習問題

# 10章　AWKの基礎
AWKの特徴
- テキストファイルを1行ずつ読み込む
- メインブロックのコードは、行の基準にマッチする各行に対して実行される
- BEGINブロックとENDブロックのコードは一度だけ実行される
- 組み込み変数や独自に定義した変数を扱うことができる
- whileやforなどのループ処理、ifの条件処理が行える
## 10.1　AWKの背後にある歴史
## 10.2　ファイルの内容の表示とフィルタリング
- awkが用意している変数
  - $0 : 行全体
  - $1 : 最初のフィールド
  - $2 : 2番目のフィールド
  - ...
- awkのデフォルトのセパレーター
  1. 1つの/任意の数のスペース
  1. タブ
  1. 改行
セパレーターを変更する方法
```
$ awk -F":" '{ print }' /etc/passwd
$ awk 'BEGIN{ FS=":" } { print $1 }' /etc/passwd
```
awkのその他の内部変数
```
$ awk 'BEGIN{ FS=":" } { print $1 } END{ print NR }' /etc/passwd
```
選択した行だけ表示する
```
$ awk 'NR > 24' /etc/passwd
$ awk 'NR==21,NR==25 { print }' /etc/passwd
$ awk '/bash$/' /etc/passwd
```
## 10.3　AWKの変数
|変数名|説明|
|---|---|
|FIELDWIDTHS|フィールドの長さを指定する|
|RS|レコードセパレーターを指定する|
|FS|フィールドセパレーターを指定する|
|ORS|出力レコードセパレーターを指定する|
|OFS|出力フィールドセパレーターを指定する|
|FILENAME|処理されるファイル名を保持する|
|NF|現在のレコードのフィールド数を保持する|
|FNR|現在のファイルにおいて処理したレコードの数を保持する|
|NR|現在までに処理したレコードの数を保持する|
|IGNORECASE|大文字と小文字を区別しない|
```
$ cat sample-code/ch10/myfile1 | awk 'BEGIN{RS=""; FS="\n"; OFS=" | "} {print FNR,$1,$3}'
1 | John Doe | (123) 455-3584
2 | Mokhtar Ebrahim | (456) 352-3541
```
### 10.3.1　ユーザー定義変数
```
$ awk '
> BEGIN{
> var1=2
> var2=3
> var3= var1 + var2
> print var3
> }'
5
```
```
$ awk '
> BEGIN{
> str1 = "Welcome"
> str2 = " To shell scripting"
> str3 = str1 str2
> print str3
> }'
Welcome To shell scripting
```
## 10.4　条件文
### 10.4.1　if文
```
$ awk '{if ($1 > 50) { x = $1 * 2; print x } else { x = $1 * 3; print x }}' inputfile
150
90
160
140
60
180
```
### 10.4.2　whileループ
AWKではファイルのすべての行を処理するが、各行のフィールドに対して処理を繰り返したい場合は、whileループを用いる。
```
$ cat sample-code/ch10/myfile4
321 524 124
174 185 254
195 273 345

$ awk '{                      
total = 0
i = 1
while (i <= NF)
{
total += $i
i++
}
mean = total / 3
print "Mean value: ", mean
}' sample-code/ch10/myfile4
Mean value:  323
Mean value:  204.333
Mean value:  271
```
### 10.4.3　forループ
より少ない行数で同様のことができる。
```
$ awk '{
total = 0
for (field = 1; field <= NF; field++)
{
total += $field
}
mean = total / NF
print "Mean value: ", mean
}' sample-code/ch10/myfile4
Mean value:  323
Mean value:  204.333
Mean value:  271
```
## 10.5　出力結果の書式設定
```
$ awk 'BEGIN{ FS = ":"; printf "%20s %8s %20s\n", "Name", "UID", "Shell" }{ printf "%20s %8d %20s\n", $1, $3, $7 }' /etc/passwd | head
                Name      UID                Shell
                root        0            /bin/bash
              daemon        1    /usr/sbin/nologin
                 bin        2    /usr/sbin/nologin
                 sys        3    /usr/sbin/nologin
                sync        4            /bin/sync
               games        5    /usr/sbin/nologin
                 man        6    /usr/sbin/nologin
                  lp        7    /usr/sbin/nologin
                mail        8    /usr/sbin/nologin
```                
AWKの関数定義
```
$ awk 'function green(s) {
printf "\033[1;32m" s "\033[0m"
}
BEGIN { FS=":";
green("      Name       UID     Shell\n")}
{ printf "%10s %10d %10s\n", $1, $3, $7 } ' /etc/passwd | head
      Name       UID     Shell
      root          0  /bin/bash
    daemon          1 /usr/sbin/nologin
       bin          2 /usr/sbin/nologin
       sys          3 /usr/sbin/nologin
      sync          4  /bin/sync
     games          5 /usr/sbin/nologin
       man          6 /usr/sbin/nologin
        lp          7 /usr/sbin/nologin
      mail          8 /usr/sbin/nologin
```
## 10.6　UIDを使ってユーザー表示をさらにフィルタリングする
比較演算子を使う
```
$ awk -F":" '$3 > 999' /etc/passwd
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
takaaki:x:1000:1000:Takaaki Yamada,,,:/home/takaaki:/bin/bash
```
## 10.7　AWKの制御ファイル
10.5の内容を passwdawk に保存すれば制御ファイルとして利用できるようになる。
```
function green(s) {
  printf "\033[1;32m" s "\033[0m"
}
BEGIN { 
  FS=":";
  green("      Name       UID     Shell\n")
}
{ 
  printf "%10s %10d %10s\n", $1, $3, $7 
}
```
呼び出し方は次の通り。
```
$ awk -f passwd.awk /etc/passwd
```
### 10.7.1　組み込み関数
```
$ awk 'BEGIN{ x = sqrt(5); print x }'
2.23607

$ awk 'BEGIN{ x = "welcome"; print toupper(x) }'
WELCOME
```
## 10.8　まとめ
## 10.9　練習問題

# 11章　正規表現
## 11.1　正規表現エンジン
Linuxでは2つの正規表現エンジンを使うことができる。
- BRE (Basic Regular Expression)
- ERE (Extended Regular Expression)
## 11.2　BREパターンの定義
次の文字にマッチさせる場合はエスケープする必要がある。
```
. * [ ] ^ $ { } \ + ? | ( )
```
### 11.2.1　アンカー文字
|正規表現|説明|
|---|---|
|^|行の先頭|
|$|行の末尾|
- 空行以外を抜き出す。
```
$ cat sample-code/ch11/myfile1 | awk '!/^$/ {print $0}'
Lorem Ipsum is simply dummy text .
Lorem Ipsum has been the industry's standard dummy.
It has survived not only five centuries
It is a long established fact that a reader will be distracted.
```
### 11.2.2　ドット文字

### 11.2.3　文字クラス
### 11.2.4　一連の文字
### 11.2.5　特殊文字クラス
### 11.2.6　アスタリスク
## 11.3　EREパターンの定義
### 11.3.1　疑問符
### 11.3.2　プラス記号
### 11.3.3　波括弧
### 11.3.4　パイプ文字
### 11.3.5　正規表現のグループ化
## 11.4　grepの使用
## 11.5　まとめ
## 11.6　練習問題

# 12章　AWKを使ったログの集約
## 12.1　HTTPログファイルのフォーマット
## 12.2　Webログからのデータの表示
### 12.2.1　日付によるエントリーの選択
### 12.2.2　404エラーの集約
### 12.2.3　HTTPアクセスコードの集約
### 12.2.4　リソースのヒット数
### 12.2.5　画像のホットリンクの識別
## 12.3　最もランキングの高いIPアドレスの表示
## 12.4　ブラウザーデータの表示
## 12.5　Eメールログの処理
## 12.6　まとめ
## 12.7　練習問題

# 13章　AWKを使ったlastlogの改良
## 13.1　AWKの範囲を使ってデータを除外する
### 13.1.1　lastlogコマンド
### 13.1.2　AWKによる行のフィルタリング
### 13.1.3　マッチした行のカウント
## 13.2　フィールド数に基づく条件
## 13.3　AWKのレコードセパレーターを操作してXMLデータを処理する
### 13.3.1　Apacheバーチャルホスト
### 13.3.2　XMLカタログ
## 13.4　まとめ
## 13.5　練習問題

# 14章　bashスクリプトの代わりとしてのPython
## 14.1　Pythonとは何か？
## 14.2　PythonでのHello World
## 14.3　Pythonでの引数
## 14.4　引数の引き渡し
## 14.5　引数のカウント
## 14.6　重要な空白
## 14.7　ユーザー入力の読み取り
## 14.8　Pythonを使ってファイルに書き出す
## 14.9　文字列の操作
## 14.10　まとめ
## 14.11　練習問題

# 付録A　bashのその他の機能
## A.1　組み込みコマンド
### A.1.1　builtinコマンドとcommandコマンド
### A.1.2　declareコマンド
### A.1.3　getoptsコマンド
### A.1.4　mapfileコマンド（readarrayコマンド）
### A.1.5　printfコマンド
### A.1.6　selectコマンド
### A.1.7　setコマンド
### A.1.8　trapコマンド
### A.1.9　waitコマンド
## A.2　ヒアストリング
## A.3　ブレース展開
### A.3.1　数字のパターン生成
### A.3.2　アルファベットのパターン生成
### A.3.3　文字列のパターン生成
## A.4　パラメーター展開
### A.4.1　変数の定義の有無に基づく展開
### A.4.2　部分文字列の取得
### A.4.3　大文字小文字の変換
### A.4.4　文字列の削除、置換
### A.4.5　変数名が指定した文字列で始まる変数の一覧取得
### A.4.6　変数の変換
## A.5　プロセス置換

# 付録B　練習問題の解答
## 1章　bashのスクリプトとは何か、なぜそれが必要なのか？
## 2章　インタラクティブなスクリプトの作成
## 3章　条件の付加
## 4章　コードスニペットの作成
## 5章　代替構文
## 6章　ループを使った反復処理
## 7章　関数の作成
## 8章　ストリームエディターの導入
## 9章　Apacheバーチャルホストの自動化
## 10章　AWKの基礎
## 11章　正規表現
## 12章　AWKを使ったログの集約
## 13章　AWKを使ったlastlogの改良
## 14章　bashスクリプトの代わりとしてのPython

# 参考文献
# 索引

# コラム目次
## ヒアドキュメント
## case文での文字列のマッチング
