# muse-poc

Muse × Claude Code の GitHub 経由受け渡しの動作確認用フォルダです。

## 回し方（深川の手順）

1. このリポジトリを clone（済みなら pull）する
2. ターミナルでこのフォルダに入る: `cd mobile-codex-dev/muse-poc`
3. Claude Code を起動して、こう頼む:
   「AGENTS.md の取り決めに従って、TASKS.md の Task 001 を実施してPRを作って」
4. PRができたら Muse（チャット）に知らせる → Muse がレビューを返す
5. GitHub上でレビューが見えたら疎通確認完了。マージは任意

取り決めの正本は AGENTS.md、タスク一覧は TASKS.md です。
