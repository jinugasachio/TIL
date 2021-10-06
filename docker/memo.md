# Docker
## 公式ドキュメント
- [docker docs](https://matsuand.github.io/docs.docker.jp.onthefly/)
## アンチパターン
### .dockerignore
- 必要なものだけでなく汎用的なものを列挙すること（[参考](https://qiita.com/munisystem/items/b0f08b28e8cc26132212#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)）

## Tips
- `apt-get`を実行する場合、ダウンロード元を日本のサーバーにするとbuildの節約時間を減らすことができる場合がある（[参考](https://genzouw.com/entry/2019/09/04/085135/1718/)）