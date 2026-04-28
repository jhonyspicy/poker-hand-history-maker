# Poker Hand History Maker

スマホで片手でサクッと入力できるポーカーハンドヒストリー記録アプリ。

テキサスホールデムのハンドを素早く記録し、GTO Wizard で読み込み可能な形式でエクスポートできます。

## 特徴

- **片手・片指操作** — スマホを縦持ちしたまま全操作が完結
- **文字入力を最小限に** — SB/BB/Ante 以外はタップ・スライドで入力
- **なぞり入力式カード入力** — ランク→スートの順に連続タップ（例: K→♡→T→♢）で複数枚を素早く入力
- **スライダーでスタック・ベット額入力** — 正確な数値より「だいたいこのくらい」を素早く選べることを優先
- **ローカルストレージ保存** — サーバー不要、端末内に完結
- **GTO Wizard エクスポート** — PokerStars 形式のハンドヒストリーとして書き出し

## アプリの入力フロー

```
1. 自分のハンド (2枚) とポジションを入力
2. SB / BB / Ante を入力（前回値がデフォルト、数値キーボードで入力）
3. 参加人数を選択（2〜10人）
4. UTG から順に各プレイヤーのアクションを入力
   └ Raise / Call / Fold
     └ Raise / Call 選択時 → スライダーでスタック量を入力
     └ Raise 選択時    → さらにスライダーでレイズ額を入力
5. フロップ (3枚) を入力
6. フロップのアクションを入力（Check / Bet / Call / Fold）
7. ターン (1枚) → アクション入力
8. リバー (1枚) → アクション入力
9. 保存 → ローカルストレージへ
```

## 技術スタック

- [React](https://react.dev/) + [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/)
- ローカルストレージ（永続化）
- GitHub Pages（ホスティング）

## 開発環境のセットアップ

```bash
bun install
bun run dev
```

## ビルド

```bash
bun run build
```

## GitHub Pages へのデプロイ

`main` ブランチへプッシュすると GitHub Actions が自動デプロイします。

手動でデプロイする場合:

```bash
bun run deploy
```

## ポジション早見表

| 人数 | 順番（UTG 側から） |
|------|-----------------|
| 2 | SB, BB |
| 3 | BTN, SB, BB |
| 4 | UTG, BTN, SB, BB |
| 5 | UTG, CO, BTN, SB, BB |
| 6 | UTG, HJ, CO, BTN, SB, BB |
| 7 | UTG, LJ, HJ, CO, BTN, SB, BB |
| 8 | UTG, UTG+1, LJ, HJ, CO, BTN, SB, BB |
| 9 | UTG, UTG+1, UTG+2, LJ, HJ, CO, BTN, SB, BB |
| 10 | UTG, UTG+1, UTG+2, MP, LJ, HJ, CO, BTN, SB, BB |
