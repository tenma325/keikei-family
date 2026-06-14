---
date: 2026-06-14
type: project
tags: [project, design-system, kaikei, family-kakeibo, web, prototyping]
status: active
priority: high
started: 2026-06-14
ai-first: true
---

# Kaikei.family — 家族で管理する家計簿

## For future Claude
これは [[family-kakeibo]] のデザインシステム仕様書です。アイボリー基調＋家族ごとカラフル配色、流動アニメーション全面採用、親権限による子供の支出管理ダッシュボード、QRコード払い、バーチャルカード、月次閾値ルール機能をプロトタイプ化したもの。構築日: 2026-06-14。プロトタイプ: `C:\Users\user\family-kakeibo\index.html` (42KB, 単一ファイル)。

---

## 1. コンセプト
**「家族みんなで、おカネの流れを見える化しよう」**

- 家族それぞれに固有の色を割り当て、個性を可視化しつつ家計簿の"チーム感"を表現
- お金の絶え間ない動きを「波・液体・線」として全面アニメーション化
- アイボリー基調で全体を上品に保ち、子どもの色を"おいてかぶせた"デザインに

## 2. カラーパレット

### Base (アイボリー基調)
| Token | Hex | 用途 |
|-------|------|------|
| `--ivory` | #FAF6EE | メイン背景 |
| `--ivory-2` | #F4EDDF | セクション背景・チップ |
| `--ink` | #1F1B16 | テキスト・メインボタン |
| `--ink-2` | #4A423A | サブテキスト |
| `--muted` | #8A7E70 | メタ情報・ラベル |
| `--line` | #E7DFCE | ボーダー線 |

### Family Colors (家族パレット — カラフル)
| Token | Hex | 役割 |
|-------|------|------|
| `--papa` | #2F6BFF | パパ(Admin) |
| `--mama` | #FF7AA8 | ママ(Admin) |
| `--kodomo1` | #FFB347 | そうた(小5) |
| `--kodomo2` | #3CC6A6 | ゆい(中2) |
| `--kodomo3` | #A06CE4 | あおい(年長) |
| `--kodomo4` | #FF6F61 | 予備枠 |
| `--accent` | #FFD56B | アクセント |
| `--good` | #3CC6A6 | 予算内OK |
| `--warn` | #FF7AA8 | 警告 |
| `--bad` | #FF6F61 | 上限超過 |

## 3. タイポグラフィ

| ロール | Font | Size | Weight |
|--------|------|------|--------|
| Display | Plus Jakarta Sans | clamp(40,5.6vw,72px) | 600 (-.02em) |
| Heading | Plus Jakarta Sans | clamp(28,3.4vw,44px) | 600 |
| Body | Plus Jakarta Sans | 16px | 400 |
| Caption | Plus Jakarta Sans | 12-14px | 500 |
| Number/Mono | JetBrains Mono | varies | 500 |

## 4. アニメーション設計

### 流動性「お金の動き」の表現
- `.fluid-bg` の 4つの `blob` 要素が18sで `ease-in-out infinite alternate` のフロート → 背景全体がお金を媒介する液体のよう
- 上下セクション間の `.wave-seam` は縫い目を波線で隠す → シームレスな流れ
- SVG `<animate>` で path の `d` を14s/11sごとにモーフィング → グラデーション波が常に形を変える
- スクロールポジションに対し上部プログレスバーが虹色グラデーションで `scaleX` 変形
- `.ribbon` 統計値、`.bar-fill` メンバー支出、`lang-` フローチャートpathが `1.2s cubic-bezier(.2,.8,.2,1)` で順次立ち上がる
- マウス追従で `.orb` が `translate` で視差パララックス
- 取引リストが6秒ごとに `shift/push` で更新 → ライブ感

### 周期
- spin: 16s linear (brand)
- float: 18s ease-in-out alternate (blobs)
- pulse: 2s (live dot)
- pulse-ring: 2.6s (QR scan)
- wave-shift: 8s (seam)
- stream rotation: 6s (txn feed)

## 5. レイアウト & スペーシング

Stripe準拠の4pxベース:
- scale: 4, 8, 12, 16, 20, 24, 32, 64, 100
- container max: 1280px
- section padding: 100px y / 32px x
- radius: 4px(btn), 14px(cardの中), 16-20px(card), 99px(chip)

## 6. セクション構成

1. **Hero** — Display + 家族5人オーブ(球状+mousemove視差)
2. **Stats Ribbon** — 4つのKPIストリップ(総支出/残予算/取引数)
3. **Dashboard (親権限)** — 週次キャッシュフロー折線グラフ + メンバー別支出バー
4. **Categories** — 食費・教育・娯楽のスパークライン付きカード
5. **Pay (親権限)** — QRコード(yui中2用) + バーチャルカード
6. **Rules (親権限)** — メンバー×カテゴリの閾値トグル式ルール
7. **Live Stream** — 直近取引(自動更新、6秒ローテーション)
8. **Footer** — ダークウェーブで終端

## 7. 権限モデル

| Role | できること |
|------|------------|
| Admin(親) | 全家族の支出閲覧 / カード発行 / ルール作成 / QR入金額設定 / 上限ロック |
| Member(子) | 自分のQR表示 / バーチャルカード利用 / 入金をリクエスト |

ルールトグル `.toggle` をクリックすると `.toggle.on` (background:var(--good)) でON状態が視覚化。offなら`var(--ivory-2)`+shadow付きオフ状態。

## 8. 技術スタック(プロトタイプ)
- 単一HTML、外部依存はGoogle Fonts (Plus Jakarta Sans + JetBrains Mono) のみ
- CSS: フルカスタム(フレームワークなし)
- JS: vanilla、IntersectionObserverで `.reveal.in` 付与、setTimeoutフォールバック1.5s付
- データ: 静的ハードコード(モック)
- アニメーション: CSS keyframes + SVG `<animate>` + JS `requestAnimationFrame`
- Iconfont/絵文字なし、英字イニシャルを`.ico`で表示(高級感維持・[[cosmic-rewards]]規約準拠)

## 9. ファイル
- プロトタイプ: `C:\Users\user\family-kakeibo\index.html` (42,689 bytes)
- 起動: file:// で直接開く(サーバ不要)

## 10. 関連ノート
- [[family-kakeibo-architecture]] — 構築環境(ollama)仕様
- [[family-kakeibo-tasks]] — 今後のタスク
- [[cosmic-rewards]] — 同ユーザの色・タイポ規約リファレンス
