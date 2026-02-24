# ⚙️ CI/CD パイプライン 詳細設計書

Jenkins × iOS (Swift) × GitHub × Azure  
Claude + Codex CLI 役割分担版（2025）

## 1. 目的

このドキュメントは、`mobile-codex-dev` に iOS 向け CI/CD パイプラインを段階的に導入するための詳細設計です。

- CI: Lint / Test / Build
- CD: TestFlight / App Store 申請
- Artifact: Azure Blob Storage 保存
- Signing: Fastlane Match（Azure）

## 2. 役割分担

| 担当 | 主な作業 |
|---|---|
| Claude | Jenkinsfile / Fastfile / Matchfile の設計・生成、エラー解析 |
| Codex CLI | ローカルのファイル作成・編集、Git 操作（commit/push） |
| 手動作業 | Jenkins / Azure / Apple Developer / GitHub Webhook の GUI 設定 |

## 3. ブランチ戦略

| ブランチ | 実行ステージ | 出力 |
|---|---|---|
| `feature/*` | Lint → Test | テスト結果 |
| `develop` | Lint → Test → Build | ビルド確認 |
| `main` | Lint → Test → Build → Upload(Azure) → TestFlight | TestFlight 配信 |
| `release/*` | Lint → Test → Build → Upload(Azure) → App Store(承認付き) | App Store 申請 |

> 推奨: `main` 直接 push を禁止し、PR マージ運用にする。

## 4. リポジトリ構成

```text
.
├── Jenkinsfile
├── Gemfile
├── .swiftlint.yml
├── docs/
│   └── cicd-detailed-design.md
└── fastlane/
    ├── Fastfile
    ├── Appfile
    └── Matchfile
```

## 5. 実装済みテンプレート

本ブランチには、次のテンプレートを追加済みです。

1. `Jenkinsfile`
2. `fastlane/Fastfile`
3. `fastlane/Appfile`
4. `fastlane/Matchfile`
5. `Gemfile`
6. `.swiftlint.yml`

## 6. 必須 Credentials / 環境変数

- `MATCH_PASSWORD`
- `ASC_KEY_ID`
- `ASC_ISSUER_ID`
- `ASC_KEY_CONTENT_BASE64`（`.p8` を base64 化した文字列）
- `AZURE_STORAGE_CONNECTION_STRING`
- `SLACK_WEBHOOK_URL`（任意）
- `APP_IDENTIFIER`
- `TEAM_ID`

## 7. 初期セットアップ手順

1. Jenkins macOS agent に Xcode/Fastlane/Bundler を導入
2. Azure Blob コンテナ作成
   - `ios-builds`
   - `ios-certificates`
3. Apple App Store Connect API Key 作成
4. Jenkins Credentials 登録
5. `feature/*` push で CI、`main` push で TestFlight を確認

## 8. 運用メモ

- 機密情報は **絶対にリポジトリへ直書きしない**。
- 失敗時は Jenkins ログを保存し、lane 単位で切り分ける。
- `bundle exec fastlane <lane>` をローカル再現の基準にする。
