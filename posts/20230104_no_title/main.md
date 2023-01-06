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
- [certbot instructions](https://certbot.eff.org/instructions?ws=apache&os=ubuntubionic)
```
# certbotをインストールするため、まずはパッケージ管理ツールsnapdをインストールする
$ sudo apt install snapd
$ sudo snap install core; sudo snap refresh core
$ sudo apt-get remove certbot # 既存のcertbotをアンインストールする（ある場合のみ）
$ sudo certbot --apache
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): ymd.takaaki@gmail.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y
Account registered.

Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: bashcms2.technoyamada.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): 1
Requesting a certificate for bashcms2.technoyamada.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/bashcms2.technoyamada.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/bashcms2.technoyamada.com/privkey.pem
This certificate expires on 2023-04-06.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for bashcms2.technoyamada.com to /etc/apache2/sites-available/bashcms2-le-ssl.conf
Congratulations! You have successfully enabled HTTPS on https://bashcms2.technoyamada.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
```
- certbotが自動的に設定変更した内容を確認する
```
$ diff -ru <保存しておいたオリジナルファイルのパス> /etc/apache2
./mods-enabled のみに存在: rewrite.load
./mods-enabled のみに存在: socache_shmcb.load
./mods-enabled のみに存在: ssl.conf
./mods-enabled のみに存在: ssl.load
./sites-available のみに存在: bashcms2-le-ssl.conf
diff -ru /home/takaaki/TEST/apache2/sites-available/bashcms2.conf ./sites-available/bashcms2.conf
--- /home/takaaki/TEST/apache2/sites-available/bashcms2.conf    2023-01-06 14:25:42.501724746 +0900
+++ ./sites-available/bashcms2.conf     2023-01-06 14:28:15.852647100 +0900
@@ -26,6 +26,9 @@
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
+RewriteEngine on
+RewriteCond %{SERVER_NAME} =bashcms2.technoyamada.com
+RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
 </VirtualHost>

 # vim: syntax=apache ts=4 sw=4 sts=4 sr noet
./sites-enabled のみに存在: bashcms2-le-ssl.conf
diff -ru /home/takaaki/TEST/apache2/sites-enabled/bashcms2.conf ./sites-enabled/bashcms2.conf
--- /home/takaaki/TEST/apache2/sites-enabled/bashcms2.conf      2023-01-06 14:25:42.501724746 +0900
+++ ./sites-enabled/bashcms2.conf       2023-01-06 14:28:15.852647100 +0900
@@ -26,6 +26,9 @@
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
+RewriteEngine on
+RewriteCond %{SERVER_NAME} =bashcms2.technoyamada.com
+RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
 </VirtualHost>
```
- 自動更新のテスト
```
$ sudo cerbot renew --dry-run
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Processing /etc/letsencrypt/renewal/bashcms2.technoyamada.com.conf
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Account registered.
Simulating renewal of an existing certificate for bashcms2.technoyamada.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations, all simulated renewals succeeded:
  /etc/letsencrypt/live/bashcms2.technoyamada.com/fullchain.pem (success)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
- 自動更新タイマーの確認
```
$ systemctl list-timers
NEXT                         LEFT          LAST                         PASSED       UNIT
Fri 2023-01-06 19:45:00 JST  2h 37min left n/a                          n/a          snap.certbot.renew.timer
Fri 2023-01-06 20:17:21 JST  3h 9min left  Thu 2023-01-05 20:17:21 JST  20h ago      systemd-tmpfiles-clean.t
Fri 2023-01-06 20:35:00 JST  3h 27min left Fri 2023-01-06 11:20:21 JST  5h 47min ago apt-daily.timer
Sat 2023-01-07 06:38:20 JST  13h left      Fri 2023-01-06 06:23:02 JST  10h ago      apt-daily-upgrade.timer
Sat 2023-01-07 08:12:19 JST  15h left      Fri 2023-01-06 14:11:15 JST  2h 56min ago motd-news.timer
Mon 2023-01-09 00:00:00 JST  2 days left   Thu 2023-01-05 18:36:34 JST  22h ago      fstrim.timer
n/a                          n/a           n/a                          n/a          ua-timer.timer

7 timers listed.
Pass --all to see loaded but inactive timers, too.
```

### Git/GitHub
