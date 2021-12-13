# Github Actions

### permission

- それぞれのWorkflow/Jobでの `secrets.GITHUB_TOKEN` の権限を設定できるようになっている。 
- `secrets.GITHUB_TOKEN` は GitHub Actions の実行ごとに発行されるGitHubのTokenで、GitHub Actionsはこのトークンを使ってリポジトリをgit cloneしたり、Issueにコメントを書いたりしている
- 自動で定義してくれるツールもあるみたい
  - [update-github-actions-permissions](https://github.com/pkgdeps/update-github-actions-permissions)

## 資料
- [フィルターパターンチートシート](https://docs.github.com/ja/actions/learn-github-actions/workflow-syntax-for-github-actions#filter-pattern-cheat-sheet)