# ssh先のリモートマシンにユーザー権限でvscodeをインストールする方法
[vscode download](https://code.visualstudio.com/Download#)
このリンクに飛んで，`linux`，`.tar.gz`，`x64`をローカルマシンでインストールする．もしリモートで(wgetなどで)できるならば，手間がかからない．<br>

`scp`や`sftp`コマンドでリモートにダウンロードしたファイルを移す．<br>
以下のコマンドで`.tar.gz`ファイルを解凍する．
```bash
tar -zxvf ****.tar.gz
```
`VSCode-linux-x64`というディレクトリが作成されるはず．<br>
その中の`code`コマンドが実行できればいいのだが，
```bash
$ ./VSCode-linux-x64/code
[**:ERROR:ozone_platform_x11.cc(244)] Missing X server or $DISPLAY
[**:ERROR:env.cc(257)] The platform failed to initialize.  Exiting.
```
以上のようなエラーが出る．<br>
`Xサーバもしくは$DISPLAYが見つかりません．プラットフォームの初期化に失敗しました．終了します．`<br>
ということを言っていて，CLI環境でGUI操作を行うプログラムを実行しようとした時に吐かれるものらしい．<br>
`xvfb`などを使用し，仮想ディスプレイを作ることで開けるかも．