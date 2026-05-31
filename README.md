# crw-docker-compose

[fastCRW](https://github.com/us/crw) の自分用 docker-compose ファイルです。

[公式の docker-compose.yml](https://github.com/us/crw/blob/main/docker-compose.yml) を元に、ローカルビルドを ghcr イメージに置き換えています。

## 構成サービス

| サービス | イメージ | 概要 |
|---|---|---|
| `crw` | `ghcr.io/us/crw:0.10.0` | CRW 本体。ポート 3000 で公開 |
| `searxng` | `searxng/searxng:2026.5.9-0cba32c15` | `/v1/search` を担うメタ検索バックエンド |
| `lightpanda` | `lightpanda/browser:latest` | 軽量 JS レンダラー（デフォルト有効） |
| `chrome` | `chromedp/headless-shell:latest` | フル Chromium レンダラー（`--profile heavy` でオプトイン） |
| `chrome-stealth` | `ghcr.io/browserless/chromium:v2.27.0` | ステルス Chromium レンダラー（`--profile stealth` でオプトイン） |

## 起動方法

### 基本（LightPanda のみ）

```bash
docker compose up -d
```

### フル Chromium あり（`heavy` プロファイル）

```bash
docker compose --profile heavy up -d
```

### ステルス Chromium あり（`stealth` プロファイル）

```bash
docker compose --profile stealth up -d
```

> `heavy` と `stealth` は実質的に排他です。どちらか一方を選んでください。

## 設定ファイル

| ファイル | 説明 |
|---|---|
| `config.docker.toml` | CRW の設定ファイル。コンテナに read-only でマウントされます |
| `config/searxng/settings.yml` | SearXNG の設定ファイル。コンテナに read-only でマウントされます |

`config.docker.toml` は[公式リポジトリ](https://github.com/us/crw)からコピーしたものをこのリポジトリに含めています。適宜カスタマイズして使用してください。

## 環境変数

`.env` ファイルを作成して以下を設定できます。

```env
# SearXNG のシークレットキー（本番環境では必ず変更してください）
# 生成: openssl rand -hex 32
SEARXNG_SECRET_KEY=change-me-with-openssl-rand-hex-32-please

# browserless のトークン（stealth プロファイル使用時）
# 生成: openssl rand -hex 24
BROWSERLESS_TOKEN=crwtest
```

## バージョンアップ

`crw` および `searxng` のイメージはバージョンが固定されています。アップデートする際は[公式の docker-compose.yml](https://github.com/us/crw/blob/main/docker-compose.yml) を参照してバージョンを更新してください。

## ライセンス注意事項

- **SearXNG**: AGPL-3.0。上流イメージをそのまま使用しています。
- **chrome-stealth (browserless)**: SSPL-3.0。スクレイピング機能を第三者に提供するサービスとして運用する場合は、法的レビューを行ってください。詳細は `docker-compose.yml` のコメントを参照してください。
