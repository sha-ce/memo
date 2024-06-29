# SSH公開鍵認証による接続
rsa暗号によるssh鍵を作成する
```bash
ssh-keygen -t rsa -b 4096
```
保存する場所を聞かれるがそのままエンター
```
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\<user name>/.ssh/id_rsa):
```
パスワードを聞かれるがそのままエンター
```
Enter passphrase (empty for no passphrase):
```
エンター
```
Enter same passphrase again:
```

`C:\Users\<user name>/.ssh/id_rsa`，`C:\Users\<user name>/.ssh/id_rsa.pub`<br>
という2つのファイルが作成される．
`id_rsa`が秘密鍵で，`id_rsa.pub`が公開鍵．<br><br>
次に，公開鍵をリモート側に教える．<br>
これまで通り，パスワード認証でリモートマシンに接続する．<br>
`/home/<user>/.ssh/authorized_keys`に公開鍵をペーストする．
```bash
mkdir .ssh
cd .ssh
echo 'ここに公開鍵' > authorized_keys
```
これで，完了！！！！

仮に，ローカル側で秘密鍵を上書きしてしまい，わからなくなってもパスワード認証に切り替わるだけなので，再度同じ設定をすれば問題なし．<br>

もれなく，パスワードを強力に変更しておきましょう．
```bash
passwd
```
もし何らかのトラブルでローカル側の秘密鍵とリモート側の公開鍵がマッチしなければ，パスワード認証に切り替わるので，この変更したパスワードを忘れないようにしましょう．