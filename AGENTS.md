# Repository Guidelines

## Project Structure & Module Organization
- Root files: `index.html` (single-file app: HTML + CSS + JS), `README.md`, `REQ.md`, `DESIGN.md`, `DEVPROCESS.md`, `DEVSTORY.md`.
- No src/ directory; game logic lives inline in `index.html`.
- Node metadata: `package.json` (no scripts) and `node_modules/` (not required to run the game).

## Build, Test, and Development Commands
- Run (file URL): `open index.html` or drag-drop into a browser.
- Run (local server, recommended for CORS parity):
  - Python: `python3 -m http.server 5173` then visit `http://localhost:5173/index.html`.
- Build: none (static site, no bundler).
- Lint/format: none configured.

## Coding Style & Naming Conventions
- JavaScript: 2-space indent, semicolons required, single quotes for strings; `camelCase` for variables/functions (e.g., `findGroup`, `applyGravity`), `SCREAMING_SNAKE_CASE` for constants (e.g., `ROWS`, `COLS`).
- CSS: custom properties in `--kebab-case`; class names in `kebab-case` (e.g., `.board-wrap`, `.piece`).
- HTML: keep structure semantic; accessibility attributes are present (`aria-*`, focus management). Preserve them on changes.
- Prefer small, pure functions and keep all logic inside `index.html` unless a strong reason to refactor.

## Testing Guidelines
- Current: manual testing in modern Chrome/Firefox/Safari/Edge.
- Manual QA checklist: new game starts, group highlight on hover/touch, score increments with n²×10, gravity + column shift work, end-game overlay appears, size selector updates board.
- Optional automation (when introduced): place browser tests under `tests/` as `*.spec.js` (e.g., Playwright). Keep coverage ≥70% for game logic helpers.

## Commit & Pull Request Guidelines
- Commit style: Conventional Commits (e.g., `feat:`, `fix:`, `docs:`) as used in history.
- One logical change per commit; keep messages imperative and concise.
- PRs must include: clear summary, rationale, before/after screenshots or short GIF for UI changes, steps to verify, and any accessibility notes.
- Link related issues; note any follow-ups explicitly.

## Architecture & Agent Notes
- Scope: single-file, framework-free implementation; fast load and low complexity are priorities.
- Don’t introduce a build step or external state unless necessary. If adding files, document structure in README and update this guide.
- Security: no secrets; static assets only. Keep dependencies minimal and audited before adding.

## Accessibility Checklist
- Keyboard: Tab/Shift+Tab cycles Header → Size Select → Score → New Game → Board → Footer.
- Focus: visible focus ring; do not hide it.
- Activation: Enter/Space trigger buttons; arrow keys change the size select.
- Overlay: end-game modal traps focus; background is inert until closed.
- ARIA: score uses `aria-live`; board region is labeled appropriately.
- Contrast: text/background contrast ratio ≥ 4.5:1.
- Non-color cues: do not rely on color alone (shapes also indicate type).
- Responsive: usable down to ~320px viewport width.
- Touch targets: interactive targets are ~40px or larger.
- Reduced motion: respect `prefers-reduced-motion` (limit transitions if needed).
- Announcements: critical events (end state, score updates) are announced.
- Console: no runtime errors in DevTools during interactions.

## 日本語ガイド (For Japanese Contributors)
- 構成: 主要実装は単一の `index.html`（HTML/CSS/JS 一体）。設計・要件は `DESIGN.md`/`REQ.md` を参照。
- 実行: `index.html` をブラウザで直接開くか、`python3 -m http.server 5173` 実行後に `http://localhost:5173/index.html` へアクセス。
- コーディング規約: JSは2スペース、セミコロン必須、変数/関数は `camelCase`、定数は `SCREAMING_SNAKE_CASE`。CSSは `kebab-case`、カスタムプロパティは `--var` 形式。既存のアクセシビリティ属性（`aria-*` など）を保持。
- テスト: 手動確認でOK。新規ゲーム開始、グループのハイライト、スコアが n²×10 で加算、重力と列シフト、終了オーバーレイ、サイズ変更の反映を確認。
- コミット/PR: Conventional Commits（例: `feat:`, `fix:`, `docs:`）。PRには要約、理由、UI変更のスクショ/GIF、検証手順、a11y注意点、関連Issueを添付。
- 方針: フレームワーク/ビルド導入は原則不可。必要な場合は理由と影響を記述し、`README.md` と本ガイドを更新。

### 日本語例（テンプレ & サンプル）
- コミット例:
  - `feat: 盤面サイズセレクタを追加`
  - `fix: 重力処理のバグを修正`
  - `docs: README に実行手順を追記`
- PR テンプレ（コピペ可）:
  ```
  概要:
  変更点:
  動作確認手順:
  - python3 -m http.server 5173
  - ブラウザで http://localhost:5173/index.html を開く
  - [ ] 新規ゲーム開始を確認
  - [ ] グループのハイライトを確認
  - [ ] スコアが n²×10 で加算される
  - [ ] 重力 + 列シフトが正しい
  - [ ] 終了オーバーレイが表示される
  スクリーンショット/GIF:
  関連Issue:
  備考/a11y:
  ```
- 命名例: JS `findGroup`, `applyGravity`, `SCORE_UNIT` / CSS `.board-wrap`, `.piece`, `--cell-size`。
- よく使うコマンド: `open index.html` ／ `python3 -m http.server 5173`。

### アクセシビリティ検証チェックリスト（日本語）
- キーボード操作: `Tab`/`Shift+Tab` でフォーカスがヘッダー→サイズ選択→スコア→新しいゲーム→盤面→フッターの順に移動する。
- フォーカス可視性: フォーカスリングが見える（カスタムで隠さない）。
- 操作: `Enter`/`Space` でボタンが作動。サイズ選択は矢印キーで変更できる。
- オーバーレイ: ゲーム終了時にダイアログへフォーカスが移り、閉じるまでフォーカストラップされる（背景は `inert`）。
- ARIA: スコアは `aria-live` で読み上げ更新。盤面領域に適切な `role`/`aria-label` が設定されている。
- コントラスト: 文字と背景のコントラスト比が少なくとも 4.5:1 を満たす。
- 代替手段: 色のみで情報を伝えない（形状も併用）。
- レスポンシブ: 幅320px程度まで縮小しても操作しやすい。
- タッチ目標: タップターゲットはおおむね 40px 以上。
- 低モーション: `prefers-reduced-motion` 設定でアニメーションが軽減される（必要に応じて検討）。
- アナウンス: 終了状態やスコア更新など重要イベントが適切に通知される。
- エラー: コンソールにエラーが出ていない（DevTools で確認）。
