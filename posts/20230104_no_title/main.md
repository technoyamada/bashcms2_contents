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
