---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# 1日1問、半年以内に習得 シェル・ワンライナー160本ノック
## 第1部　シェルとコマンドに親しむ
### 第1章　Linux環境
#### 1.1　環境を準備する
#### 1.2　端末，シェル，コマンド，ファイルの関係を理解する
##### 練習1.2.a　端末を使う
##### 練習1.2.b　コマンドの止め方
##### 練習1.2.c　1＋1の計算
##### 練習1.2.d　ファイルへの保存
##### 練習1.2.e　ファイルとディレクトリの操作
##### 練習1.2.f　ファイルのパーミッション
##### 練習1.2.g　コマンドの調査
#### 1.3　頻出コマンドを覚える
##### 練習1.3.a　sedによる置換の練習
##### 練習1.3.b　grepによる検索の練習
##### 練習1.3.c　grepによる検索＆切り出しの練習
##### 練習1.3.d　awkによる検索と計算の練習
##### 練習1.3.e　sortとuniqによる集計
##### 練習1.3.f　xargsによる一括処理
##### 練習1.3.g　bashによるメタプログラミング
#### 1.4　ファイルを操作する
##### 練習1.4.a　GitHubからリポジトリをクローン
##### 問題1　ファイル名の検索
##### 問題2　画像ファイルの一括変換
##### 問題3　ファイル名の一括変更
##### 問題4　特定のファイルの削除
#### 1.5　もっとawkとsedに慣れる
##### 問題5　設定ファイルからの情報抽出
##### 問題6　端末に模様を描く
##### 問題7　消費税
##### 問題8　ログの集計
##### 問題9　ログの抽出
sed -n '/正規表現1/,/正規表現2/p' で行を範囲指定する。
```
$ cat 9/log_range.log | sed -n '/24\/Dec\/2016 21:..:../,/25\/Dec\/2016 03:..:../p'
192.168.77.248 - - [24/Dec/2016 21:12:20] "GET / HTTP/1.0" 200 4294
192.168.152.143 - - [24/Dec/2016 22:06:19] "GET / HTTP/1.0" 200 7255
192.168.6.132 - - [24/Dec/2016 23:00:42] "GET / HTTP/1.0" 200 4298
192.168.222.3 - - [25/Dec/2016 00:03:23] "GET / HTTP/1.0" 200 8547
192.168.101.95 - - [25/Dec/2016 01:01:40] "GET / HTTP/1.0" 200 8488
192.168.141.18 - - [25/Dec/2016 02:15:52] "GET / HTTP/1.0" 200 4533
192.168.110.169 - - [25/Dec/2016 03:06:54] "GET / HTTP/1.0" 200 3461
```
AWKでも同様に範囲指定できる。sedのpコマンドは不要。
```
$ cat 9/log_range.log | awk '/24\/Dec\/2016 21:..:../,/25\/Dec\/2016 03:..:../'
192.168.77.248 - - [24/Dec/2016 21:12:20] "GET / HTTP/1.0" 200 4294
192.168.152.143 - - [24/Dec/2016 22:06:19] "GET / HTTP/1.0" 200 7255
192.168.6.132 - - [24/Dec/2016 23:00:42] "GET / HTTP/1.0" 200 4298
192.168.222.3 - - [25/Dec/2016 00:03:23] "GET / HTTP/1.0" 200 8547
192.168.101.95 - - [25/Dec/2016 01:01:40] "GET / HTTP/1.0" 200 8488
192.168.141.18 - - [25/Dec/2016 02:15:52] "GET / HTTP/1.0" 200 4533
192.168.110.169 - - [25/Dec/2016 03:06:54] "GET / HTTP/1.0" 200 3461
```
AWKなら文字列の大小比較が使える。
```
$ cat 9/log_range.log | awk '$4" "$5 >= "[24/Dec/2016 21:00:00]" && $4" "$5 < "[25/Dec/201
6 03:59:60]"'
192.168.77.248 - - [24/Dec/2016 21:12:20] "GET / HTTP/1.0" 200 4294
192.168.152.143 - - [24/Dec/2016 22:06:19] "GET / HTTP/1.0" 200 7255
192.168.6.132 - - [24/Dec/2016 23:00:42] "GET / HTTP/1.0" 200 4298
192.168.222.3 - - [25/Dec/2016 00:03:23] "GET / HTTP/1.0" 200 8547
192.168.101.95 - - [25/Dec/2016 01:01:40] "GET / HTTP/1.0" 200 8488
192.168.141.18 - - [25/Dec/2016 02:15:52] "GET / HTTP/1.0" 200 4533
192.168.110.169 - - [25/Dec/2016 03:06:54] "GET / HTTP/1.0" 200 3461
```
##### 問題10　見出しの記法の変換
正規表現「+」は拡張正規表現の記号なので、-r または -E が必要。
```
$ cat headings.md 
# AAA

これはAAAです

# BBB

これはBBBです。
楽しいですね。

## CCC

これはCCCCです

## DDD

これはDDDです

$ cat headings.md | sed -E 's/^##\s+(.*)/\1\n---/' | sed -E 's/^#\s+(.*)/\1\n===/'
AAA
===

これはAAAです

BBB
===

これはBBBです。
楽しいですね。

CCC
---

これはCCCCです

DDD
---

これはDDDです
```
Pandocを用いた例
```
$ pandoc headings.md --from markdown+hard_line_breaks --to markdown+hard_line_breaks
AAA
===

これはAAAです

BBB
===

これはBBBです。
楽しいですね。

CCC
---

これはCCCCです

DDD
---

これはDDDです
```
#### 問題11　議事録の整理
xargsは空行を無視してくれる模様。
```
$ cat gijiroku.txt | xargs -n 2 | sed -E 's/^(..) /\1:/; s/^すず/鈴木/; s/^さと/佐藤/; s/^やま/山田/'
鈴木:あばばあばば
佐藤:あばばばばばばば！
山田:びっくりするほどユートピア！びっくりするほどユートピア！
鈴木:うひょひょひょｗｗｗｗｗやまｗｗやまｗｗｗ
佐藤:ひょおお？ひょおお？？？
鈴木:それでは会議を終わります
```
### 第2章　シェルの基本
- 世の中には「シェルの文法はクセがある」と言って嫌う人が多いが、扱う対象が異なるので、クセではなくて根本的に違うものと考えた方が良い。
- C/C++やPython、あるいはAWKで作ったプログラムは「コマンド」とみなせるが、シェルはそれを組み合わせるものなので、役割の階層が異なる。
#### 2.1　変数と制御構文，コマンドの入出力操作を把握する
##### 練習2.1.a　標準入出力・標準エラー出力
|file descriptor|説明|
|0<|標準入力|
|1>|標準出力|
|2>|標準エラー出力|

標準エラー出力のパイプ渡し
```
# n>&m は、n番を今m番がつながっている（参照している）先に振り向けるの意
$ sed 2>&1 | wc -l
39
# 標準出力燃標準エラー出力もまとめて右のコマンドに渡すの意
$ sed |& wc -l
39
```
##### 練習2.1.b　シェルと変数
変数を定義するときに、= の前後にスペースを入れてはいけない。
```
$ city=Osaka
```
「シェルはプログラミング言語だがコマンドとファイル操作のためのもの」なので、上の = の前後にスペースを入れてしまうと、city がコマンドで、 Osaka が引数と解釈してしまう。

また、変数をコマンドにリダイレクトする <<<（ヒアストリング）を用いることもできる。
```
$ cat <<< $city
```
##### 練習2.1.c　文字列の連結と置換
連結と置換
```
$ a=私は; b=俳優よ
a+=$b
```
変数展開(parameter expansion)

${変数名:開始文字位置:長さ}
```
$ cat <<< ${a:2:2}
俳優
```
${変数名/置換対象文字列/置換後の文字列}
```
$ c=${a/俳優/作家}; cat <<< $c
私は作家よ
```
##### 練習2.1.d　変数を使った計算
- Bashの変数は単なる文字列。
- 計算する場合は、$(( )) の中に計算式を書く**算術式展開**を用いる。
```
$ a=6; b=2

$ echo $((a+b)) $((b-a)) $((a*b)) $((b<<a))
8 -4 12 128
```
- b<<a はビット演算で、2進数で2は10なので6桁左に移すと、10000000となり10進数の128となる。
- また、算術式展開の中で、変数に $ をつけても良い。
```
$ echo $(($a + $b)) $(($b - $a)) $(($a * $b)) $(($b << $a))
8 -4 12 128
```
##### 練習2.1.e　クォートと変数
- awkを使う際、引数に与えるAWKのコードをシングルクォート囲むが、これには2つの役割がある。
  - {print 1+1} のように空白の入った引数を、ひとつにまとめて引き渡し {print と 1+1} の2つの引数として渡さない
  - $1 などのAWKの変数が、シェルの変数として解釈されることを防ぐ
i
- Bashはシングルクォートで囲まれた文字列に何も手を加えない。grepやsedの引数も、同じ理由でシングルクォートで囲っている。
- しかしこれでは困ることがあるため、ダブルクォートで囲む方法も用意されている。
  - Bashの変数は解釈される
  - ホームディレクトリを表す「~」などの特殊記号も解釈される
- Bashでは、空文字対策として、変数はなるべくダブルクォーテーションで囲うようにする。
```
$ re=""; grep "$re" /etc/passwd
=: コマンドが見つかりません
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```
```
$ re=""; grep $re /etc/passwd
(「grep /etc/passwd」と入力したのと同じになってしまい、入力待ち状態になる)
```
##### 練習2.1.f　Bashの配列と連想配列
Bashの配列を初期化するには、配列名=( ) の括弧内に文字列を並べる。
```
$ a=( "$SHELL" "$LANG" "$USER")
$ echo "${a[0]}" "${a[1]}" "${a[2]}" 
/bin/bash ja_JP.UTF-8 takaaki
# 要素をすべて出力する
$ echo ${a[@]}
/bin/bash ja_JP.UTF-8 takaaki
$ echo ${a[*]}
/bin/bash ja_JP.UTF-8 takaaki
# インデックスを表示する
$ echo ${!a[@]}
0 1 2
# 要素の個数を表示する
$ echo ${#a[@]}
3
$ echo ${#a[*]}
3
```
連想配列は、declare -A で作り、名前[キー]=値 で設定する。
```
$ b["SHELL"]="$SHELL"; b["LANG"]="$LANG"; b["USER"]="$USER"
$ echo "${b[SHELL]}" "${b[LANG]}" "${b[USER]}"
/bin/bash ja_JP.UTF-8 takaaki
# 値をすべて出力する
$ echo "${b[@]}"
/bin/bash takaaki ja_JP.UTF-8
$ echo "${b[*]}"
/bin/bash takaaki ja_JP.UTF-8
# キーをすべて出力する
$ echo "${!b[@]}"
SHELL USER LANG
$ echo "${!b[*]}"
SHELL USER LANG
# 個数を出力する
$ echo "${#b[@]}"
3
$ echo "${#b[*]}"
3
```
##### 練習2.1.g　繰り返しと終了ステータス
```
$ set aa bb cc
$ echo ${2}
bb

$ for x in "$1" "$2" "$3"; do echo ${x}; done
aa
bb
cc
$ for x in $@; do echo "${x}"; done
aa
bb
cc
$ for x in "$@"; do echo "${x}"; done
aa
bb
cc
$ for x in $*; do echo "${x}"; done
aa
bb
cc
# $* をクォートで囲むと連結された１つの引数「aa bb cc」としてコマンドに渡される。
$ for x in "$*"; do echo "${x}"; done
aa bb cc

# while 右側のコマンドが正常に処理される間実行される。
# read は標準入力から1行ずつ文字列を読み込み、変数にセットする。
$ seq 3 | while read x; do printf "%s " ${x}; done
1 2 3 
```
- コマンドは、自分自身がどのように終了したかを終了ステータスによってシェルに伝える。
- シェルは、次のコマンドが実行されるまで、終了ステータスを記憶している。
- 終了ステータスは、$? に保存される。
```
$ grep 'bash' /etc/passwd; echo $?
root:x:0:0:root:/root:/bin/bash
takaaki:x:1000:1000:Takaaki Yamada,,,:/home/takaaki:/bin/bash
0

$ grep 'basha' /etc/passwd; echo $?
1

$ grep 'basha' /etc/passwdxxx; echo $?
grep: /etc/passwdxxx: そのようなファイルやディレクトリはありません
2

$ grepxxx 'basha' /etc/passwdxxx; echo $?
grepxxx: コマンドが見つかりません
127
```
- $? で確認できるのは最後のコマンドの終了ステータスのみ。
- これでは不都合が多いので、Bash は配列 PIPESTATUS にパイプライン全ての終了ステータスを記録する。
```
$ cat /etc/passwd | grep 'hoge'

$ echo ${PIPESTATUS[@]}
0 1
```
##### 練習2.1.h　条件分岐
```
#!/bin/bash
if grep '[02468]$' <<< "$1" ; then
  echo 偶数
elif grep '[13579]$' <<< "$1" ; then
  echo 奇数
else
  echo その他
fi
```
##### 問題12　変数の読み込み
引数と標準入力どちらからも受け取れるようにする。
```
#!/bin/bash
if [ "$1" = "" ] ; then
  read n
else
  n="$1"
fi
echo $((n * 2))
```
三項演算子っぽい書き方
```
#!/bin/bash
[ "$1" = "" ] && read n || n="$1"
echo $((n * 2))
```
変数展開 ${変数名:-文字列} を用いる方法
```
#!/bin/bash
n=${1:-$(cat)}
echo $((n * 2))
```
##### 問題13　存在しないファイルの初期化
存在しなければ作成する。
```
$ [ -e unfile ] || touch unfile
```
リダイレクト記号 <> を用いる。「読み書きモードでファイルを開く」の意味を持つ。ここでは、unfileが存在すれば読み込み、存在しなければcatからの書き出しに備えてファイルを作成する。
```
$ cat <> unfile
```
##### 問題14　さまざまなループ
```
$ n=1; while [ $n -le 10 ]; do echo $n; n=$((n + 1)); done
```
標準入力を読み込む while read を使う
```
$ seq 10 | while read n; do echo $n; done
```
コマンド置換を用いる
```
$ for n in $(seq 10); do echo $n; done 
```
ブレース展開の一種であるBashのシーケンス式を用いる
```
$ for n in {1..10}; do echo $n; done
```
ループを使わない方法
```
$ seq 10 0 | xargs -I@ echo "ロケット発射まで @" 
```
前半でシェルスクリプトを作り、後半でそれを Bash に渡して実行している
```
$ seq -f 'echo ロケット発射まで %g' 10 0 | bash
```
##### 問題15　文字種の変換
パイプから文字列を変数に取り込む方法
```
$ echo I am a perfect human | while read c; do echo "$c¥n"; done
```
bash の -c オプションで 別の bash を立ちげてコマンドを実行する方法
```
$ echo I am a perfect human | bash -c 'read str; echo "input string is:" $str'
```
サブシェルを使う方法
- bash -c と同様に別の bash を立ち上げて中のコマンドを実行する
```
$ echo I am a perfect human | (read str; echo "input string is:" $str)
```
変数展開の機能を使う（Bashバージョン4以降）
```
# 大文字に変換
$ echo I am a perfect human | (read str; echo ${str^^})
# 小文字に変換
$ echo I am a perfect human | (read str; echo ${str,,})
```
配列の各要素の先頭を大文字にする（read -a w で 配列w に代入している）
```
# シンプルな例 w[*] 配列の区切りはIFSのまま
$ echo pen-pineapple-apple-pen | (IFS=-; read -a w; echo "${w[*]^}")
Pen-Pineapple-Apple-Pen
# シンプルな例 w[@] 配列の区切りが空白になる
$ echo pen-pineapple-apple-pen | (IFS=-; read words; echo ${words[@]^})
Pen pineapple apple pen
# forループで改行しながら表示する
$ echo pen-pineapple-apple-pen | (IFS=-; read -a words; for w in ${words[*]^}; do echo $w; done)
Pen
Pineapple
Apple
Pen
```
#### 2.2　プロセスを意識してシェルを操作する
##### 練習2.2.a　プロセスを知る
異なるプロセスがいくつあるか表示させる
```
$ sleep 100 | sleep 100 | sleep 100 | sleep 100 | sleep 100 &
$ ps | awk '$4=="sleep"{print $1}' | sort -u | wc -l  
5
```
PIDに重複があったか確認する場合
```
$ sleep 100 | sleep 100 | sleep 100 | sleep 100 | sleep 100 &
$ ps | awk '$4=="sleep"{a[$1]=""; b++}END{print length(a), b}'
5 5
```
##### 練習2.2.b　プロセスの親子関係を知る
```
sleep 100 | sleep 100 | sleep 100 | sleep 100 | sleep 100 &
$ pstree | grep -m 1 sleep -B1 -A4
 | |   \-+= 01629 takaaki.yamada /bin/zsh -il
 | |     |--= 02998 takaaki.yamada sleep 100
 | |     |--- 02999 takaaki.yamada sleep 100
 | |     |--- 03000 takaaki.yamada sleep 100
 | |     |--- 03001 takaaki.yamada sleep 100
 | |     |--- 03002 takaaki.yamada sleep 100
 ```
##### 練習2.2.c　ビルトインコマンドと外部コマンドを意識する
ビルトインコマンド（組み込みコマンド）
```
$ compgen -b | column
unset           pushln          zstat           compctl         comparguments   bindkey         limit           unfunction      set
rehash          zle             which           r               pushd           typeset         echotc          fc              continue
popd            readonly        unhash          zmodload        functions       true            echo            vared           command
ulimit          exit            pwd             zregexparse     float           hash            wait            unalias         zcompile
local           false           zparseopts      history         zstyle          strftime        dirs            kill            whence
jobs            times           logout          return          print           compset         unsetopt        compcall        umask
disable         sched           disown          exec            declare         cd              read            where           trap
[               setopt          type            compadd         comptry         compvalues      :               fg              private
compfiles       getln           source          emulate         alias           getopts         integer         zformat         log
printf          builtin         eval            chdir           shift           compgroups      bye             suspend
autoload        let             comptags        ttyctl          -               export          echoti          unlimit
noglob          bg              compdescribe    test            .               enable          compquote       break
```
外部コマンド
```
$ ls /bin 
[         chmod     dash      df        expr      ksh       ln        mv        pwd       rmdir     stty      test      zsh
bash      cp        date      echo      hostname  launchctl ls        pax       realpath  sh        sync      unlink
cat       csh       dd        ed        kill      link      mkdir     ps        rm        sleep     tcsh      wait4path
```
ビルトインコマンドの方が高速
- 外部コマンドは、実行される度に新しいプロセスとして呼び出される。OSが必要なメモリ領域を確保したり、プロセスの一覧表（プロセステーブル）を書き換えたりしなければならない。
- ビルトインコマンドは、Bashのプログラム中（C言語）に実装されているため、あるプログラムが自分の関数を呼び出すときのコストしかかからない。
```
$ time for i in {1..1000}; do /bin/echo "$i" >/dev/null; done
real    0m1.534s
user    0m0.294s
sys     0m0.964s

$time for i in {1..1000}; do builtin echo "$i" >/dev/null; done
real    0m0.033s
user    0m0.012s
sys     0m0.020s
```
##### 練習2.2.d　サブシェルを使う
- シェルはサブシェルを立ち上げるときに、自分自身を完全にコピーして、全く同じ内容のプロセスが走ることになる。
- そのため、親のシェルで保持している変数は子であるサブシェルに渡るが、子であるサブシェルで変数を変更しても、親のシェルの変数は影響を受けない。
- 親は子の終了ステータスを受け取ることができる。
- サブシェルは、端末で動いているBashとは別のプロセスで動作する。
```
$ pwd
/Users/takaaki.yamada/Development/books/shellgei/work/qdata

$ (cd /etc; ls *.conf)
asl.conf                newsyslog.conf          ntp_opendirectory.conf  syslog.conf
autofs.conf             nfs.conf                pf.conf
kern_loader.conf        notify.conf             resolv.conf
man.conf                ntp.conf                rtadvd.conf

$ pwd
/Users/takaaki.yamada/Development/books/shellgei/work/qdata
```
##### 練習2.2.e　コマンド置換とプロセス置換を使う
コマンド置換 $( )
```
$ a='きたうらわ'  
$ echo ${a}を逆さにすると$(echo ${a} | rev)
きたうらわを逆さにするとわらうたき
```
プロセス置換 <( )
```
$ a='きたうらわ'  
$ cat <(echo $a) <(echo を逆さにすると) <(echo $a | rev)
きたうらわ
を逆さにすると
わらうたき
```
##### 問題16　変数のローカル化
- サブシェル
  - シェルはサブシェルを立ち上げるときに、自分自身を完全にコピーする。
  - 親は子の終了ステータスを受け取ることができる。
```
$ n="XYZ"; (for i in {A..C}; do n+="$i"; echo "$n"; done); echo $n
XYZA
XYZAB
XYZABC
XYZ
```
**fork-execの補足が理解できていない。いつか戻ってくること**
##### 問題17　コマンドが使えないときのコピー
##### 問題18　シェルのビルトインだけでの集計
```
$ declare -A x ; IFS=: ; while read {a..g} ; do [[ -n "$g" ]] && x[$g]+=. ; done < /etc/passwd ; for s in ${!x[@]} ; do echo $s ${#x[$s]} ; done ; unset x
/bin/bash 1
/usr/sbin/uucico 1
/bin/sh 1
/usr/bin/false 114
```
##### 問題19　シェルの機能を利用したファイルの上書き
#### 2.3　ブレース展開とファイルグロブを使いこなす
##### 練習2.3.a　ブレース展開
```
# 例１
echo {1..5}{.txt,.bash}
1.txt 1.bash 2.txt 2.bash 3.txt 3.bash 4.txt 4.bash 5.txt 5.bash
# 例２
echo {1..5}.{txt,bash}
1.txt 1.bash 2.txt 2.bash 3.txt 3.bash 4.txt 4.bash 5.txt 5.bash
```
2から10までの数字を2つの間隔で出力する
```
$ echo {2..10..2}{.txt,.bash}
2.txt 2.bash 4.txt 4.bash 6.txt 6.bash 8.txt 8.bash 10.txt 10.bash
```
##### 練習2.3.b　ワイルドカードとファイルグロブ
アスタリスクの意味

|種類|説明|
|---|---|
|正規表現|直前の表現が0字以上（ワイルドカードと同じ意味にするには「.*」と書く）|
|ワイルドカード|長さ0字以上の任意の文字列|

ワイルドカード

|記号|説明|
|---|---|
|?|任意の1文字|
|\*|任意の文字列|
|[set]|setの中のいずれかの文字|

「\*.png」のような文字列をグロブと呼ぶ。

```
$ ls ?.txt
1.txt   2.txt   3.txt   4.txt   6.txt   7.txt   8.txt   9.txt
$ ls [126][5].*
15.bash 15.txt  25.txt  65.bash 65.txt
$ ls [^29].*
1.bash  1.txt   3.bash  3.txt   4.bash  4.txt   5.bash  6.bash  6.txt   7.bash  7.txt   8.bash  8.txt
```
##### 問題20　lsの出力をシェルの機能で
```
$ for i in $(cd /usr; echo *); do echo ${i}; done
X11
X11R6
bin
lib
libexec
local
sbin
share
standalone
```
別解：${変数名##除去したい文字列}
```
$ for f in /usr/* ; do echo ${f##*/} ; done
X11
X11R6
bin
lib
libexec
local
sbin
share
standalone
```
別解：${変数名/置換対象文字列/置換後の文字列}
```
$ files=(/usr/*) ; echo -e ${files[@]/\/usr\//\\n}

X11 
X11R6 
bin 
lib 
libexec 
local 
sbin 
share 
standalone
```
##### 問題21　条件がややこしいファイルの一覧の作成
```
$ tree
.
├── dir_a
│   ├── file_1
│   └── file_2
├── dir_b
│   ├── dir_a
│   │   └── file_1
│   ├── dir_c
│   │   ├── file_1
│   │   └── file_2
│   ├── file_1
│   └── file_2
└── dir_c
    └── dir_b
        ├── dir_a
        │   └── file_1
        ├── file_1
        └── file_2

7 directories, 10 files
$ find . -type f | grep -v '\./dir_b/d' | sort  
./dir_a/file_1
./dir_a/file_2
./dir_b/file_1
./dir_b/file_2
./dir_c/dir_b/dir_a/file_1
./dir_c/dir_b/file_1
./dir_c/dir_b/file_2
```
別解。shopt は Bash の拡張機能をセットするコマンド。
```
$ shopt -s globstar
$ shopt -p globstar
shopt -s globstar

$ echo dir_a/* dir_b/* dir_c/** | grep -o "[^ ]*[0-9]"
dir_a/file_1
dir_a/file_2
dir_b/file_1
dir_b/file_2
dir_c/dir_b/dir_a/file_1
dir_c/dir_b/file_1
dir_c/dir_b/file_2
```
##### 問題22　ダミーのFQDNの生成
```
$ echo {mail,blog,eng,www,help,sub,ns,}.{robotics,ojisan,yamada,ueda,nakamura,tashiro,blacknon}.{co.jp,com,tech,org,jp,go.jp,tw,asia} | tr ' ' '\n' | sed -E 's/^\.//g' | sort -R | head -n10 | c
at -n
     1  blog.tashiro.org
     2  eng.yamada.go.jp
     3  www.ueda.asia
     4  help.tashiro.jp
     5  ns.blacknon.go.jp
     6  sub.blacknon.org
     7  sub.yamada.org
     8  help.ueda.go.jp
     9  help.tashiro.com
    10  blog.ueda.tech
```
/usr/share/dict/words を使った別解。
まず、shuf コマンドをインストールする。
```
$ brew install shuf
```
```
$ seq -f 'echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # %g' 5 
echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # 1
echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # 2
echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # 3
echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # 4
echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # 5

$ seq -f 'echo $(grep -E "^[a-z]+$" /usr/share/dict/words | shuf -n1).$(shuf -n1 -e {com,org,{co.,}jp,net}) # %g' 5 | bash
disanswerable.co.jp
leiodermatous.net
bibliogony.jp
gasking.org
pantotherian.jp
```
#### 2.4　シグナルを理解してあやつる
- シグナルは、プロセス間通信のための１つの仕組み。
- kill は、シグナルを送信するためのコマンド。どの端末からでも特定のプロセスに向けてシグナルを送ることができる。
##### 練習2.4.a　シグナルの操作
```
# PIDを指定する方法
$ sleep 1000 &
[1] 3326
$ kill 3326
[1]  + 3326 terminated  sleep 1000                                                                                                                                 

# ジョブ番号を指定する方法
$ sleep 1000 &
[1] 3427
$ kill %1
[1]  + 3427 terminated  sleep 1000 
```
##### 練習2.4.b　シグナルの種類
- Ctrl + C は、SIGINT
- SIGKILL は、どんなプロセスも消せる。プログラム側に trap コマンドを書いておいても消せる。
```
$ kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGEMT       8) SIGFPE       9) SIGKILL     10) SIGBUS
11) SIGSEGV     12) SIGSYS      13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGURG      17) SIGSTOP     18) SIGTSTP     19) SIGCONT     20) SIGCHLD
21) SIGTTIN     22) SIGTTOU     23) SIGIO       24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGINFO     30) SIGUSR1
31) SIGUSR2
```
##### 問題23　別のシェルにシグナルを飛ばす
##### 問題24　exit時にファイルを消す
##### 問題25　pipefail時の困りごと
##### 問題26　18時を過ぎたら帰りましょう
#### 2.5　シェルやコマンドを扱う際の微妙な挙動や機能に触れる
##### 問題27　引数を変えてコマンドを再実行
##### 問題28　変な名前のディレクトリの扱い
##### 問題29　シェルスクリプトのエラーチェック
##### 問題30　前方一致する変数名
## 第2部　発想力を鍛える
### 第3章　文章と文字の扱い
#### 3.1　テキストの扱いを遊んで理解する
##### 練習3.1.a　Perl，Rubyワンライナー
##### 練習3.1.b　Pythonワンライナー
##### 練習3.1.c　正規表現の種類
##### 練習3.1.d　Perlを使った置換
##### 問題31　大文字への変換
##### 問題32　回文の検出
##### 問題33　回文かどうかの判定
##### 問題34　漢字の後ろにふりがなを入れる
##### 問題35　5文字以上のカタカナ言葉を使ったらアウト
##### 問題36　括弧の対をチェック
#### 3.2　文章を調査・加工する
##### 練習3.2.a　行またぎの検索と置換
##### 練習3.2.b　ひらがなとカタカナの変換
##### 練習3.2.c　漢字と読みの変換と日本語と英語の翻訳
##### 練習3.2.d　フォーマットの変換
##### 問題37　コピペミスの検出
##### 問題38　込み入った文字実体参照の解決
##### 問題39　文章の折り返し
##### 問題40　雑多な変換
##### 問題41　注釈のチェック
##### 問題42　順序付きリストの整形
##### 問題43　文献リストのソート
##### 問題44　行またぎの検索
##### 問題45　複数行にわたる重複の検索
##### 問題46　ルビを付ける
### 第4章　データの管理，集計，変換
#### 4.1　表形式のデータを扱う
##### 練習4.1.a　表計算
##### 練習4.1.b　データの連結
##### 問題47　前月比データの付加
##### 問題48　CPU負荷の調査1
##### 問題49　CPU負荷の調査2
##### 問題50　売り上げの集計
##### 問題51　テストの得点一覧の出力
##### 問題52　集計形式の変換
##### 問題53　欠損値の補完
#### 4.2　ややこしいフォーマットのデータを扱う
##### 練習4.2.a　JSONとjq
##### 練習4.2.b　エスケープの入ったCSVの扱い
##### 問題54　JSONファイルからの抽出
##### 問題55　JSONログの比較
##### 問題56　非正規データ同士の結合
##### 問題57　テーブルのレイアウトの整形
##### 問題58　CSVファイルの数字の集計
##### 問題59　CSVの列数の調査
#### 4.3　日付や時間を扱う
##### 練習4.3.a　日付や時刻の計算をする
##### 問題60　プレミアムフライデー
##### 問題61　先週のファイル
##### 問題62　休日の突き合わせ
##### 問題63　第5週が存在する月の調査
##### 問題64　第三火曜日の列挙
##### 問題65　各月の休日数
##### 問題66　リスケジュール
##### 問題67　曜日別に分割
##### 問題68　Unix時刻の限界
##### 問題69　うるう秒
### 第5章　文字コードとバイナリ
#### 5.1　文字コードに親しむ
##### 練習5.1.a　n進数
##### 練習5.1.b　ASCIIコード
##### 練習5.1.c　UnicodeとUTF-8
##### 練習5.1.d　文字コードと改行記号の変換
##### 問題70　Excel方眼紙
##### 問題71　文字のバイト数の調査
##### 問題72　絵文字
##### 問題73　展開後文字化けしたファイル名の修正
##### 問題74　未確定の元号
##### 問題75　絵文字を除去したい
##### 問題76　少し前のMacでできたファイルのリスト
##### 問題77　異なる文字コードのファイルの一括検索
##### 問題78　常用漢字でない漢字の検出
##### 問題79　文字コードの特定
#### 5.2　バイナリをあやつる
##### 練習5.2.a　文字列のバイナリ解析とバイトオーダ
##### 練習5.2.b　バイナリファイル調査用のコマンド
##### 問題80　2進数から文字列を復元
##### 問題81　BOMの識別
##### 問題82　画像の分割
##### 問題83　Shift_JISをそのままechoするシェルスクリプト
##### 問題84　改行コードの識別と集計
##### 問題85　文字のバイナリの平均をとる
##### 問題86　分数の計算
##### 問題87　odの出力の復元
##### 問題88　電子透かしの解読
##### 問題89　CTF
### 第6章　パズル
#### 6.1　数学で遊ぶ
##### 練習6.1.a　正確な計算
##### 練習6.1.b　素数
##### 練習6.1.c　組み合わせの生成
##### 問題90　n進数の計算
##### 問題91　組み合わせの抽出
##### 問題92　指数の計算
##### 問題93　最初に素数になる年月日時分秒
##### 問題94　ラグランジュの四平方定理の部分的な検証
##### 問題95　交番二進符号
#### 6.2　雑多な問題を片付ける
##### 問題96　日程調整
##### 問題97　縦読みの文字列の検出
##### 問題98　レコードの振り分け
##### 問題99　ポーカーの役
##### 問題100　しりとり順に並べる
##### 問題101　連続するアルファベットの検出と略記
##### 問題102　クワイン
## 第3部　応用する
### 第7章　Linux環境の調査，設定と活用
#### 7.1　ファイル，ディレクトリを調査・操作する
##### 練習7.1.a　各種ファイルの置き場所
##### 練習7.1.b　ファイルシステム
##### 練習7.1.c　iノード
##### 問題103　ls -lの出力の2列目
##### 問題104　ファイル名の長さの限界は？
##### 問題105　ディスク使用量の集計
##### 問題106　特殊なパーミッション
##### 問題107　何回rootになった？
##### 問題108　トラブルを起こしたプロセスの解析
##### 問題109　中身が同じファイルの検索
##### 問題110　複数のディレクトリ内のファイル数をカウント
#### 7.2　疑似ファイルシステムを利用する
##### 練習7.2.a　疑似ファイルシステム
##### 練習7.2.b　プロセス置換とファイル
##### 問題111　ロードアベレージの調査
##### 問題112　日付の取得
##### 問題113　カレントディレクトリの調査
##### 問題114　共有ライブラリの検索
##### 問題115　USBメモリのアンマウント
##### 問題116　デバイスの番号調査
##### 問題117　USBの抜き差しの監視
##### 問題118　プロセスが開いているファイルの調査
##### 問題119　メモリマップの表示
##### 問題120　謎のデータの調査
#### 7.3　システムコールを追いかける
##### 練習7.3.a　straceを使う
##### 問題121　ファイルパスの抽出
##### 問題122　使ったプロセス数のカウント
##### 問題123　計算時間の解析
#### 7.4　雑多な調査と設定を片付ける
##### 問題124　ネットワークデバイス一覧
##### 問題125　IPアドレスの追加
##### 問題126　Bashのバージョンと変数
##### 問題127　端末エミュレータのウィンドウサイズ
##### 問題128　grepの挙動の違いの正体を突き止めろ
##### 問題129　実行ファイルから文字列を抜き取る
#### 7.5　ワンライナーでサービスをしかける
##### 問題130　即席のWebサーバ
##### 問題131　メールの通知を投げる
##### 問題132　ログの監視＆アラート
##### 問題133　システムを自動でシャットダウンする
### 第8章　ソフトウェア開発中に繰り出すワンライナー
#### 8.1　ソースコードやスクリプトを調査・整形する
##### 問題134　Pythonのインデントの確認
##### 問題135　Lispの括弧の整合性の確認
##### 問題136　関数の位置の入れ替え
##### 問題137　コードの整形
#### 8.2　データを生成する
##### 練習8.2.a　ダミーデータの生成
##### 問題138　テストケースの作成
##### 問題139　URLの列挙
##### 問題140　Webサイトの構造からアクセスログを生成
##### 問題141　テーブル情報からのダミーデータ生成
##### 問題142　ビットスクワッティング
#### 8.3　Gitのリポジトリを調査・操作する
##### 練習8.3.a　リポジトリの用意
##### 練習8.3.b　変更されたファイルの確認
##### 練習8.3.c　コミット済みの情報を利用する
##### 練習8.3.d　ブランチとマージ
##### 問題143　条件にあうファイルだけをコミット
##### 問題144　条件にあうファイルだけをもとに戻す
##### 問題145　コミットの頻度を調べる
##### 問題146　ずっとマージされていないブランチを調べる
### 第9章　インターネットと通信
#### 9.1　インターネットから情報を取得する
##### 練習9.1.a　HTML文章の処理
##### 問題147　単語の出現頻度
##### 問題148　駅名のリストの作成
##### 問題149　天気情報の取得
##### 問題150　複数ページのスクレイピング
##### 問題151　複数サイトの情報の連携
#### 9.2　通信関係の調査や操作を行う
##### 練習9.2.a　名前解決
##### 練習9.2.b　HTTPステータスコード
##### 問題152　ネットワーク監視
##### 問題153　telnetコマンドでHTTP通信
##### 問題154　/etc/hostsの使用調査
##### 問題155　不正なContent-Length
##### 問題156　複数IPのアドレスが登録されているドメイン
##### 問題157　pingのパケット解析
##### 問題158　パケットを使ったOSの推定
##### 問題159　複数のドメインのチェック
##### 問題160　SSL証明書の調査

