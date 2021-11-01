# Git Tips

## ディレクトリによってgitアカウントを自動で切り替える
1. `.gitconfig_work` を作成

    ```
    # .gitconfig_work
    [user]
        name = workで使用したいuser.name
        email = workで使用したいglobalのuser.email
    ```
2. `.gitconfig_private` を作成
    ```
    [user]
        name = privateで使用したいuser.name
        email = privateで使用したいglobalのuser.email
    ```
3. `.gitconfig` でディレクトリごとにパスを指定する
    ```
    [user]
        name = globalのuser.name
        email = globalのuser.email

    # workディレクトリの時に.gitconfig_workが読み込まれる
    [includeIf "gitdir:~/work/"]
      path = ~/.gitconfig_work

    # privateディレクトリの時に.gitconfig_privateが読み込まれる
    [includeIf "gitdir:~/private/"]
      path = ~/.gitconfig_private
    ```
