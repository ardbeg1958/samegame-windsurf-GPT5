# ハイスコアランキング機能 設計書

## 概要

既存のSame Gameにローカルストレージベースのハイスコアランキング機能を追加する。単一HTMLファイル構成を維持し、既存のUI/UXパターンに合わせて実装する。

## アーキテクチャ

### データ構造

#### ハイスコアデータ形式
```javascript
{
  scores: [
    {
      score: 1250,
      date: "2025-01-17T10:30:00.000Z",
      boardSize: "10x10"
    }
  ]
}
```

#### ローカルストレージキー
- `samegame-highscores`: ハイスコアデータを格納

### コンポーネント設計

#### 1. HighScoreManager クラス
```javascript
class HighScoreManager {
  constructor() {
    this.storageKey = 'samegame-highscores';
    this.maxEntries = 10;
  }
  
  // スコア保存
  saveScore(score, boardSize) { }
  
  // ハイスコア取得
  getHighScores() { }
  
  // 新記録判定
  isNewRecord(score) { }
  
  // データリセット
  clearScores() { }
}
```

#### 2. UI コンポーネント

**ハイスコアボタン（ヘッダー）**
- 位置: 「新しいゲーム」ボタンの隣
- アイコン: 🏆 + "ハイスコア"

**ハイスコアモーダル**
- 既存の終了モーダルと同様のスタイル
- ランキングテーブル表示
- リセットボタン
- 閉じるボタン

**新記録通知**
- ゲーム終了モーダル内に表示
- アニメーション効果付き

## インターフェース設計

### HTML構造

```html
<!-- ヘッダーに追加 -->
<button id="highScoreBtn" class="btn" aria-label="ハイスコアランキングを表示">
  🏆 ハイスコア
</button>

<!-- ハイスコアモーダル -->
<div id="highScoreOverlay" class="overlay" role="dialog" aria-modal="true" aria-hidden="true">
  <div class="modal-card">
    <h2>🏆 ハイスコアランキング</h2>
    <div id="highScoreList" class="high-score-list">
      <!-- ランキングテーブル -->
    </div>
    <div class="modal-actions">
      <button id="clearScoresBtn" class="btn btn-secondary">記録をリセット</button>
      <button id="closeHighScoreBtn" class="btn btn-primary">閉じる</button>
    </div>
  </div>
</div>
```

### CSS設計

```css
.high-score-list {
  max-height: 300px;
  overflow-y: auto;
}

.high-score-table {
  width: 100%;
  border-collapse: collapse;
}

.high-score-row {
  border-bottom: 1px solid var(--border-color);
}

.rank-badge {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 12px;
  font-size: 0.8em;
  font-weight: bold;
}

.rank-1 { background: gold; color: #333; }
.rank-2 { background: silver; color: #333; }
.rank-3 { background: #cd7f32; color: white; }

.new-record-badge {
  animation: pulse 1s infinite;
  background: linear-gradient(45deg, #ff6b6b, #ffd93d);
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}
```

## データフロー

### 1. スコア保存フロー
```
ゲーム終了 → スコア計算 → HighScoreManager.saveScore() → 
ローカルストレージ更新 → 新記録判定 → UI更新
```

### 2. ハイスコア表示フロー
```
ハイスコアボタンクリック → HighScoreManager.getHighScores() → 
データ取得 → テーブル生成 → モーダル表示
```

### 3. 新記録通知フロー
```
スコア保存時 → isNewRecord()判定 → 
新記録の場合: 特別UI表示 → アニメーション実行
```

## 主要アルゴリズム

### スコア保存アルゴリズム
```javascript
saveScore(score, boardSize) {
  const scores = this.getHighScores();
  const newEntry = {
    score,
    date: new Date().toISOString(),
    boardSize
  };
  
  scores.push(newEntry);
  scores.sort((a, b) => b.score - a.score);
  scores.splice(this.maxEntries); // 上位10位まで保持
  
  localStorage.setItem(this.storageKey, JSON.stringify({ scores }));
}
```

### 新記録判定アルゴリズム
```javascript
isNewRecord(score) {
  const scores = this.getHighScores();
  if (scores.length === 0) return { isNew: true, rank: 1 };
  
  const rank = scores.findIndex(s => s.score < score) + 1;
  return {
    isNew: rank > 0 || scores.length < this.maxEntries,
    rank: rank || scores.length + 1
  };
}
```

## エラーハンドリング

### ローカルストレージエラー
- ストレージ容量不足: 古い記録を削除して再試行
- ストレージ無効: 機能を無効化し、エラーメッセージ表示
- データ破損: デフォルト値で初期化

### データ検証
- スコア値の妥当性チェック（数値、正の値）
- 日付形式の検証
- 盤面サイズの検証

## アクセシビリティ

### キーボードナビゲーション
- Tab順序: ハイスコアボタン → モーダル内要素 → 閉じるボタン
- Escape キーでモーダル閉じる
- Enter/Space でボタン操作

### スクリーンリーダー対応
- `aria-label` でランキング情報を説明
- `aria-live` で新記録通知
- テーブルヘッダーとセルの関連付け

### 視覚的配慮
- 十分なコントラスト比
- フォーカス表示
- 色だけに依存しない情報伝達

## パフォーマンス考慮

### データサイズ最適化
- 最大10件の記録のみ保持
- 不要なデータ項目の削除
- JSON圧縮（必要に応じて）

### UI応答性
- ローカルストレージアクセスの非同期化
- 大量データ時の仮想スクロール（将来拡張）
- アニメーション最適化

## テスト観点

### 機能テスト
- スコア保存・取得の正確性
- ランキングソートの正確性
- 新記録判定の正確性
- モーダル表示・非表示
- リセット機能

### エラーケーステスト
- ローカルストレージ無効時
- データ破損時
- 容量不足時

### アクセシビリティテスト
- キーボード操作
- スクリーンリーダー読み上げ
- フォーカス管理

## 既存システムとの統合

### 既存コードへの影響
- `endGame()` 関数にスコア保存処理を追加
- ヘッダーUIにハイスコアボタンを追加
- 既存のモーダルシステムを拡張

### 設定の互換性
- 既存の盤面サイズ設定と連携
- 既存のスコア計算ロジックを活用
- 既存のアクセシビリティ機能を継承

## 実装完了記録

### 実装日時
2025年1月17日

### 実装された機能
- ✅ HighScoreManagerクラス（完全なエラーハンドリング付き）
- ✅ ハイスコアランキング表示モーダル
- ✅ 新記録通知システム（アニメーション付き）
- ✅ ハイスコアリセット機能
- ✅ 完全なアクセシビリティ対応
- ✅ レスポンシブデザイン対応
- ✅ 堅牢なエラーハンドリング（ストレージ容量不足、データ破損対応）

### 修正された問題
- ✅ 新しいゲームボタンのセレクター問題を修正（`document.querySelector('.btn')`から`document.querySelector('.toolbar .btn:last-child')`に変更）

### テスト結果
- ✅ 全機能の動作確認完了
- ✅ 既存機能との互換性確認完了
- ✅ アクセシビリティテスト完了
- ✅ エラーハンドリングテスト完了

### パフォーマンス
- ローカルストレージサイズ: 最大10件の記録で約1KB以下
- UI応答性: 全操作が即座に反応
- メモリ使用量: 追加のメモリ使用量は最小限