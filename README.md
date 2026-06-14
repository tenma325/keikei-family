# Kaikei.family — 家族で管理する家計簿

> 「家族みんなで、おカネの流れを見える化しよう。」
> 家族それぞれに固有の色を割り当て、その"色"で支出を可視化する家計簿プロトタイプ。
> アイボリー基調 + 全面流動アニメーション + 親権限による子供の支出コントロール。

🌐 **Demo**: https://tenma325.github.io/kaikei-family/

## ✨ Highlights

- **家族カラーパレット** — パパ(青)・ママ(桃)・そうた(橙)・ゆい(緑)・あおい(紫)＋予備(コーラル)。一人ひとりの"色"がそのままUIトーンとして可視化されます。
- **アイボリー基調** — `#FAF6EE` をベースに、複数の `--ivory-2` サーフェスをレイヤー状に構成。
- **流動アニメーション** — 背景に4つのblobがフロート、上下セクションは`<svg>`パスのモーフィングで波のように繋ぐ。スクロールで上部虹色プログレスバーが伸び、家族オーブがマウス追従で視差パララックス。
- **親権限ダッシュボード** — 週次キャッシュフロー(折線)・メンバー別支出(バー)・カテゴリ別スパークライン。
- **QRコード × バーチャルカード** — 子ども用にQR払い残高/上限/本日使用量を可視化、VISA風バーチャルカードをCSSグラデーションで再現。
- **閾値ルール** — On/Offトグル式のルール一覧(例: ゆいのお菓子週¥800超で通知→4回目で自動ブロック)。
- **ライブストリーム** — 直近の取引が6秒ごとに循環、家族みんなの"今この瞬間のおカネの動き"を表現。

## 🚀 Quick Start

プロトタイプは単一HTMLファイル。ビルド不要。

```bash
# 方法1: ブラウザで直接開く
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux

# 方法2: ローカルサーバで配信する場合(オプション)
python -m http.server 8080
# → http://localhost:8080/
```

依存関係は **Google Fonts (Plus Jakarta Sans + JetBrains Mono) のみ**。  
オフラインならCSSの `font-family: 'Plus Jakarta Sans', system-ui, sans-serif` でOSフォントにフォールバックします。

## 📂 Project Structure

```
family-kakeibo/
├── index.html              ← 単一ファイルプロトタイプ(42KB, フル実装)
├── docs/
│   ├── DESIGN.md           ← デザインシステム仕様(カラー/タイポ/アニメ)
│   └── ARCHITECTURE.md     ← 本番化に向けたADR (ollama + Express + SQLite)
└── .gitignore
```

## 🎨 Design Tokens (抜粋)

```css
:root{
  /* Base — ivory */
  --ivory:#FAF6EE;
  --ivory-2:#F4EDDF;
  --ink:#1F1B16;
  --ink-2:#4A423A;
  --muted:#8A7E70;
  --line:#E7DFCE;

  /* Family palette */
  --papa:#2F6BFF;      /* Admin */
  --mama:#FF7AA8;      /* Admin */
  --kodomo1:#FFB347;   /* そうた(小5) */
  --kodomo2:#3CC6A6;   /* ゆい(中2)   */
  --kodomo3:#A06CE4;   /* あおい(年長) */
  --kodomo4:#FF6F61;   /* 予備 */

  /* Status */
  --good:#3CC6A6;
  --warn:#FF7AA8;
  --bad:#FF6F61;
}
```

## 🛠 Roadmap

これは **プロトタイプ** です。本番化フェーズは [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) を参照。

- [ ] Vite + React 19 + TypeScript 移行
- [ ] Express + better-sqlite3 バックエンド
- [ ] ollama連携 (支出カテゴリ自動分類、月次レポート生成)
- [ ] Caddy リバースプロキシ + TLS
- [ ] マルチファミリー対応 (テナント分離)

## 📄 License

MIT

## ✍️ Author

tenma325
