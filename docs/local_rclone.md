# local-rclone
user権限でrcloneコマンドを実行するために，.localを使用する方法です．<br>
[rclone install](https://rclone.org/install/)ここに詳しく書いているが，手順を追って説明する．<br>

- wget で rclone zip ファイルをダウンロードする．
- `.local/bin`に`rclone`，`rclone.1`コマンド実行ファイルを移動する．
- `.local/bin`にパスを通す．
- `rclone config`でリモートマシンの設定．

#### wget で rclone zip ファイルをダウンロードする．
```bash
wget https://downloads.rclone.org/rclone-current-linux-amd64.zip
unzip rclone-current-linux-amd64.zip
```
これで，`rclone-*-linux-amd64`というディレクトリが作成される．
その中にいくつかファイルが存在するが，`rclone`，`rclone.1`というファイルだけ使用する．

#### `.local/bin`に`rclone`，`rclone.1`コマンド実行ファイルを移動する．
`/home/<user>/.local/bin/`の中に`rclone`，`rclone.1`をムーブする．
`.local/bin`がなければ作成する．

#### `.local/bin`にパスを通す．
```bash
export PATH=$PATH:~/.local/bin
```

#### `rclone config`でリモートマシンの設定．
```bash
$ rclone config
```
`n` を入力
```bash
e/n/d/r/c/s/q> n
```
リモートマシンの名前を設定する．なんでもいい．
```bash
name> <remote-machine-name>
```
`SSH/SFTP`を選択する．ここでは`44`．
```bash
Storage> 44
```
リモートマシンのipアドレスを入力する．
```bash
host> <IP adress>
```
リモートマシンのアクセス権のあるユーザーを設定する．
```bash
user> <user-name>
```
後は基本的にエンターで問題ない．
```bash
port> 
```
エンター
```bash
y/g/n> 
```
＜中略＞<br>
続けて，，エンターを繰り返す．
```bash
ssh> 
```
エンター
```bash
y/n> 
```
エンター
```bash
y/e/d> 
```
`q`を押して設定終了．続けて設定する場合は，`n`で新たなマシンの設定をする．
```bash
e/n/d/r/c/s/q> q
```

### rclone mount
```bash
rclone mount <"remote-machine-name">:<"from path"> <"to path">
```
バックグラウンド実行の場合は末尾に`&`を追加する．<br>
マウントの必要がなくなれば，`umount`で処理を終了する．
```bash
umount <"to path">
```