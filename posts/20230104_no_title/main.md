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

### 公開鍵認証を使わないSSH接続
```
$ ssh takaaki@<server ip address>
```
