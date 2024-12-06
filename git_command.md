# git コマンド まとめ

### 初期設定
```
git config --global user.name '<your name>'
git config --global user.email '<your email>'
```

### ローカルのプロジェクトをリモートにプッシュ
```
cd <local project dir>
git init
git add .
git commit -m '[add] ローカルプロジェクトからリモートへの追加'
git remote add origin <git repository link>
git push origin main
```

### リモートからクローンする
```
git clone <remote project link>
```

### リモートから変更を取得
`--rebase`: 異なるブランチ上でpullしたときにポインタを最新のものに合わせる
```
git pull
--- or ---
git pull --rebase
```


### git add の取り消し
`HEAD`: 今自分が作業している場所を示すポインタ
```
git reset HEAD 
```

### git commit の取り消し
`--hard`: コミット取り消した上でワークディレクトリの内容も書き換えたい場合<br>
`--soft`: ワークディレクトリの内容はそのままでコミットだけを取り消したい場合<br>
`HEAD^`: 直前のコミット<br>
`HEAD~{n}`: n個前のコミット<br>
```
git reset --soft HEAD^
```

### commit の打ち消し
```
git revert <commit hash>
```

### commit message の修正
```
git commit --amend '<new commit message>'
```

### push の取り消し
```
git reset --hard <取り消したいコミットのハッシュ>
git push -f
```


### ローカルでブランチ作成→切り替え
```
git branch <branch name>
git checkout <branch name>
--- or ---
git checkout -b <branch name>
```

