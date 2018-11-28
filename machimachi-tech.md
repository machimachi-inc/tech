
# アプリケーション

## サーバー
- Ruby
- Rails
- RSpec
- Sidekiq
- etc.

## クライアント
- React.js
  -   ReduxなどFlux Architectureは採用していない
- Flowtype
    - anyを使わないで型検査による品質保証を頑張る
- Jest
    - 型検査とE2Eテストでカバーできない挙動が難しいコンポーネントは書く
- ES2017
- JavaScriptはWebpacker、CSSはSprocketsでコンパイル
    - Sassでgrubを使っているので、Webpackerに移行できず
- Sass(SCSS)
    - styled-components, css modulesなどは未使用
- 情報ポータル・ランディングページ系
    - CDNに乗せる前提でステートレスに書いている
    - JavaScriptは最小限


サーバー(rubygems)、クライアント(npm packages)共にライブラリは自動でPRを出してアップグレードしている

Rubyは緩めに設定したRubocop、JS/CSSはprettierで整形するPRを自動化している

# ミドルウェア

## データベース
- PostgreSQL
  - 9.6
  - with PostGIS
  - on Google Cloud SQL
- Redis
  - バックグラウンドジョブのキュー
  - on Redis Cloud
- Memcached
  - Railsのキャッシュ
  - on Memcached Cloud
- Elasticsearch
  - ニュースの地域判定、住所検索
  - on Elastic Cloud


# サーバー

- Google App Engine

# CI
- CircleCI 2.0

# デプロイ

- 全リモートブランチをGoogle App Engineのステージング用プロジェクトにデプロイしている
- masterのみプロダクション用プロジェクト

# 定時実行タスク

- Jenkins
  - 記録が残る、通知が出来る、設定を変更しやすいなど便利なので、Jenkinsから実行している
- sidekiq-scheduler
  - 頻度が高いものはsidekiq-schedulerを使っている

# 静的アセットなど
- AWS S3
- Cloudflare
- Fastly (辞めつつある)

# DNS
- AWS Route53
- Cloudflare

# 開発環境
- rbenv
- docker-compose

# 監視
- New Relic
- Stackdriver
- Google App Engine Dashboard
- Mackerel

# ユーザー行動ログ
- Mixpanel
- サーバー、クライアント両方から送っている

# ログ
- Stackdriver Logging

# メール
- SendGrid

# SMS
- Accrete

# エラー検知
- BugSnag

# 分析
- BigQuery
  - Embulkを使ってPostgreSQLとMixpanelのデータを日次でBigQueryにエクスポートしている
  - 視覚化はGoogle Data StudioとGoogle Spreadsheet

# タスクランナー
- Jenkins

# その他使われている技術
- Terraform
  - Jenkinsのセットアップで使っている
- Ansible
  - Jenkinsのセットアップで使っている

# FAQ
- なぜRails
  - CTOが習熟していた。7年以上の実務経験がある
- なぜReact
  - 1\) よく使われている 2) 経験ある 3) よいライブラリである
    - 一方向データバインディングによる状態管理の楽さ
    - パターンが決まっていて、作りやすい
  - Railsとの連携のしやすさ
    - react-railsがあった（現在は使っていない）
- なぜPostgreSQL
  - ほぼ間違いなく地理情報を扱う必要があるので
  - Rails業界で広く使われている
- なぜHeroku
  - CTOの前職でのノウハウがある
  - サーバー構築・管理のコストを外出しできる
  - 使い捨ての環境を作りやすい
    - テスト用アプリ、デモ用アプリなど必要になるのでここ重要
  - 太平洋横断のレイテンシーがUX的にボトルネックになるので、<del>いずれ国内に移管したい</del>移管した
- なぜGoogle App Engine
    - See [マチマチをGAEで動かす話](https://tech.machimachi.com/entry/2018/08/20/133000)
- なぜネイティブアプリから作らないの
  - 藤村はネイティブ開発の経験がないが、モバイルWebアプリの開発経験はある。また審査のないモバイルWebはリリースが楽。また、プロダクト・マーケット・フィットの検証はWebでもできる。ということで、ネイティブアプリは先送りになった
- Railsの実行環境はDockerにしないの
  - Dockerで動かす事自体はもうできていてブランチもある。今のところ必要性を感じないのでしていないだけ
- 初期のスタートアップにしてはCIやCDをちゃんとやってるけどなぜ
  - メンバーが少ないので、できることは極力自動化している
