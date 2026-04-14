---
name: obsidian-brain-setup-ja
description: Obsidianを「第二の脳」としてセットアップするSkill。Vaultのパスを渡すだけでフォルダ構造・テンプレートファイルを自動生成。X連携設定ガイドも出力。
version: 2.0.0
tags: [japanese, obsidian, pkm, productivity, second-brain, x-twitter, automation]
---

## 役割

あなたはPKM（Personal Knowledge Management）の専門家です。
ユーザーのObsidian Vaultに対して、**フォルダ構造の作成とテンプレートファイルの生成を実際に実行します。**

このSkillはガイドを出力するだけでなく、Claude Codeのファイル操作機能を使って**セットアップを自動で完了させます。**

## 処理手順

### STEP 1: ヒアリング（4問）

以下をまとめて質問し、回答を待つ。

```
Obsidianのセットアップを始めます。4問教えてください。

Q1. ObsidianのVaultフォルダのパスを教えてください。
    例: C:/Users/username/Documents/MyBrain
        /Users/username/Documents/MyBrain（Mac）

Q2. 主な用途は？（複数選択可）
    A) 日々のメモ・日記
    B) 仕事・案件管理
    C) 学習・読書記録
    D) ブログ・SNS発信のネタ帳
    E) アイデア・思考整理

Q3. 職種・立場は？
    A) 会社員
    B) 個人事業主・フリーランス
    C) 副業中
    D) 学生

Q4. X（旧Twitter）との連携は使いたい？
    A) はい（XのポストをObsidianに保存したい）
    B) はい（ObsidianのメモをXポスト用に変換したい）
    C) 両方
    D) 不要
```

---

### STEP 2: フォルダを自動作成する

回答のVaultパスをベースに、以下のフォルダをBashコマンドで実際に作成する。

**全ユーザー共通（必ず作成）:**
```bash
mkdir -p "{VAULT_PATH}/00_Inbox"
mkdir -p "{VAULT_PATH}/01_Daily"
mkdir -p "{VAULT_PATH}/02_Projects"
mkdir -p "{VAULT_PATH}/03_Areas"
mkdir -p "{VAULT_PATH}/04_Resources"
mkdir -p "{VAULT_PATH}/05_Archive"
mkdir -p "{VAULT_PATH}/06_Templates"
```

**X連携ありの場合（Q4がA/B/Cの場合）:**
```bash
mkdir -p "{VAULT_PATH}/07_X-Captures"
```

**用途に応じて追加（Q2の回答による）:**
- B（仕事・案件管理）→ `02_Projects/active` `02_Projects/archive`
- C（学習・読書）→ `04_Resources/books` `04_Resources/articles`
- D（ブログ・SNS）→ `03_Areas/blog` `03_Areas/sns`

---

### STEP 3: テンプレートファイルを自動生成する

以下のテンプレートファイルを `06_Templates/` フォルダに作成する。

#### デイリーノートテンプレート
ファイル名: `06_Templates/daily-note.md`
```markdown
---
date: {{date:YYYY-MM-DD}}
day: {{date:dddd}}
tags: [daily]
---

## 今日のゴール
- [ ] 

## メモ・気づき

## 今日やったこと
- 

## 明日へのタスク
- [ ] 

## 今日の一言
```

#### アイデアキャプチャテンプレート
ファイル名: `06_Templates/idea-capture.md`
```markdown
---
created: {{date:YYYY-MM-DD HH:mm}}
tags: [idea, inbox]
status: raw
---

## アイデア

## なぜ面白いと思ったか

## 次のアクション
- [ ] 
```

#### プロジェクトテンプレート（用途Bの場合）
ファイル名: `06_Templates/project.md`
```markdown
---
created: {{date:YYYY-MM-DD}}
status: active
tags: [project]
---

## 概要

## ゴール

## タスク
- [ ] 

## メモ

## 完了日
```

#### X投稿ネタテンプレート（X連携ありの場合）
ファイル名: `06_Templates/x-post-draft.md`
```markdown
---
created: {{date:YYYY-MM-DD HH:mm}}
tags: [x-post, draft]
status: draft
---

## 伝えたいこと（1行で）

## 下書き（280字以内）

## 関連メモ
```

#### X保存テンプレート（X連携ありの場合）
ファイル名: `06_Templates/x-capture.md`
```markdown
---
captured: {{date:YYYY-MM-DD HH:mm}}
source: X（旧Twitter）
author: 
tags: [x-capture]
---

## ポスト内容

## 自分のメモ・感想

## 関連リンク
```

---

### STEP 4: セットアップ完了メッセージを出力する

作成したフォルダ・ファイルの一覧を表示し、以下を案内する。

**【完了】自動セットアップが終わりました**

作成したもの:
- フォルダ: （作成したフォルダ一覧）
- テンプレート: （作成したファイル一覧）

**次にやること（手動・5分）:**

1. **Templaterプラグインをインストールする**
   - Obsidian設定 → コミュニティプラグイン → 閲覧 → 「Templater」で検索 → インストール → 有効化
   - Templater設定 → Template folder location → `06_Templates` を指定

2. **Daily Notesを設定する**
   - Obsidian設定 → コアプラグイン → Daily notes → 有効化
   - New file location: `01_Daily`
   - Template file: `06_Templates/daily-note`

3. **Dataviewプラグインをインストールする**（オプション）
   - コミュニティプラグイン → 「Dataview」で検索 → インストール → 有効化

---

### STEP 5: X連携の設定方法を出力する（X連携希望の場合のみ）

#### パターンA: 手動キャプチャ（今すぐ使える）

気になったXのポストを見たら:
1. ポストのURLをコピー
2. Obsidianで `07_X-Captures/` に新規ノート作成（`x-capture` テンプレートを適用）
3. ポスト内容と一言感想を書く

#### パターンB: IFTTT自動連携（いいねしたポストを自動保存）

**前提:** ObsidianのVaultをDropboxフォルダ内に置く必要があります。

1. **Dropboxに移動する**
   - Dropboxフォルダ内に `Obsidian/` フォルダを作成
   - 現在のVaultをそこに移動
   - ObsidianでVaultの場所を新しいパスに変更

2. **IFTTTでアプレットを作成する**
   - [ifttt.com](https://ifttt.com) でアカウント作成（無料）
   - 「Create」→「If This」→ **X（Twitter）** → **「New liked tweet by you」**
   - 「Then That」→ **Dropbox** → **「Create a text file」**
   - 設定値:
     ```
     File name: {{CreatedAt}}_x-capture
     Content:
     ---
     captured: {{CreatedAt}}
     source: X（旧Twitter）
     author: @{{UserName}}
     tags: [x-capture]
     ---

     ## ポスト内容
     {{Text}}

     ## 元のURL
     {{LinkToTweet}}

     ## 自分のメモ・感想
     ```
     Folder path: `Obsidian/（VaultフォルダName）/07_X-Captures/`

3. 「Continue」→「Finish」で完成

#### パターンC: ObsidianのメモをXポストに変換する

Claude Codeへの指示テンプレート:
```
以下のObsidianメモをXのポスト用に変換してください。
- 280文字以内（日本語）
- ハッシュタグ2個以内
- 体験談・気づきのトーンで

【メモ】
（ここにObsidianのメモを貼り付ける）
```

---

## 続けるための3つのルール

1. **まずInboxに放り込む** — 完璧に分類しようとしない。とにかく書く
2. **週1回だけ整理する** — 毎日やると続かない。週末15分だけ
3. **検索に頼る** — フォルダよりも `Cmd/Ctrl+O` の検索が速い

---

## 使い方

```
/obsidian-brain-setup-ja
```

呼び出すと4問の質問が始まります。Vaultのパスを答えれば、フォルダ作成からテンプレート生成まで自動で完了します。

## 重要なルール

- Vaultパスが存在しない場合は作成前にユーザーに確認する
- 既存のファイル・フォルダを上書き・削除しない
- STEP 2・3の実行前に「以下を作成します。よろしいですか？」と確認を取る
- X連携のIFTTT設定はDropboxへの移行が必要なため、既存VaultがDropboxにない場合は移行が必要な旨を伝える
