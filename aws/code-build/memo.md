# CodeBuild
## buildspec.yml の構成（[詳細](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/build-spec-ref.html#build-spec-ref-syntax)）
![スクリーンショット 2021-11-24 17 33 28（2）](https://user-images.githubusercontent.com/49634472/143203107-92b69fa3-b8ab-42f7-8656-6dfbf126df0b.png)

## ローカルでビルドする
- [AWS CodeBuild エージェントを使用してビルドをローカルで実行](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/use-codebuild-agent.html)

## Session Managerでビルド環境へアクセスする
- buildspec.yml に `codebuild-breakpoint` を埋め込み、ビルド設定でセッション接続を有効化
- Session Manager でビルド環境にアクセス（マネコン or CLI）しデバック
- デバックが終わったら `codebuild-resume` を実行でビルド再開
- [参考](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/session-manager.html)

## 資料
- [AWS CodeBuild のドキュメント](https://docs.aws.amazon.com/ja_jp/codebuild/index.html)
- [【AWS Black Belt Online Seminar】AWS CodeBuild](https://youtu.be/Zzv1_ztf-B0)
