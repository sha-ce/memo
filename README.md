# rclone mount
まず，rcloneによるマウントを行う．以下`<***>`は変数を表す．個人で適当なものに変更してもらう．
マウント先の`<machine>`内にある`'/home/<user>/share'`フォルダを`'/home/<aaaa0000>/share'`からアクセスできるようにする．
```bash
rclone mount <machine>:/home/<user>/share /home/<aaaa0000>/share &
```
# pip 
`pip3`コマンドを使ってライブラリをインストールしたいが`pip`や`pip3`は使えないと思われる．ローカルで`pip`コマンドを使えるようにする．
```bash
wget https://bootstrap.pypa.io/get-pip.py
```
`get-pip.py`がダウンロードされる．<br>
このファイルを実行したいが，その前に，シンボリックリンクを作成して，マウント先に書き込むように設定する．
`get-pip.py`を実行すると
```
.cache/
    pip/

.local/
    bin/
    lib/
```
以上のようなディレクトリ構造で複数のファイルが作成される．<br>
ここで，`.local/bin/pip`に`pip`のパスを通すのが一般的だが，どうやらコマンドのパスをマウント先に設定すると権限がないためか，詳しくはわからないが，コマンドの実行時にエラーがはかれる．そのため，`.local/bin`はシンボリックリンクの設定から省く．
### symbolick link
```bash
cd
mkdir ./share/cache
ln -s ./share/cache .cache
```
```bash
cd
mkdir ./share/local
mkdir ./share/local/lib
mkdir ./.local
ln -s ./share/local/lib .local/lib
```
以上で，`.cache`にアクセスすると，マウント先の`share/cache`にリンクされ，`.local/lib`は，マウント先の`share/local/lib`にリンクされる．
### get pip
マウント先に書き込むので少し時間がかかるよう．
```bash
cd
python3 get_pip.py
```
パスを通す．
```bash
export PATH=$PATH:~/.local/bin
```
`pip`，`pip3`コマンドが使えるようになった．
# pip install
例えば，`torch`のインストールは以下のようにできる．
```bash
pip3 install torch torchvision torchaudio
```