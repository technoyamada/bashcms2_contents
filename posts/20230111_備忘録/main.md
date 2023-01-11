---
Keywords: 
Copyright: (C) Takaaki Yamada
---

# 備忘録 on 2023/01/11

## bashスクリプトのオプション
```
#!/bin/bash -eux
```
| オプション | 説明 |
----|----
| x | 実行したコマンドを標準エラー出力に表示する |
| e | コマンドにエラーが発生したら止まる |
| u | 定義されていない変数があったら止まる |

## Update SSH_AUTH_SOCK on TMUX ATTACH
- attachしたときに走るスクリプトを見つけられず断念。
- また、git pushするときにssh接続に失敗することに対して、以下のエイリアスで対応しようとしたが失敗。sshコマンドを直接叩いているわけではないようなので。
```
alias ssh=$(cat << 'EOS'
  if [ -n "$TMUX" ]; then
    SSH_AUTH_SOCK=`find /tmp/ssh* -name "agent*" -uid $(id -u) 2>/dev/null | tail -n 1`     export SSH_AUTH_SOCK
  fi
  /usr/bin/ssh
EOS
)

```

## CGI
