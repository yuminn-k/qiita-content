---
title: MacでGitの複数アカウント（個人用・業務用）を管理する方法
tags:
  - Git
  - SSH
  - 設定
private: false
updated_at: '2025-01-02T18:51:19+09:00'
id: 346aa55dd3ceecdae186
organization_url_name: null
slide: false
ignorePublish: false
---

# MacでGitの複数アカウント（個人用・業務用）を管理する方法

Gitを使うとき、個人用アカウントと業務用アカウントを使い分ける必要があることはよくあります。特に、Mac上でこれを実現するには、適切な設定が必要です。本記事では、個人用と業務用フォルダを分け、それぞれのGit設定とSSH認証を適用する手順を詳しく解説します。

<br>

## 1. `.gitconfig` の設定

`~/.gitconfig` ファイルにフォルダごとの設定を適用します。以下のように記述してください。

```bash
[user]  # グローバル設定
    email = user@email.com
    name = username

[includeIf "gitdir:~/Documents/personal/"]  # 個人用フォルダ設定
    path = ~/.gitconfig-personal

[includeIf "gitdir:~/Documents/company/"]  # 業務用フォルダ設定
    path = ~/.gitconfig-company
```

上記の設定は次を意味します：

- ~/Documents/personal/ 以下のフォルダでは ~/.gitconfig-personal の設定を使用。
- ~/Documents/company/ 以下のフォルダでは ~/.gitconfig-company の設定を使用。

<br>

## 2. フォルダ別のアカウント情報設定

それぞれのフォルダで使用するGit設定ファイルを作成します。

<b>個人用</b>

`$ vi ~/.gitconfig-personal`

```bash
[user]
    name = MyGitHubName
    email = MyGitHubEmail
```

<b>業務用</b>

`$ vi ~/.gitconfig-company`

```bash
[user]
    name = MyCompanyGitHubName
    email = MyCompanyGitHubEmail
```

これで、個人用フォルダと業務用フォルダで異なる名前とメールアドレスが適用されます。

<b>確認コマンド</b>

`$ git config user.name`

	⚠️ git init 済みのフォルダでのみ設定が適用されます。それ以外の場合は、グローバル設定が優先されます。

## 3. SSH認証の設定

<b>SSHキーの生成</b>

個人用と業務用でそれぞれSSHキーを生成します。

<br>

<b>個人用キー生成</b>：

`$ ssh-keygen -t ed25519 -C "MyPersonalEmail@naver.com" -f "~/.ssh/id_ed25519-personal"`

<b>業務用キー生成</b>：

`$ ssh-keygen -t ed25519 -C "MyCompanyEmail@naver.com" -f "~/.ssh/id_ed25519-company"`

<br>

コマンド実行後、以下のようなファイルが生成されます：

-	~/.ssh/id_ed25519-personal と ~/.ssh/id_ed25519-personal.pub
-	~/.ssh/id_ed25519-company と ~/.ssh/id_ed25519-company.pub

<br>

<b>SSH設定ファイルの編集</b>

`~/.ssh/config` ファイルに以下を追加します。

```bash
Host github-personal.com
    HostName github.com
    User git
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519-personal

Host github-company.com
    HostName github.com
    User git
    AddKeysToAgent yes
    UseKeychain yes
    IdentityFile ~/.ssh/id_ed25519-company
```

<br>

## 4. 動作確認

<b>クローンテスト</b>

それぞれのアカウントでGitリポジトリをクローンします。

<br>

<b>個人用リポジトリのクローン</b>：

`$ git clone git@github-personal.com:[username]/[repository-name].git`

<b>業務用リポジトリのクローン</b>：

`$ git clone git@github-company.com:[username]/[repository-name].git`

	⚠️ Permission denied (publickey) エラーが出た場合、以下の方法で解決してください。

<br>

<b>SSHキーの登録</b>

GitHubにSSHキーを登録します。

1.	個人用キーを表示： <br>
    `$ cat ~/.ssh/id_ed25519-personal.pub`
2.	表示されたキーをコピーして、`GitHub` → `Settings` → `SSH and GPG keys` → `New SSH Key` に追加します。
3.	同様に業務用キーも登録します。

<br>

## 5. まとめ

これで、1台のMacで個人用と業務用のアカウントをスムーズに管理できるようになりました。~/.gitconfig の設定により、各フォルダで適切なユーザー情報が自動的に適用されます。また、SSH認証を完了することで、アカウントの衝突を避けつつ快適に作業ができます。

参考になりましたか？フィードバックや質問があればコメントで教えてください！ 😊
