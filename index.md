# 考え方

- 自調自考
    - 何が必要かを整理して明らかにするために調べる
    - たいがいのことには先人の足跡がある
        - 標準規格: ISO, RFC, schema.org等
    - 道しるべがない、前提知識が足りない、などは素直に人に聞いて解決する
- 「なぜ」をクリアに、オープンにして行動する
    - 考えた過程を書き残す
    - 何度も自分で読み返す
- 人の承認を待たずに、やるべきことはやる
    - 意義について深く自問する
- やらない理由を考えてやらないよりも、やる理由をでっち上げてやってしまう人になる
    - プロとして信ずるところを実現するため、必要な社会的スキルを高める
- 設計の必然性を探究する
    - 機能単位のあるべき責務は何か？
    - 属性と思ったものは本当に内的属性か？
    - ストック/リソースと思ったデータはフロー/イベントではないか？
- 筋のいい解があるはずと楽観して深く調べ、考える
    - 要件を分解して標準的でシンプルなパーツの組合せに還元する
    - 複数の要件を同時に満たせるはず
    - 正しさ、良さへの執念を貫く
- 行き詰まったら、暫定解で割り切って力強く進む
    - ひとところに長く立ち止まらない
    - 後で改善する覚悟を強く持ち、そのときが来たら思い切ってやる
    - その覚悟は具体的に表現する
        - 今は通らないがいずれこのテストが通るようにする、のように

# アプリケーション

## サーバー
- Ruby 2.7 (随時最新バージョンに更新)
- Rails 6.1 (随時最新バージョンに更新)
- Sidekiq Enterprise
- RSpec
    - See [立ち上げ期スタートアップのテスト方針（Ruby on Rails編）](https://tech.machimachi.com/entry/2018/07/24/141144)
- etc.

## クライアント
- React.js
  - ReduxなどFlux Architectureは採用していない
- Flowtype
  - anyを使わないで型検査による品質保証を頑張る
- Jest
  - 型検査とE2Eテストでカバーできない挙動が難しいコンポーネントは書く
- ES2017
- WebpackerとSprocketsを併用
  - webpackerはやめて素のwebpackに移る計画
- Sass (SCSS)
  - styled-components, css modulesなどは未使用
- 情報ポータル・ランディングページ系
  - CDNに乗せる前提でステートレスに書いている
  - SEO重視
  - JavaScriptは最小限

サーバー(rubygems)、クライアント(npm packages)共にライブラリは自動でPRを出してアップグレードしている

gemにバグや非互換があれば事前に気づける程度までテストのカバレッジを高め、自信を持ってリリース

Rubyは緩めに設定したRubocop、JS/CSSはprettierで整形するPRを自動化している

# ミドルウェア

## データベース
- PostgreSQL
  - 11
  - with PostGIS
  - on Google Cloud SQL
- Redis
  - バックグラウンドジョブのキュー
  - on Redis Cloud
- Memcached
  - Railsのキャッシュ
  - on Memcached Cloud
- Elasticsearch
  - 住所検索
  - プレイス検索
  - ニュースの地域判定
  - on Elastic Cloud


# サーバー

- Google App Engine

# CI
- CircleCI 2.1
  - Performance Plan で並列化
  - E2Eテストとそれ以外をワークフローで分岐し、それぞれ `resource_class` や `parallelism` をチューニング
  - parallel_tests gem でインスタンス内のテスト実行も並列化してリソースを使い切る

# デプロイ

- CIが通ったら自動デプロイ
- プロダクションとステージングはmaster
- ステージングとプロダクションはデータを含めて同等環境

# 定時実行タスク

- Jenkins
  - 記録が残る、通知が出来る、設定を変更しやすいなど便利なので、Jenkinsから実行している
  - 通知配信など、長時間掛かるタスクはSidekiqからここに投げて実行し、デプロイに伴うリスタートなどの影響を受けないようにしている
- sidekiq-scheduler
  - 頻度が高いものはsidekiq-schedulerを使っている

# 静的アセットなど
- AWS S3
  - アップロードファイル
- GCS + Google Cloud CDN
  - 静的アセット
- Cloudflare Workers
  - CDN

# DNS
- Cloudflare
  - machimachi.comおよび開発用のドメイン管理
- AWS Route 53
  - コーポレート・ビジネスサイドのドメイン管理
  - Routefileで管理

# 開発環境
- rbenv
- docker-compose

# 監視
- Scout APM
- Stackdriver
- Google App Engine Dashboard
- Mackerel

# ユーザー行動ログ
- Mixpanel
- Google Analytics
- サーバー、クライアント両方から送っている

# ログ
- Stackdriver Logging

# メール
- SendGrid
  - WebhookからCloud Functions経由でBigQueryにイベントログを保存している

# SMS
- Accrete

# エラー検知
- Bugsnag

# 分析
- BigQuery
  - Embulkを使ってPostgreSQLとMixpanelのデータを日次でBigQueryにエクスポートしている
  - 視覚化はGoogle Data StudioとGoogle Spreadsheets

# タスクランナー
- Jenkins
  - Docker化してDokku上で運用

# FAQ
- なぜRails
  - 道具立ての豊富さと質
  - 泥臭い仕事も可能で柔軟
  - 情報の多さと質
  - 武者がRubyコミッターとして長年活動しておりRailsも実務経験豊富
- なぜReact
  - 1\) よく使われている 2) 経験ある 3) よいライブラリである
    - 一方向データバインディングによる状態管理の楽さ
    - パターンが決まっていて、作りやすい
  - Railsとの連携のしやすさ
    - react-railsがあった（現在は使っていない）
- なぜPostgreSQL
  - ほぼ間違いなく地理情報を扱う必要があるので
    - PostGISはとても強い
  - Rails業界で広く使われている
  - 機能が豊富で急な用件にも困らない
    - マテリアライズドビューも活用
    - 早くからUUID、JSON(JSONB)などをネイティブサポート
- なぜGoogle App Engine
    - See [マチマチをGAEで動かす話](https://tech.machimachi.com/entry/2018/08/20/133000)
- なぜネイティブアプリから作らないの
  - プロダクト・マーケット・フィットの検証はWebでもできる。
  - 審査のないモバイルWebはリリースが楽。
- Railsの実行環境はDockerにしないの
  - Dockerで動かす事自体はもうできている。今のところ必要性を感じないのでしていないだけ
- スタートアップにしてはCIやCDをちゃんとやってるけどなぜ
  - メンバーが少ないので、できることは極力自動化している

# 参考リンク

- [会社紹介](https://www.machimachi.co.jp/)
  - 「採用情報」のミッションとコアバリューは必読
- [マチマチ技術ブログ](https://tech.machimachi.com/)
  - 「マチマチとOSSの関わりをGitHubで振り返る」「続・マチマチとOSSの関わりをGitHubで振り返る」以降も、コミットログにOSSとの関わりの足跡が記されています
