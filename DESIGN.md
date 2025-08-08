# 設計書（DESIGN.md）

最終更新: 2025-08-08
対象: Same Game / さめがめ（Web実装、単一HTML、Vanilla JS + CSS3）
出典: `REQ.md`

---

## 1. 全体設計
- **構成**: 単一ファイル `index.html` に HTML/CSS/JS を内包。
- **主な責務**
  - 表示/UI: 盤面、スコア、操作ボタン、終了メッセージ。
  - ロジック: 盤面生成、グループ検出、削除、重力落下、列シフト、終了判定、スコア計算。
  - イベント: クリック/タップでセル選択、ボタン押下で新規ゲーム。
- **原則**: 最小限の DOM 更新（バッチ適用）、アクセシビリティ配慮（色+形で区別）。

## 2. 画面レイアウト
- **ヘッダー（固定）**
  - タイトル: "Same Game"
  - スコア: 現在スコアを数値表示
  - 新しいゲーム: 盤面再生成ボタン
  - 盤面サイズセレクト: ドロップダウン（10×10［既定］/ 10×15 / 15×10 / 15×15）
- **メイン**
  - 盤面グリッド: CSS Grid で N×M（既定例 10×15）
  - 終了メッセージ: 画面中央のモーダルオーバーレイで表示し、モーダル内に「新しいゲーム」ボタンを配置
- **フッター（任意）**
  - 簡単なヘルプ/著作権

レイアウトはレスポンシブで、幅に応じてセルサイズがスケール。最小タップ領域目安 36px 以上。

## 3. データ設計
- **定数**
  - `ROWS`, `COLS` は動的に変更可能（既定: ROWS=10, COLS=10）
  - `TILE_TYPES=5`
  - `SCORE_UNIT=10`
- **盤面表現**
  - 2次元配列 `board[rows][cols]`。要素は `null`（空）または `{ type: 0..4 }`。
  - 表示は CSS Grid 上にセル DOM を割り当て、`data-row`, `data-col`, `data-type` を付与。
  - `#board` の `grid-template-columns` を `repeat(COLS, var(--cell-size))` に設定（JSで動的反映）。
- **状態**
  - `score: number`
  - `gameState: 'playing' | 'ended'`
  - （UI）モーダル表示は DOM の `#overlay` に `show` クラス付与/除去と `aria-hidden` 切替で制御

## 4. 駒の種類と表現
- 種類: 5 種（●/▲/■/◆/★）
- 視覚表現（例）
  - type 0: 赤 丸（circle）
  - type 1: 青緑 三角（triangle）
  - type 2: 青 四角（square）
  - type 3: 緑 ダイヤ（diamond）
  - type 4: 黄 星（star）
- 実装案: CSS クラスで形状表現（border/transform） or 絵文字。可読性と軽さを優先。

## 5. 主要アルゴリズム
- **グループ検出（BFS/DFS）**
  - 入力: `startRow, startCol`
  - 条件: 同 `type` で上下左右に連結
  - 出力: セル座標配列 `group`
  - 削除可能条件: `group.length >= 2`
- **削除**
  - `group` のセルを `null` に設定
  - `score += group.length ** 2 * SCORE_UNIT`
- **重力落下**
  - 各列で下から詰め直し: 非 `null` を下端から連続配置
- **列シフト**
  - 左から順に空列を詰める: 空列を除去し、右側列を左へシフト
- **終了判定**
  - 全セルを走査し、`group.length >= 2` が一つもなければ `ended`

## 6. イベント駆動フロー
1. 初期化: `init()` で `board` 生成、DOM 構築、スコア=0
2. セル入力: クリック/タップで `onCellSelect(r,c)`
3. 検出: `findGroup(r,c)` → サイズ<2なら何もしない
4. 反映: begin batch → `removeGroup` → `applyGravity` → `shiftColumns` → end batch（DOM 再描画）
5. 終了判定: `hasAnyMoves()` でなければ `endGame()` を実行し、`#overlay.show` を表示（`aria-hidden=false`）
6. 新規ゲーム: `newGame()` で再初期化し、`#overlay` から `show` を外す（`aria-hidden=true`）
7. 盤面サイズ変更: `sizeSelect.change` → `ROWS/COLS` を更新 → `newGame()` を呼び出し（スコアリセット、`#board` を再構築し、グリッド列数を更新）

## 7. DOM/描画方針
- 盤面の DOM ノードは固定長で再利用（座標→index を決定）。
- 反映は **データを正**として DOM に同期。
- 更新の塊ごとに `requestAnimationFrame` でスタイル反映、CSS Transition で落下/シフト表現（初回は簡易）。
 - 終了モーダル要素: `#overlay`（`role="dialog"`, `aria-modal`）。表示は `.show` クラスでトグル。

## 8. アクセシビリティ
- 色だけに依存せず形状も併用。
- コントラスト確保（暗背景 × 鮮やかな駒）。
- フォーカスリング（キーボード対応は任意）。
 - モーダル: `role="dialog"`, `aria-modal`, `aria-hidden` を適切に切替。将来的にフォーカストラップ導入を検討。

## 9. 擬似コード
```js
function init(rows, cols) {
  board = createRandomBoard(rows, cols, TILE_TYPES);
  score = 0; gameState = 'playing';
  renderBoard(board);
  updateScore();
}

function onCellSelect(r, c) {
  if (gameState !== 'playing' || !board[r][c]) return;
  const g = findGroup(r, c);
  if (g.length < 2) return;
  removeGroup(g);
  applyGravity();
  shiftColumns();
  renderBoard(board); // or patch DOM
  updateScore();
  if (!hasAnyMoves()) endGame();
}
```

## 10. スタイル設計（抜粋）
- ルート変数: セルサイズ、色、影、角丸、トランジション時間。
- グリッド: `grid-template-columns: repeat(COLS, var(--cell-size));`
- セル: Flex 中央、`will-change: transform`, `transition: transform .15s ease, opacity .15s ease`。
- ホバー/選択: 枠線 or スケールアップ。
 - モーダル: 半透明背景 + `backdrop-filter: blur()`、カードは角丸+シャドウ、`opacity` と `pointer-events` で表示制御。

## 11. テスト観点（抜粋）
- グループ検出の境界条件（端/孤立/蛇行）
- 重力/列シフトの多段適用
- 大規模削除時のスコア、DOM 更新の一貫性
- 各ブラウザ/端末でのタップ精度

## 12. 将来拡張のフック
- サウンド: 効果音トリガー箇所（削除/終了）にフック関数
- 保存: `localStorage` でハイスコア記録
- テーマ: CSS 変数の切替

---
この設計は `REQ.md` を満たす最小構成をターゲットにし、段階的拡張を想定しています。
