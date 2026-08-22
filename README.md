# Claude Code Ping

Claude Code CLI を GitHub Actions から 5 時間ごとに実行し、
ごく少量のトークンを消費させ続けるためのリポジトリです。

## セットアップ

1. ローカル（認証済みの端末）で長期トークンを発行する。

   ```bash
   claude setup-token
   ```

   表示された `sk-ant-oat...` を控える。API キー（`sk-ant-api...`）でも動作する。

2. GitHub のリポジトリ設定でシークレットを登録する。

   `Settings` > `Secrets and variables` > `Actions` > `New repository secret`

   | Name | Value |
   | --- | --- |
   | `CLAUDE_SESSION_TOKEN` | 手順 1 で取得したトークン |

3. `Actions` タブから **Claude Code Ping** を選び、`Run workflow` で手動実行して動作確認する。

## 仕様

- スケジュール: `0 */5 * * *`（UTC。GitHub 側の都合で数分〜数十分遅延することがある）
- 実行内容: 空ディレクトリ `/tmp/empty_workspace` に移動して `claude -p "ping" --max-turns 1`
- リポジトリのファイルをコンテキストに載せないため、`actions/checkout` は意図的に実行していない

## 注意

- 60 日間リポジトリに活動（コミット等）がないと、GitHub はスケジュール実行を自動的に無効化する。
  その場合は `Actions` タブから手動で再有効化するか、何らかのコミットを行うこと。
- トークンには有効期限がある。期限切れになるとワークフローが失敗するので、
  失敗通知が届いたら `claude setup-token` で再発行してシークレットを更新すること。
