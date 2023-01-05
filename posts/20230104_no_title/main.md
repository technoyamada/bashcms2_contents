---
Keywords: 環境構築, さくらのVPS, Ubuntu
Copyright: (C) Takaaki Yamada
---

# さくらのVPSをセットアップする

## Ubuntuの再インストール

### OSの選択
- 「OS再インストール」をクリックする。
- カスタムOSで「Ubuntu 18.04 amd64」を選択し、インストールOSとして「Ubuntu 18.04 amd64」を選択した上で、「内容確認」をクリックする。
- 内容を確認の上、「OS再インストール」をクリックする。

### ユーザーの追加
- 「VNCコンソールを起動」をクリックする。
- Full name for the new user: として、「Takaaki Yamada」を入力して「Continue」。
- User name for your account: として、「takaaki」を入力して「Continue」。
- Password を入力して「Continue」。
- Partition disks として、「Guided - use entire disk」を選択する。
- 「Virtual disk 1 (vda) - ...」を選択する。
- 「Yes」を選択してインストールを続行する。

## SSH接続

### まずは、公開鍵認証を用いないSSH接続
```
$ ssh takaaki@<server ip address>
```

### 公開鍵認証を用いたSSH接続
ローカルPC
```
$ scp .ssh/id_rsa.pub takaaki@<server ip address>:~/
$ ssh takaaki@<server ip address>  # パスワードを求められる
```
サーバー
```
$ mkdir .ssh
$ chmod 700 .ssh/
$ cat id_rsa.pub >> .ssh/authorized_keys
$ chmod 600 .ssh/authorized_keys
$ exit
```
ローカルPC
```
$ ssh -i <秘密鍵> takaaki@<server ip address>  # パスワードを求められない
```

### パスワードログインの禁止
サーバー
```
$ cd /etc/ssh/
$ sudo cp -p sshd_config sshd_config.org
$ sudo vi sshd_config
$ diff sshd_config sshd_config.org
< PasswordAuthentication no
---
> #PasswordAuthentication yes
$ sudo systemctl restart sshd
$ exit
```
ローカルPC
```
$ ssh takaaki@<server ip address>
takaaki@<server ip address>: Permission denied (publickey).
$ ssh -i .ssh/Sakura/id_rsa takaaki@<server ip address>                                 255 ↵
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 4.15.0-200-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
New release '20.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Thu Jan  5 19:06:48 2023 from 60.71.250.240
```

## 日本語化
サーバー
```
$ locale
LANG=C.UTF-8
LANGUAGE=
LC_CTYPE=C.UTF-8
LC_NUMERIC="C.UTF-8"
LC_TIME="C.UTF-8"
LC_COLLATE="C.UTF-8"
LC_MONETARY="C.UTF-8"
LC_MESSAGES="C.UTF-8"
LC_PAPER="C.UTF-8"
LC_NAME="C.UTF-8"
LC_ADDRESS="C.UTF-8"
LC_TELEPHONE="C.UTF-8"
LC_MEASUREMENT="C.UTF-8"
LC_IDENTIFICATION="C.UTF-8"
LC_ALL=

$ sudo apt install language-pack-ja
$ sudo update-locale LANG=ja_JP.UTF-8
$ echo 'export LANG=ja_JP.UTF-8' >> ~/.bashrc
$ echo 'export LANGUAGE="ja_JP:ja"' >> ~/.bashrc
$ exit # 再度ログインする

$ locale
LANG=ja_JP.UTF-8
LANGUAGE=ja_JP:ja
LC_CTYPE=C.UTF-8
LC_NUMERIC="ja_JP.UTF-8"
LC_TIME="ja_JP.UTF-8"
LC_COLLATE="ja_JP.UTF-8"
LC_MONETARY="ja_JP.UTF-8"
LC_MESSAGES="ja_JP.UTF-8"
LC_PAPER="ja_JP.UTF-8"
LC_NAME="ja_JP.UTF-8"
LC_ADDRESS="ja_JP.UTF-8"
LC_TELEPHONE="ja_JP.UTF-8"
LC_MEASUREMENT="ja_JP.UTF-8"
LC_IDENTIFICATION="ja_JP.UTF-8"
LC_ALL=
```

## 各種コマンドのバージョン

### bash
```
$ bash --version
GNU bash, バージョン 4.4.20(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2016 Free Software Foundation, Inc.
ライセンス GPLv3+: GNU GPL バージョン 3 またはそれ以降 <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

### awk/gawk
```
# awkの本体を調査する
$ which awk
/usr/bin/awk
$ ls -l /usr/bin/awk
lrwxrwxrwx 1 root root 21  1月  5 18:28 /usr/bin/awk -> /etc/alternatives/awk
$ ls -l /etc/alternatives/awk
lrwxrwxrwx 1 root root 13  1月  5 18:28 /etc/alternatives/awk -> /usr/bin/mawk*
$ ls -l /usr/bin/mawk
-rwxr-xr-x 1 root root 125416  4月  3  2018 /usr/bin/mawk

# gawk(GNU Awk)をインストールする
$ sudo apt install gawk

# awkがgawkを指していることを確認する
（省略）
```
### sed
```
$ sed --version
Copyright (C) 2017 Free Software Foundation, Inc.
ライセンス GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

作者 Jay Fenlason、 Tom Lord、 Ken Pizzini、
および Paolo Bonzini。
GNU sed ホームページ: <http://www.gnu.org/software/sed/>.
GNU ソフトウェアを使用する際の一般的なヘルプ: <http://www.gnu.org/gethelp/>.
E-mail bug reports to: <bug-sed@gnu.org>.
```
 
## Webサーバーのセットアップ

### ドメインの取得
- Google Domains で「technoyamada.com」を購入した。

### ホスト名の変更
サーバー
```
$ cat /etc/hostname
tk2-114-57828
$ echo "bashcms2.technoyamada.com" | sudo tee /etc/hostname
$ cat /etc/hostaname
bashcms2.technoyamada.com
$ sudo reboot
```

### Apacheのインストール
```
$ sudo apt install apache2
$ curl http://localhost |& grep title
    <title>Apache2 Ubuntu Default Page: It works</title>
```

### Apacheの情報を隠す
```
$ sudo vim /etc/apache2/apache2.conf
# 以下を追記
ServerSignature Off
ServerTokens ProductOnly
$ sudo systemctl restart apache2
```

### HTTPS化

