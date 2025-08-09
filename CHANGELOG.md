# Changelog

## v0.2.0 — 2025-08-09

### Release Summary
- 目的: 盤面のキーボード操作、モーダルのフォーカストラップ、低モーション対応、タッチ目標拡大でアクセシビリティを強化。
- 追加: JP/EN の貢献ガイドとアクセシビリティチェックリスト。

### New Features
- キーボード操作: 盤面セルにロービング `tabindex` を導入。矢印キーで移動、Enter/Space で削除実行。
- フォーカストラップ: 終了ダイアログ表示中、背景を `inert` 化しフォーカス循環をモーダル内に限定。
- 低モーション配慮: `prefers-reduced-motion: reduce` でアニメーション/トランジション抑制。
- タッチ目標: セルの最小サイズを 36px に拡大。

### Documentation
- `AGENTS.md`: 貢献ガイドを追加（英日対応）。
  - 構成/コマンド/コーディング規約/テスト/PR要件。
  - 日本語テンプレ（コミット例・PRテンプレ・QAチェック）。
  - 英日アクセシビリティチェックリスト。

### Developer Notes
- 影響範囲: `index.html`（ARIA属性、キーボード操作、CSSメディアクエリ、フォーカストラップ）。
- 互換性: 破壊的変更なし。UI/操作性のみ改善。
- コミット: `822f723` feat(a11y): keyboard navigation, modal focus trap, reduced motion, larger touch targets。

### How To Run
- 直接: `open index.html`
- ローカルサーバ（推奨）: `python3 -m http.server 5173` → `http://localhost:5173/index.html`

### Verification
- Tab 移動順が期待通り（ヘッダー → 盤面 → フッター）。
- 盤面上で矢印キー移動、Enter/Space で削除できる。
- 終了ダイアログ表示時、背景にフォーカスできない（`inert`）。
- 動きを減らす設定でアニメーションが抑制される。
- スコア/状態更新が `aria-live` で読み上げられる。
