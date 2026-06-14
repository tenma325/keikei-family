---
date: 2026-06-14
type: adr
status: proposed
tags: [architecture, kaikei, family-kakeibo, ollama, self-hosted, dev-environment]
ai-first: true
---

# family-kakeibo — ollama 構築環境仕様

## For future Claude
[[family-kakeibo]] を ollama 自己ホスト環境で動かすための技術スタック選定 (ADR)。プロトタイプ段階は単一HTMLファイルで完結するが、本番指向では Express + SQLite + 自己ホストLLM をベースにする。confidence: medium (プロトタイプレイヤーが対象)。

---

## Context

ユーザ指示: 「構築データは Obsidian へ md 保存、ollama にて構築環境を保存」

→ Obsidian: 設計仕様書 (本ノート群) で保存済み。  
→ ollama: ローカル環境構築のレシピを定義。  
本ADRは「ollama 内で再現できる自己完結した開発環境」のコア構成要素と理由を記述する。

## Decision

### スタック概要
```
Frontend  : Vite + React 19 + TypeScript (Stripe inspired design system)
Backend   : Express + better-sqlite3 + zod
Runtime   : Node.js 22 LTS
LLM       : ollama (llama3.1 / qwen2.5) — 家族カテゴリ自動分類等
Storage   : SQLite (`data/kaikei.db`)
Migrate   : node-pg-migrate 風の自作スクリプト (SQLite向け)
Reverse   : Caddy (TLS + ローカルプロキシ)
```

### ollama で何を動かすか
1. **支出カテゴリ自動タグ付け** — OCR/QRから取得したテキスト→「食費/教育/娯楽/突発」に分類
2. **月次家族レポートの自動生成** — 家族向けに「先月〇〇くんはゲーム課金が+12%増えています」等の所見生成
3. **ルール異常検知** — `Z-score` で閾値逸脱を検出した取引の説明文を生成

### 推奨モデル
- llama3.1:8b-instruct-q5_K_M — バランス (≈5GB)
- qwen2.5:7b-instruct-q5_K_M — 日本語精度重視
- embeddingモデル: nomic-embed-text (取引履歴ベクトル化)

## Consequences

### Pros
- 完全オフライン運用可 → 家族財務データを外部に出さない
- Stripeライクなデザイン + 国内ファミリーフォーマット両立
- Cosic Rewards の規約 (JetBrains Mono + 絵文字禁止 + ダarthテーマ流用) と整合

### Cons
- ローカルLLMの推論速度はクラウド比 10-100倍遅い
- ブラウザから直接 ollama 接続は CORS で一工夫必要 (プロキシ必須)
- SQLiteのため水平スケール不可 (シングルファミリー前提なら問題ない)

## Implementation Plan

### Phase A — バックエンド最小
1. Caddyで `caddy reverse-proxy --from :5173 --to :3000` 相当を `Caddyfile` に書く
2. Express + TypeScript で `/api/transactions` `/api/rules` `/api/members` の3エンドポイント
3. better-sqlite3 で `members`, `transactions`, `rules` の3テーブル

### Phase B — LLM連携
1. `ollama serve` を `:11434` で起動
2. Express middleware として `POST /api/llm/categorize` → ollama.chat に投げる
3. 結果を `transactions.category` に保存

### Phase C — フロント連携
1. 既存プロトタイプ `index.html` を `<script type="module">` で distributed build
2. `src/lib/stripe-tokens.ts` に本デザインシステムトークンを移行
3. TanStack Query で `/api/*` を呼び出し

## ファイル/サービス構成

```
~/projects/family-kakeibo/
├── proto/
│   └── index.html            ← 既存プロトタイプ
├── app/
│   ├── src/
│   │   ├── routes/api/        ← Express routers
│   │   ├── db/
│   │   │   ├── schema.sql
│   │   │   └── kaikei.db
│   │   └── llm/
│   │       └── categorize.ts
│   ├── package.json
│   ├── tsconfig.json
│   └── Caddyfile
├── ollama/
│   ├── Modelfile             ← 家族財務特化のカスタムモデル
│   └── prompts/
│       ├── categorize.txt
│       └── monthly-report.txt
└── README.md
```

## 関連ノート
- [[family-kakeibo-design]] — デザインシステム
- [[family-kakeibo]] — プロジェクト本体
- [[cosmic-rewards]] — 既存 convention 参考
