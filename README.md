# memo
#### メモ帳リポジトリ
- [apgpu setup](./apgpu_setup.md)
apgpuは，5GBのクオータ制限があり，pytorchなどのライブラリを入れることで溢れる．そのためマウントを行うが，いくつか問題がある．<br>
minicondaを使用して問題解決する方法と，既存のpython3環境を使用して解決する2種類についてまとめる．
- [local rclone mount](./local_rclone.md)
ユーザー権限でrcloneコマンドを使用するためのセットアップについてまとめる．
- [ssh public key](./ssh.md)
リモートマシンにssh接続する際，パスワード認証がデフォルトだが，簡単なパスワードだとセキュリティ上問題がある．<br>
そのため，秘密鍵と公開鍵でssh接続する方法についてまとめる．