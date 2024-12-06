# memo
#### メモ帳リポジトリ
- [apgpu setup](./apgpu_setup.md)<br>
apgpuは，5GBのクオータ制限があり，pytorchなどのライブラリを入れることで溢れる．そのためマウントを行うが，いくつか問題がある．<br>
minicondaを使用して問題解決する方法と，既存のpython3環境を使用して解決する2種類についてまとめる．
- [local rclone mount](./local_rclone.md)<br>
ユーザー権限でrcloneコマンドを使用するためのセットアップについてまとめる．
- [ssh public key](./ssh.md)<br>
リモートマシンにssh接続する際，パスワード認証がデフォルトだが，簡単なパスワードだとセキュリティ上問題がある．<br>
そのため，秘密鍵と公開鍵でssh接続する方法についてまとめる．
- [vscode on CLI](./vscode.md)<br>
ssh先のLinuxマシン(CLI)上でvscodeを立ち上げる方法．<br>
vscodeの拡張機能`remote-ssh`は使い勝手はいいがセキュリティに関して疑問がある．具体的にいいとも悪いとも言えないが，，そのため，vscodeを介さずにssh接続を行い，向こうのマシン上でvscodeを使用するという方法．これをユーザー権限で実行できたらなと．
- [git command](./git_command.md)<br>
git コマンドまとめ
- [github ssh setting](./github_ssh_setting.md)<br>
github のプライベートリポジトリなどで作業する際、毎回パスワードやアクセストークンを入力するのは億劫である。そのため、sshで接続するようにできるまでの設定を記す。