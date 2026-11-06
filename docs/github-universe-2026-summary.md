# GitHub Universe 2026 発表内容まとめ

> 作成日: 2026-11-07
> 対象: GitHub Universe 2026（2026年10月28日〜29日／米国サンフランシスコ Fort Mason Center）

---

## 0. 全体サマリ

| 項目 | 内容 |
|---|---|
| 基調講演（Day 1） | Jay Parikh（Microsoft EVP, CoreAI）。デモは Cassidy Williams / Burke Holland / Damian Brady / April Leonard |
| 閉会基調講演（Day 2） | Martin Woodward。OSS・メンテナー保護・開発者の喜びがテーマ |
| メインメッセージ | GitHubを「世界で最も包括的なエンドツーエンドのソフトウェアデリバリーシステム」にする |
| 3つの軸 | **Velocity**（速度・コスト効率）／**Intelligence**（コンテキスト統合と継続学習）／**Quality**（デフォルトでセキュア、出荷前にバグ検出） |
| 主役の発表 | **Copilot Fusion（GA）**、Copilot app強化、**GitHub EnterpriseにCopilot全機能が同梱**、**新Gitアーキテクチャ** |
| 同時公開 | Octoverse 2026 レポート |

キーメッセージ：「成果物は“コード量”ではなく“完了した仕事（機能・バグ修正・セキュリティ修正）”」。SDLCはもはや直線的ではなく、開発者が方向性と意思決定・説明責任を持ち、エージェントが実装と検証を担う、という整理。

---

## 1. GitHub Copilot 関連

### 1.1 Copilot Fusion（GA）— マルチモデル・ルーティング／オーケストレーション

- モデル選択画面に「Copilot Fusion」という**仮想モデル**として表示され、タスク内容（推論・コード生成・デバッグ・ツール利用）を評価して実際のモデルに振り分ける
- 9月のリサーチプレビュー「Project HydraFusion」がベース（Octoverseによると約6万セッションをルーティング済み）
- **実行パターン（3種）**
  - `Single`：単一モデルで処理
  - `Cascade`：ドラフト → ジャッジ評価 → 必要なら上位モデルへエスカレーション
  - `Critique`：他モデルがドラフトをレビューし、その指摘で改訂
- **エフォートレベル（3種）**：`Standard`（日常作業）／`High`（より大きなタスク向けにオーケストレーション追加）／`Max`（深い推論優先）
- ベンチマーク（GitHub公式発表）：Artificial Analysis Coding Agent Index で Fusion Max が Opus 5.5 Max と2ポイント差以内、コスト半分・タスクあたり時間40%減
  - ※国内報道（クラウドWatch）では比較対象・数値の表記が公式ブログと異なる。**公式ブログの数値を正とし、社内展開時は一次情報で再確認推奨**
- ガバナンス面：**プラン／管理者のモデルポリシーを尊重**、利用したモデル分の課金。Fusion自体はサブエージェントを起動しない（Projectsのオーケストレーションとは別機能）
- キャンペーン：有料Copilotプランは2026年11月30日までFusionのモデル料金30%割引

### 1.2 GitHub Copilot app（デスクトップ）強化

エージェント前提で設計されたデスクトップアプリ。Issue → マージまでを1つのアプリで完結させる構想。コード以外（仕様書作成、設計議論、テレメトリ分析）も扱う。ブラウザ／ターミナル／カスタムビューの「キャンバス」を持つ。

| 機能 | 概要 | ステータス |
|---|---|---|
| **Copilot Projects** | 目標を与えると、複数リポジトリにまたがって並列・フルマネージドのエージェントセッションを実行。進捗・判断・指示を1か所で管理 | パブリックプレビュー |
| **Session continuity** | 同じクラウドエージェントセッションを app / VS Code / CLI / Web / モバイル間で引き継ぎ | 発表 |
| **Copilot Connectors** | GitHub管理の検証済みMCP接続。一度認証すれば各Copilot面で利用可（Slack, Work IQ, Sentry, Vercel, Netlify, Cloudflare, Figma, Datadog 等） | パブリックプレビュー（Actions上のセッションでは未対応） |
| **Copilot Dreaming** | 古くなったカスタムインストラクションを検知し、更新PRを提案。学習したメモリも表示 | パブリックプレビュー（Proプラン・データレジデンシー環境は対象外、AIクレジット消費） |
| **Security canvas** | code scanningアラートをapp内で説明・修正。PR前のローカル変更レビューも可能 | パブリックプレビュー（Dependabot/secret scanningアラートは対象外） |

- Copilotのハーネスは Microsoft 全体に展開済みで、**Microsoft Foundry 経由で同じハーネスを使ったエージェント構築**も可能に

### 1.3 Copilot Automations

- リポジトリ内にAIワークフローを作成。**クラウド実行**で、手動／API／スケジュール／イベントトリガーで起動
- 用途例：Issueトリアージ、コードレビュー、Sentryのエラーを起点とした調査〜修正
- **制御機能**：利用可能ツールの指定、「PR作成は許可・マージは不許可」といった権限分離、**AIクレジット上限の設定**、チーム共有
- **自己改善**：実行結果から学習し、改善案を提案（人がレビュー・承認）
- 要件：対象の有料プラン、private/internalリポジトリ、AIクレジット＋GitHub Actions使用量

### 1.4 Copilot code review Max

- 既存の `Lite`（一般的な問題）／`Balanced`（より高い推論）に、最深レベルの `Max` を追加
- ReviewBench の高深刻度Issueで最高F1スコア（GitHub発表）
- ローンチ以来の累計指摘は2億件
- 組織が有料利用を有効化すれば、**Copilotサブスクリプションのないメンバーもクレジット課金でレビュー利用可**（現時点ではGitHub.com）

### 1.5 PR／マージ周りの改善

| 機能 | 概要 |
|---|---|
| **Agent Merge** | レビュー指摘対応・CI失敗・コンフリクトをバックグラウンドで処理し、要件を満たしたらマージ可能に（fork PR・stacked PRは対象外、auto-mergeは別設定） |
| **Stacked PRs** | 依存関係のある変更を積み重ねてレビュー・マージ。10月6日にGA（Universe前） |
| **GitHub CLI 添付** | `--attach` で画像・動画をIssue/PR/コメントにインライン添付 |
| **Issue Artifacts** | 計画・調査・リンク等をIssueに紐付けて保持 |
| **リポジトリPRビュー刷新** | オープン中の作業の状態把握を改善 |

---

## 2. ライセンス・課金モデルの変更（重要）

**GitHub Enterprise 契約で Copilot 全機能が利用可能に（別途サブスク不要、AIクレジット従量課金）**

- 対象：Copilot app、code review、automations ほか全機能
- 提供経路：現時点は **GitHub Sales 経由**、セルフサービスは近日提供
- 既存の **Copilot Business / Copilot Enterprise 顧客は現行プラン継続も移行も選択可**
- 示唆：ライセンス（席数）管理から**クレジット予算管理・ツール権限管理**へガバナンスの重心が移る

---

## 3. セキュリティ関連

| 機能 | 概要 | ステータス・注意点 |
|---|---|---|
| **Agentic Autofix** | code scanningの指摘をCopilotにアサインすると修正PRを生成。バックログ一括アサインも可 | — |
| **AI Scan** | リポジトリ全体スキャン、CodeQL非対応言語（PHP, Shell, Terraform, Dockerfile, JSP, Blazor等）のPRもAIで検出 | 指摘は助言扱いで、**rulesetによるマージ必須条件にはできない** |
| **AI検出によるシークレットのプッシュ保護** | 既知パターンに一致しない非構造化シークレット（パスワード等）もpush時にブロック | パブリックプレビュー。push protectionとAIシークレット検出の両方の有効化が必要、AIクレジット消費 |
| **Package firewall** | 悪意ある／リスクのあるnpmパッケージを、エージェント・開発ツール・CIランナーがインストールする前にブロック（パッケージ年齢・マルウェアアドバイザリ等のポリシー） | パブリックプレビュー |
| **Reachability analysis** | Dependabotアラートについて、アプリコードから脆弱な関数への呼び出し経路を静的解析し優先度付け | パブリックプレビュー（JS/TSのみ）。到達可能性＝実行可能性・悪用可能性ではない |

---

## 4. インフラ・Gitアーキテクチャ刷新

### 4.1 2026年のインフラ増強

- CPU 400万コア、高速ストレージ 160PB、ネットワーク容量 50Tb/s を追加
- **Azureのマルチリージョン構成への移行**を進行中（2027年初頭に完了予定）
- 今年はパフォーマンス・信頼性に課題があったことを認め、「成長は言い訳にならない」と言及

### 4.2 新Gitアーキテクチャ

- 背景：エージェントがブランチ作成・履歴参照・pushを大量に行う。9月だけで70億超のコミット
- 第一段：ロック単位を**リポジトリ全体 → 更新ブランチのみ**に変更し、書き込みスループット3〜4倍
- 3原則：**プリミティブの単純化／ストレージとコンピュートの分離／調整の最小化**
  - 永続的な正本は Azure Blob Storage、エフェメラルなサービングノードがキャッシュ・スケール、参照トランザクションは専用の複製ログサービスで処理
- テスト結果：1リポジトリに5,000エージェントで**毎秒7,200書き込み（現行の180倍）**。最も忙しいOSSリポジトリの24時間分のトラフィックを1分で再生
- 全ユーザーの移行は2027年初頭の見込み
- その他：AIゲートウェイ（兆単位トークン処理）、プロンプトキャッシュ、Actionsのキャパシティ拡大とコントロールプレーン強化、エッジ拠点追加 など

---

## 5. Octoverse 2026 ハイライト（集計期間：2025/10/1〜2026/9/30）

| 指標 | 数値 |
|---|---|
| 開発者数 | 2.25億人超（2年連続で毎秒1人増） |
| コードpush | 179億（2024年と2025年の合計超） |
| コミット | 343億（2年前の3倍超） |
| マージされたPR | 13億（1日平均約350万） |
| Actionsジョブ | 毎秒1,440（1日約1.24億、前年の3倍超） |
| Dependabot PRマージ | 3,000万超 |
| push protectionスキャン | 58億回（前年ペースの約4倍） |

**エージェント活用の実態**

- 9月時点で**PRの40%超がエージェントのドラフトから開始**（3月は17%）
- Copilot月間アクティブユーザーの55%がエージェント機能を利用（前年35%）、クラウドエージェント利用は前年比230%増
- エージェント設定ファイルのある新規リポジトリは、ないものに比べ平均8倍のコミット
- モデル選択の多様化：明示的にモデルを選ぶユーザーの61%が複数モデルを利用、単一モデルのシェアは最大でも25%以下。有料ユーザーの63%が自動モデル選択を1回以上利用
- 言語：TypeScript > Python > JavaScript。Rustは貢献者数が倍増
- 国別開発者数：日本は 715万人で5位

---

## 6. Day 2 閉会基調講演（Martin Woodward）

- **OSSメンテナー保護**：AI生成の低品質PR（いわゆる“slop”）への対策
  - 書き込み権限のないユーザーのオープンPR数上限（WIP limit）
  - 10/28アップデートで、アカウント年齢や過去のマージ実績による**例外設定**が可能に
  - PRを共同作業者限定にする／無効化する設定（2月から提供）
- **「DevOpsこそエージェント活用の前提条件」**：テスト・Lint・ブランチ保護など人間向けに整えたガードレールが、そのままエージェントの安全装置になる（Home Assistant、OpenPrinting＋Hiveの事例）
- **GitHub Next「Chopin」（実験的プロトタイプ）**：エージェントとの計画立案を、個人のチャットからチームのマルチプレイヤーキャンバスへ。プロアクティブ調査、クリック可能なモック、**誰がいつ決めたかを記録する意思決定カード**、タスク／PRへの自動分解
- 研究者向け：Citation対応、ORCID連携。GitHub Campus Programは約2,500校・60か国
- ハッカブルなカンファレンスバッジ（Arm協力、RP2350搭載、MicroPython）を使ったエッジMLデモ

---

## 7. 注目セッション（抜粋）

- Agentic Workflows: Automation at scale（ガバナンス・監視・セキュリティを備えた「ソフトウェア工場」の構築）
- Agentic engineering past the pilot: An enterprise playbook（エンタープライズでのPoC → 運用モデル化）
- Securing MCP: Real exploits, what actually mitigates them（MCPサーバーの本番ホスティングと脅威モデル）
- Shut the context window!（トークン消費を膨らませる習慣とその対策）
- Your benchmark is lying（Copilotの本番モデル評価の実際）

---

## 8. GHEC運用・AIガバナンス観点での注目ポイント

| 論点 | 検討事項 |
|---|---|
| **課金モデル移行** | GHEC同梱化により「席数管理」から「AIクレジット予算管理」へ。Business/Enterprise継続か移行かの比較、組織／チーム単位のクレジット上限設計、Automations単位の上限設定の運用ルール化 |
| **モデルポリシー** | Fusionは管理者のモデルポリシーを尊重するため、許可モデルリストの設計がFusionの振る舞いに直結。Dreamingなど特定モデル必須の機能との整合も確認 |
| **エージェント権限** | Automationsの「PR作成可・マージ不可」、Agent Mergeの適用範囲、ruleset／ブランチ保護との組み合わせを標準パターンとして定義 |
| **MCP / Connectors** | GitHub管理の検証済みMCP接続が登場。社内MCPレジストリ／許可リストとの関係整理、接続先サービスごとの承認フロー |
| **セキュリティ（ASPM観点）** | AI Scan・AIシークレット保護・Package firewall・Reachabilityは既存のGHAS／外部ASPMツールと重複・補完しうる。**AI Scanはマージ必須条件にできない**点はゲート設計上の制約。外部ASPM製品評価時の比較軸に追加推奨 |
| **プレビュー機能の扱い** | Projects、Connectors、Dreaming、Security canvas、Package firewall等は多くがパブリックプレビュー。本番展開ポリシー（プレビュー機能の有効化基準）を明確化 |
| **データレジデンシー** | Dreamingはデータレジデンシー環境非対応。他機能も環境別の対応状況を要確認 |
| **前提となるDevOps成熟度** | テスト／CIが不十分なリポジトリへのエージェント適用は品質リスク。適用対象リポジトリの基準づくり |

---

## 参考リンク（一次情報）

- GitHub Blog: GitHub Universe 2026: Evolving every layer of the stack
  https://github.blog/news-insights/company-news/github-universe-2026-evolving-every-layer-of-the-stack/
- Universe 2026 発表まとめページ
  https://github.com/events/universe/recap
- Changelog: Copilot Fusion
  https://github.blog/changelog/2026-10-28-route-and-orchestrate-models-with-github-copilot-fusion/
- Changelog: GitHub Copilot app Universe releases
  https://github.blog/changelog/2026-10-28-github-copilot-app-universe-releases/
- Changelog: Copilot Automations
  https://github.blog/changelog/2026-10-28-build-shared-workflows-for-your-team-with-copilot-automations/
- Changelog: Copilot code review Max
  https://github.blog/changelog/2026-10-28-copilot-code-review-merge-with-confidence-with-max-and-approvals/
- Changelog: GitHub Enterprise now includes access to GitHub Copilot
  https://github.blog/changelog/2026-10-28-github-enterprise-now-includes-access-to-github-copilot/
- Engineering: The bridge to agent-scale Git infrastructure
  https://github.blog/engineering/architecture-optimization/the-bridge-to-agent-scale-git-infrastructure/
- Octoverse 2026
  https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/
- Microsoft Command Line: Highlights from GitHub Universe 2026
  https://commandline.microsoft.com/github-universe-2026-recap/
- クラウドWatch: 「GitHub Universe 2026」開幕、Copilot FusionのGAを発表
  https://cloud.watch.impress.co.jp/docs/event/2152257.html
- DEV Community: GitHub Universe 2026: My recap from San Francisco（両日の基調講演の詳細レポート）
  https://dev.to/playfulprogramming/github-universe-2026-my-recap-from-san-francisco-23hd
