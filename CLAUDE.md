# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 概要

「ねむりの図鑑 / The Sleep Almanac」— 自分の睡眠時間を入力し、現存の動物や絶滅した生き物の睡眠時間と比べる Web アプリ。
公開リポジトリ `mrmr-jp/nemuri-zukan`（GitHub Pages で公開）。

- GitHub Pages 版：https://mrmr-jp.github.io/nemuri-zukan/
- Claude 上にも別の公開版「ねむりの図鑑（Claude）」がある（https://claude.ai/artifact/VUKWNc1byzrK3raTuEjxCe 、`PUBLIC_URL` の既定値）。**内容を変えたときは GitHub Pages 版と Claude 版の両方に反映が必要**。このリポジトリへの push だけでは Claude 版は更新されない。

## 構成と開発方法

- **`index.html` 1ファイルだけで完結**（HTML・CSS・JS すべてインライン）。ビルド、パッケージ管理、lint、テストは存在しない。
- 外部依存は Google Fonts（Zen Maru Gothic / Zen Kaku Gothic New）のみ。ライブラリは使っていない。
- 動作確認はブラウザで開くだけ。ローカルなら `python3 -m http.server` などで配信して `http://localhost:8000/` を開く。
- これまでのコミットは GitHub の Web 画面からのアップロード（"Add files via upload"）。

## index.html の中身（大きな流れ）

- `<style>`：色はすべて `:root` の CSS 変数。ダークモードは `prefers-color-scheme` と `:root[data-theme]` の両方で定義しているので、色を変えるときは**ライト・ダーク両方の定義を更新する**。
- `<main>` 内に4つのページ `.page[data-page="home|quiz|list|zukan"]`。`showTab()` が `hidden` を切り替えるだけの擬似タブ方式。
- `<script>` 内のデータ定義（この3つが中心）：
  - `SRC`：出典辞書（キー → `{ja, en}`）。`GEN`（一般的な紹介値）と `EST`（本アプリ独自推定）は出典一覧には表示されない。
  - `D`：生き物データ配列（約124件）。フィールドは `n` 和名 / `ne` 英名 / `sci` 学名 / `e` 絵文字 / `h` 1日の睡眠時間 / `g` 分類（mammal, bird, reptile, amphibian, fish, insect）/ `t` living か extinct / `r` 信頼度 A=学術文献・B=一般解説・C=推定 / `s` SRC のキー配列 / `k` 眠り方（half, stand, micro、任意）/ `m`・`me` 説明文（日・英）。
  - `T`：UI 文言の辞書 `T.ja` / `T.en`。HTML 側は `data-i="キー"` を付けると `applyLang()` が差し込む。`{x}` 形式のプレースホルダは `t(key, {x:...})` で置換。
- 描画は `render()` が中心（ダイヤル、近い生き物、一覧など）。言語切替時は `applyLang()` → `render()`。
- その他の機能：図鑑の発見記録・クイズ（`newQuiz` / `drawQuiz`）、今日の一匹（日付ハッシュで決定）、シェア用の結果カード（canvas で画像生成 `makeCard`）、隠しキャラ「マリリン」（`openSecret`、架空キャラなので検索・画像リンクを付けない）。
- 保存は `localStorage` のみ（`store` ヘルパー経由、キー：`sleepH` `sleepLang` `sleepSeen` `sleepQuiz` `sleepSecret`）。外部送信はしない、とアプリ内で明記しているので、送信系の処理を足さないこと。
- `PUBLIC_URL`：`*.github.io` 上では自身の URL、それ以外では claude.ai の公開版 URL を使う（結果カードやシェア文に入る）。

## 変更時の注意

- 文言を追加・変更するときは **`T.ja` と `T.en` の両方**、生き物を追加するときは `m` と `me` の両方を書く。
- 生き物データには必ず出典（`s`）と信頼度（`r`）を付ける。絶滅種の値は推定（`EST`）であることを UI で明示している方針を崩さない。
- 医療・健康アドバイスではない旨の免責表示がある。睡眠の良し悪しを断定する表現は避ける。
- JS が動かない環境向けの `<noscript>` 案内がある（ファイルプレビューでは動かない旨）。
