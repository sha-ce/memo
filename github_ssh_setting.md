# github ssh 設定
github のプライベートリポジトリなどで作業する際、毎回パスワードやアクセストークンを入力するのは億劫である。そのため、sshで接続するようにできるまでの設定を記す。

### ローカルマシンでsshkeyを作成
```bash
# Ed25519 アルゴリズム
ssh-keygen -t ed25519 -C "your_email@example.com"
--- or ---
# RSA 暗号
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
`.ssh/`内に`id_*`, `id_*.pub`ファイルが作成される。

### githubアカウントにssh公開鍵を追加
- [gihub > profile > settings > SSH and GPG keys](https://github.com/settings/keys)にアクセスする。
- `New SSH key`ボタンを押す。
- `Title`に適当な識別句を書く。
- `Key`内に、`id_*.pub`の公開鍵をペーストする。
- `Add SSH key`ボタンを押す。

以上で、sshを使用してgitを使用できる。


### リモートリポジトリのURLを確認する
```
git remote -v
```

### リモートリポジトリのURLを変更
```
git remote set-url origin <new url>
```
