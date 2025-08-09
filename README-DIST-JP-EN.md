# Same Game v0.2.0 — Distribution README (JP/EN)

このアーカイブは v0.2.0 の配布物です。アクセシビリティ強化（キーボード操作、モーダルのフォーカストラップ、低モーション対応）と、駒の種類を4種（赤/緑/黄/青）へ統一しています。

## 使い方（日本語）
- 実行: `index.html` をブラウザで開くか、`python3 -m http.server 5173` → `http://localhost:5173/index.html`
- 操作: 矢印キーでセル選択、Enter/Space で削除。クリック/タップも可。
- 終了: 削除可能グループが無くなるとモーダル表示。新しいゲームで再開。
- 検証（整合性）:
  - macOS: `shasum -a 256 samegame-v0.2.0.html` / `shasum -a 256 samegame-v0.2.0-source.zip`
  - Linux: `sha256sum samegame-v0.2.0.html` / `sha256sum samegame-v0.2.0-source.zip`

## How to Use (English)
- Run: open `index.html` in a browser, or `python3 -m http.server 5173` → `http://localhost:5173/index.html`
- Play: move with arrow keys; remove with Enter/Space. Click/tap also supported.
- End: when no removable groups remain, a modal appears. Use New Game to restart.
- Verify (integrity):
  - macOS: `shasum -a 256 samegame-v0.2.0.html` / `shasum -a 256 samegame-v0.2.0-source.zip`
  - Linux: `sha256sum samegame-v0.2.0.html` / `sha256sum samegame-v0.2.0-source.zip`

## Changes in v0.2.0
- Accessibility: keyboard navigation, modal focus trap, reduced motion.
- Touch targets: minimum cell size 36px.
- Pieces: 4 types (red/circle, green/diamond, yellow/star, blue/square).
- Docs: JP/EN contributor guide (AGENTS.md) and checklists.

Project: https://github.com/ardbeg1958/samegame-windsurf-GPT5
