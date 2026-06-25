## 実施結果

- 初期スコア: 0
- 最終スコア: 20066
- pass: true
- fail: 0

## 変更点

1. PHP実装で起動できるようにDocker/nginx設定を調整
2. トップページの投稿取得件数を制限
3. 画像をpublic/imageにキャッシュしてnginxから配信
4. comments(post_id, created_at)にインデックスを追加

## 特に効果があった改善

comments(post_id, created_at)のインデックス追加が最も効果的だった。
make_posts()では投稿ごとにcommentsをpost_idで検索し、さらにcreated_at順に最新コメントを取得していた。
インデックス追加前はGET / やGET /@userがタイムアウトしていたが、追加後はfailが0になった。

## うまくいかなかった/効果が小さかった改善

画像配信でSELECT *をやめる変更は、単体ではスコア改善が小さかった。
原因は不要カラム取得よりも、imgdataのBLOBを毎回MySQLから取得してPHPで返していることだと考えた。
そのため、画像をファイルにキャッシュしてnginxから返すようにした。
