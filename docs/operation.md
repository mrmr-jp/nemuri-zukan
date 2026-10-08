# ねむりの図鑑 運用手順書

最終更新：2026-10-08

アプリを修正して公開するまでの手順です。2026-10-08から、**Claude Codeで修正して、Gitで公開する**流れに変わりました。

---

## 1. 全体の流れ

```
 iMac（~/dev/nemuri-zukan）           GitHub（mrmr-jp/nemuri-zukan）      公開ページ
 ① Claude Codeで修正                                                  
 ② ブラウザで確認                                                      
 ③ コミット（iMacに記録）  ── ④ push ──▶  main に反映  ── 自動 ──▶  GitHub Pages
                                                                     （数分で更新）
 ⑤ 必要ならClaude版にも反映
```

公開URLは変わりません。すでにURLを伝えた人に、改めて連絡する必要はありません。

---

## 2. いつもの更新手順

### ① Claude Codeを起動する

```
cd ~/dev/nemuri-zukan
claude
```

起動したら、最初にGitHubの最新を取り込みます（別の場所で更新していた場合に備えるため）。

> 「GitHubから最新を取ってきて」

### ② 修正を頼む

> 「〇〇を△△に直して」

修正の内容は、右側の欄（変更ファイルの一覧）と、Claude Codeの説明で確認します。

### ③ ブラウザで確認する

> 「ブラウザで確認したいので、ローカルで開ける状態にして」

Claude Codeが確認用のURL（例：`http://localhost:8000/`）を出すので、ブラウザで開いて確認します。index.html をダブルクリックで開くこともできますが、一部の機能は公開URLで確認するのが確実です。

### ④ コミットしてpushする

> 「問題ないので、コミットしてpushして」

pushの前に確認を求められるので、内容を見てOKします。

### ⑤ 反映を確認する

1. GitHubのリポジトリ画面右側の「Deployments」で、時刻が新しくなり緑のチェックが付くのを待つ（数分）
2. スマホで公開URLを開き、下の「公開後の確認チェックリスト」を確認する
3. 古い表示のままなら、ページを再読み込みする

### ⑥ Claude版にも反映する（必要な場合）

GitHub Pages版とClaude版は別物です。Claude版も同じ内容にしたいときは、Claudeのチャットで更新します。

---

## 3. 公開後の確認チェックリスト

- [ ] 文字が表示され、スライダーとボタンが動く
- [ ] 画面下のタブバーで4ページを行き来できる
- [ ] 日本語／英語を切り替えられる
- [ ] 結果カードの下部のURLが `mrmr-jp.github.io/nemuri-zukan/` になっている
- [ ] 「カードを保存」で共有メニューが開き、画像を保存できる
- [ ] 詳細画面のリンク（写真・Wikipedia・動物園）が開く
- [ ] シークレットが起動する（起動方法は非公開メモ参照）
- [ ] ダークモードでも文字が読める

---

## 4. 元に戻したいとき

Gitがすべての版を記録しているので、日付付きの控えファイル（`index_20261008.html` など）は不要になりました。

| やりたいこと | Claude Codeへの頼み方 |
|---|---|
| まだコミットしていない変更を取り消す | 「今の変更を取り消して、最後のコミットの状態に戻して」 |
| 公開済みの変更を取り消す | 「直前のコミットを打ち消すコミットを作って（revert）、pushして」 |
| 過去の版を見たい | 「index.html の変更履歴を一覧で見せて」 |

公開済みの変更を取り消すときは、履歴を消す方法（reset や force push）ではなく、**打ち消す変更を追加する方法（revert）**を使います。履歴が残るので、安全です。

---

## 5. 新しいアプリを公開するとき（GitHub Pages）

ねむり図鑑で行った初回公開の手順です。新しいアプリでも、そのまま使えます。

1. GitHub右上の「＋」→「New repository」を開く。Owner を `mrmr-jp` にする
2. Repository name を入力する（URLの末尾になる。英小文字とハイフン）。Public、READMEはオフで「Create repository」
3. iMacのアプリのフォルダでClaude Codeを起動し、「このリポジトリにpushして」と頼む（作成直後の画面に表示されるSSHのアドレスを伝える）
4. リポジトリの「Settings」→ 左メニュー「Pages」を開く（アカウント全体の設定画面ではない）
5. Source を「Deploy from a branch」、Branch を「main」と「/ (root)」にして「Save」
6. 1〜数分後に再読み込みし、「Your site is live at …」で公開URLを確認する
7. 任意：リポジトリトップの「About」の歯車 →「Website」に公開URLを入れる

公開URLは `https://mrmr-jp.github.io/リポジトリ名/` になります。

---

## 6. よくあるトラブルと対処

| 症状 | 原因 | 対処 |
|---|---|---|
| 文字がなく、ボタンだけ並んで動かない | ファイルをプレビュー表示している（JavaScriptが動かない） | 公開URL、またはローカル確認用のURLをブラウザで開く |
| 更新したのに古い内容のまま | 反映前、またはブラウザに古い版が残っている | Deploymentsの時刻を確認し、再読み込みする |
| pushで `Permission denied (publickey)` | iMacとGitHubの鍵の接続が切れている | ターミナルで `ssh -T git@github.com` を実行し、表示を確認する |
| pushで `rejected`（fetch first） | GitHub側に、iMacにない変更がある | 「GitHubから最新を取ってきてから、もう一度pushして」と頼む |
| Claude Codeが `Can't reach the API server` | Claude側の接続が一時的に切れている | 待つ／ネット接続を確認する。コミット済みなら、ターミナルで `git push origin main` を実行すれば自分でpushできる |
| 外部の人がClaude版を開けない | Claude版の閲覧にはClaudeアカウントが必要 | GitHub Pagesの公開URLを伝える |
| 今日の一匹が0時に変わらない | 開きっぱなしの画面は自動で更新されない | 再読み込みする（端末の日付で決まる） |
| 古いURL（mira-0e.github.io/…）が開けない | Organizationへ移動してURLが変わった | `mrmr-jp.github.io/nemuri-zukan/` を伝え直す |

---

## 7. 用語集

| 用語 | 意味 |
|---|---|
| リポジトリ | ファイルと、その変更履歴を入れておく保管場所 |
| コミット | 変更を確定して、iMacの中に記録すること |
| push | iMacの記録をGitHubに送ること |
| pull | GitHubの最新の記録をiMacに取り込むこと |
| clone | GitHubのリポジトリを、iMacに丸ごとコピーしてくること |
| revert | 過去のコミットを打ち消す変更を、新しく追加すること |
| GitHub Pages | リポジトリのHTMLを、そのままWebページとして公開する無料機能 |
| Organization | 複数のリポジトリをまとめる入れ物。ログインするアカウントではない |
| main / (root) | 公開元のブランチとフォルダ。ここではリポジトリの一番上の階層 |
| CLAUDE.md | Claude Codeが起動時に読む、プロジェクトの説明書 |
