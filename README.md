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

---

## 複数アカウントでSSH接続を使い分ける

下記の内容の作業手順メモ。

> [備忘] 複数Githubアカウントでssh接続設定(config)を使い分ける手順
> https://qiita.com/yampy/items/24638156abd383e08758

## 1. SSHキー作成、GitHubに登録

事前にSSHキーを作ってGitHubに登録しておく。説明は省略。置き場所は下記。

- ~/.ssh/ユーザー名1_id_rsa
- ~/.ssh/ユーザー名1_id_rsa.public

## 2. ".ssh/config"を修正

```powershell
> code ~/.ssh/config
```

このユーザー用の設定をURL "github.com" で追加。  
ユーザー名1のHostNameを "github.com" にし、その他のユーザーのHostNameを "ユーザー名.github.com" のように変更しておく。

```text
Host github.com
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/ユーザー名1_id_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes

Host ユーザー名2.github.com
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/ユーザー名2_id_rsa
  TCPKeepAlive yes
  IdentitiesOnly yes
```

動作確認。

```powershell
> ssh -T git@github.com
Enter passphrase for key 'C:\Users\Windowsユーザー名/.ssh/ユーザー名_id_rsa':
Hi ユーザー名! You've successfully authenticated, but GitHub does not provide shell access.
```

## 3. 作業コピーのリモートURL設定を修正

作業コピーのリモートURLを修正。

```powershell
> git remote set-url origin git@github.com.ユーザー名:ユーザー名/リポジトリ名.git
```

状態確認。

```powershell
> git remote -v
origin  git@github.com.ユーザー名:ユーザー名/リポジトリ名.git (fetch)
origin  git@github.com.ユーザー名:ユーザー名/リポジトリ名.git (push)
```

動作確認。pull/pushできる。

```powershell
> git pull
Enter passphrase for key '/c/Users/Windowsユーザー名/.ssh/ユーザー名_id_rsa':
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> main

```

## 4. GitHub Desktopでも使う場合: pageantにSSHキーを登録

認証キーは以下で登録できる。ただしリモートURLのドメインをカスタムしてるのは通らない。

1. PuTTYをインストール。
    - https://www.putty.org/
1. PuTTYgenを起動。
1. [Load]ボタンをクリック。拡張子を "All Files (*.*)" にして id_rsa ファイルをロード。
1. [Save private key]ボタンをクリック。"ユーザー名.ppk" という名前で保存
1. Pageantを開く（※起動していればタスクアイコンから）
1. [Add Key]ボタンを押して、"ユーザー名.ppk" を追加

作業コピーのリモートURLを修正。HTTPSじゃないとだめっぽい。

```powershell
> git remote set-url origin https://ユーザー名@github.com/ユーザー名/リポジトリ名.git
```

これで GitHub Desktop で pull/pushできる。ただしgit CLIではリモートURLを再度修正しないとpull/pushできない。めんどうくさいな？

以上。
