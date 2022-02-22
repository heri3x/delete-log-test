# delete-log-test

## GitHub上のmainブランチのコミットログを全部削除する手順

下記の内容の作業手順メモ。

> how to delete all commit history in github? [duplicate]
> https://stackoverflow.com/questions/13716658/how-to-delete-all-commit-history-in-github/26000395#26000395

### 1. ブランチ切り替え

delete-log-test フォルダで下記を実行。latest_branch は、存在しないブランチ名であること。

```powershell
> git checkout --orphan latest_branch
```

latest_branchという名前のブランチが作られて切り替わり、コミット履歴ゼロの状態になる。

```powershell
> git log
fatal: your current branch 'latest_branch' does not have any commits yet
```

### 2. 初回コミット作成

現在の作業コピーの状態で初回コミットを作成する。

```powershell
> git add -A
> git commit -m "initial commit"
```

### 3. mainブランチを削除

作業コピーの現在のmainブランチを削除する。

```powershell
> git branch
* latest_branch
main

> git branch -D main

> git branch
* latest_branch
```

### 4. ブランチ名変更: latest_branch → main

作業コピーの現在のブランチ latest_branch のブランチ名を main に変える。

```powershell
> git branch -m main
```

状態確認。

```powershell
> git status
On branch main
nothing to commit, working tree clean

```

### 5. プッシュ

GitHubへプッシュ。これで mainブランチのコミットログが全部消えた。

```powershell
> git push -f origin main
```

以上。
