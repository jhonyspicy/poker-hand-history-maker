# CLAUDE.md

## プロジェクト概要

テキサスホールデムのハンドヒストリーをスマホで片手入力できる React + TypeScript 製 Web アプリ。
入力したデータはローカルストレージに保存し、GTO Wizard（PokerStars 形式）でエクスポートできる。

## 開発コマンド

```bash
bun run dev      # 開発サーバー起動
bun run build    # 本番ビルド
bun run lint     # ESLint
bun run preview  # ビルド後のプレビュー
bun run deploy   # GitHub Pages へ手動デプロイ
```

## 技術スタック

- React 18 + TypeScript
- Vite 6
- ローカルストレージ（永続化）
- GitHub Pages（`/poker-hand-history-maker/` にホスト）

## 設計原則

### UI / UX
- **片手・片指操作が最優先** — スマホ縦持ちで全操作が完結すること
- **文字入力は SB/BB/Ante のみ** — それ以外はタップ・スライダーで入力する
- **スライダーは「だいたい」で十分** — スタック量・ベット額は正確性より入力速度を優先
- **カード入力はなぞり式** — ランク→スートの順に連続タップ（例: K→♡→T→♢）で複数枚を素早く入力できること
- **UI 言語は日本語**

### データ
- 永続化はすべてローカルストレージで完結させる（サーバーなし）
- エクスポート形式は PokerStars ハンドヒストリー形式（GTO Wizard が読み込める）

## アプリの入力フロー

```
1. 自分のハンド（2枚）とポジション
2. SB / BB / Ante（前回値をデフォルトに、数値キーボードで入力）
3. 参加人数（2〜10人を選択）
4. プリフロップ: UTG から順に全プレイヤーのアクション
   - アクション選択: Raise / Call / Fold
   - Raise / Call → スライダーでスタック量
   - Raise      → さらにスライダーでレイズ額
5. フロップ（3枚）入力
6. フロップのアクション
7. ターン（1枚）入力 → アクション
8. リバー（1枚）入力 → アクション
9. 保存
```

- ストラドル・ミシシッピは対象外
- 各ストリートのアクションは Check / Bet / Call / Raise / Fold

## ポジション定義

人数によって使うポジション名を変える。プリフロップのアクション順は UTG → ... → BTN → SB → BB。

| 人数 | ポジション（UTG 側から） |
|------|------------------------|
| 2 | SB, BB |
| 3 | BTN, SB, BB |
| 4 | UTG, BTN, SB, BB |
| 5 | UTG, CO, BTN, SB, BB |
| 6 | UTG, HJ, CO, BTN, SB, BB |
| 7 | UTG, LJ, HJ, CO, BTN, SB, BB |
| 8 | UTG, UTG+1, LJ, HJ, CO, BTN, SB, BB |
| 9 | UTG, UTG+1, UTG+2, LJ, HJ, CO, BTN, SB, BB |
| 10 | UTG, UTG+1, UTG+2, MP, LJ, HJ, CO, BTN, SB, BB |

## データモデル（概略）

```typescript
type Suit = 'h' | 'd' | 'c' | 's';
type Rank = '2'|'3'|'4'|'5'|'6'|'7'|'8'|'9'|'T'|'J'|'Q'|'K'|'A';
type Card = { rank: Rank; suit: Suit };

type Action = {
  position: string;       // 'UTG', 'BTN', 'SB', 'BB', ...
  action: 'fold' | 'call' | 'raise' | 'check' | 'bet';
  amount?: number;        // ベット/レイズ額（スライダー値）
  stackBefore?: number;   // アクション前スタック（スライダー値）
};

type HandHistory = {
  id: string;
  createdAt: number;
  heroPosition: string;
  heroHand: [Card, Card];
  sb: number;
  bb: number;
  ante: number;
  playerCount: number;
  preflop: Action[];
  flop?: { board: [Card, Card, Card]; actions: Action[] };
  turn?: { card: Card; actions: Action[] };
  river?: { card: Card; actions: Action[] };
};
```

## エクスポート形式

PokerStars ハンドヒストリー形式（テキスト）で出力する。GTO Wizard はこの形式を読み込める。

```
PokerStars Hand #<id>: Hold'em No Limit (¥<sb>/¥<bb>) - <date>
Table '<table>' <playerCount>-max Seat #<btn_seat> is the button
Seat 1: <position> (¥<stack> in chips)
...
*** HOLE CARDS ***
Dealt to Hero [Kh Td]
UTG: raises ¥<amount> to ¥<amount>
...
*** FLOP *** [Ah 2d 3c]
...
```

## ファイル構成（予定）

```
src/
  components/
    CardInput/       # なぞり式カード入力コンポーネント
    StackSlider/     # スタック・ベット額スライダー
    ActionSelector/  # Raise/Call/Fold/Check/Bet 選択
    HandForm/        # ハンド入力フォーム（全ストリート）
    HistoryList/     # 保存済みハンド一覧
  hooks/
    useHandHistory.ts  # ローカルストレージの読み書き
  utils/
    export.ts          # PokerStars 形式へのシリアライズ
    positions.ts       # 人数→ポジション名マッピング
  types.ts             # 型定義
```
