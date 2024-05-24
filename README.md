# rclone mount
apgpuに関するセットアップのためのドキュメントです．

# minicondaを使う場合
[`Anaconda`](https://www.anaconda.com/download/success)では5GBのquota制限に到達してしまうようだが，[`miniconda`](https://docs.anaconda.com/free/miniconda/index.html)であれば容量が小さいのでインストールは可能．しかし，これらのインストールはできても`torch`などのライブラリをインストールするとquota制限に到達してしまう．そこで，minicondaをマウント先のディスクに書き込んでみたが，`conda`コマンド実行時にエラーが発生する．
```bash
$ which conda
/home/<user*>/miniconda3/bin/conda
```
`which`で確認したがパスは通っている．どうやら，コマンドのパスがマウント先を指していると問題があるよう．<br>

そこで，ライブラリを保存する`miniconda3/lib`，`miniconda3/pkgs`をマウント先のディスクにアクセスするように設定することquota制限を回避することができた．


適当に既存の`miniconda3/lib`，`miniconda3/pkgs`直下のファイルを移して，シンボリックリンクによりマウント先を指すように設定すれば問題ないだろう．
```bash
ln -s /home/<user*>/<mount-dir>/lib /home/<user*>/miniconda3/lib
ln -s /home/<user*>/<mount-dir>/pkgs /home/<user*>/miniconda3/pkgs
```
<br><br>

# 既存のpython3環境を使う場合
`usr/bin/python3`のパスのpython3を既に使用できる．

### pip 
`pip3`コマンドを使ってライブラリをインストールしたいが`pip`や`pip3`は使えない？．ローカルで`pip`コマンドを使えるようにする．
```bash
wget https://bootstrap.pypa.io/get-pip.py
```
`get-pip.py`がダウンロードされる．<br>
後に`pip`は`~/.local/bin/pip`を指すので，これをマウント先に指定しないようにする．

### symbolick link
`.local/lib`がマウント先の`local/lib`にリンクされる．
```bash
ln -s /home/<user*>/<mount-dir>/local/lib /home/<user*>/.local/lib
```
### get pip
マウント先に書き込むので少し時間がかかるよう．
```bash
python3 get_pip.py
```
パスを通す．ログアウトしても切れないように`.bashrc`ファイル内に書き込む．
```bash
export PATH=$PATH:~/.local/bin
```
`pip`，`pip3`コマンドが使えるようになった．
### pip install
例えば，`torch`のインストールは以下のようにできる．
```bash
pip3 install torch torchvision torchaudio
```