<h1 align="center">OpenAI Codex CLI</h1>

<p align="center"><code>npm i -g @openai/codex</code><br />または <code>brew install codex</code></p>

<p align="center"><strong>Codex CLI</strong> は OpenAI が提供するローカルで実行されるコーディングエージェントです。<br />
クラウドベースのエージェントである <strong>Codex Web</strong> を探している場合は <a href="https://chatgpt.com/codex">chatgpt.com/codex</a> をご覧ください。</p>

<p align="center">
  <img src="./.github/codex-cli-splash.png" alt="Codex CLI splash" width="50%" />
  </p>

---

<details>
<summary><strong>目次</strong></summary>

<!-- Begin ToC -->

- [クイックスタート](#クイックスタート)
  - [Codex CLI のインストールと実行](#codex-cli-のインストールと実行)
  - [ChatGPT プランでの Codex 利用](#chatgpt-プランでの-codex-利用)
  - ["ヘッドレス" マシンでの接続](#ヘッドレス-マシンでの接続)
    - [ローカルで認証し、資格情報をコピー](#ローカルで認証し資格情報をコピー)
    - [VPS やリモート経由で接続](#vps-やリモート経由で接続)
  - [従量課金の代替: OpenAI API キーを使用](#従量課金の代替-openai-api-キーを使用)
    - [特定の認証方法を強制する (上級者向け)](#特定の認証方法を強制する-上級者向け)
  - [Codex の自律性レベルを選択](#codex-の自律性レベルを選択)
    - [**1. 読み書き**](#1-読み書き)
    - [**2. 読み取り専用**](#2-読み取り専用)
    - [**3. 高度な設定**](#3-高度な設定)
    - [承認なしで実行できますか?](#承認なしで実行できますか)
    - [`config.toml` での微調整](#configtoml-での微調整)
  - [プロンプト例](#プロンプト例)
- [プロンプトを入力として実行](#プロンプトを入力として実行)
- [オープンソースモデルの利用](#オープンソースモデルの利用)
  - [プラットフォームのサンドボックス化の詳細](#プラットフォームのサンドボックス化の詳細)
- [実験的技術に関する注意](#実験的技術に関する注意)
- [システム要件](#システム要件)
- [CLI リファレンス](#cli-リファレンス)
- [メモリとプロジェクトドキュメント](#メモリとプロジェクトドキュメント)
- [非対話/CI モード](#非対話ci-モード)
- [Model Context Protocol (MCP)](#model-context-protocol-mcp)
- [トレース/詳細ログ](#トレース詳細ログ)
  - [DotSlash](#dotslash)
- [設定](#設定)
- [FAQ](#faq)
- [Zero data retention (ZDR) の利用](#zero-data-retention-zdr-の利用)
- [Codex オープンソース基金](#codex-オープンソース基金)
- [コントリビュート](#コントリビュート)
  - [開発ワークフロー](#開発ワークフロー)
  - [インパクトの高いコード変更を書く](#インパクトの高いコード変更を書く)
  - [プルリクエストを開く](#プルリクエストを開く)
  - [レビュー手順](#レビュー手順)
  - [コミュニティバリュー](#コミュニティバリュー)
  - [ヘルプを得る](#ヘルプを得る)
  - [Contributor license agreement (CLA)](#contributor-license-agreement-cla)
    - [クイックフィックス](#クイックフィックス)
  - [`codex` のリリース](#codex-のリリース)
- [セキュリティ & 責任ある AI](#セキュリティ--責任ある-ai)
- [ライセンス](#ライセンス)

<!-- End ToC -->

</details>

---

## クイックスタート

### Codex CLI のインストールと実行

お好みのパッケージマネージャでグローバルインストール:

```shell
npm install -g @openai/codex  # もしくは `brew install codex`
```

その後 `codex` を実行すると開始できます:

```shell
codex
```

<details>
<summary><a href="https://github.com/openai/codex/releases/latest">最新の GitHub リリース</a>からプラットフォームに合ったバイナリをダウンロードすることもできます。</summary>

各 GitHub リリースには多数の実行ファイルが含まれていますが、実際に必要なのは以下のいずれかです:

- macOS
  - Apple Silicon/arm64: `codex-aarch64-apple-darwin.tar.gz`
  - x86_64 (古い Mac): `codex-x86_64-apple-darwin.tar.gz`
- Linux
  - x86_64: `codex-x86_64-unknown-linux-musl.tar.gz`
  - arm64: `codex-aarch64-unknown-linux-musl.tar.gz`

各アーカイブにはプラットフォーム名が含まれた単一のエントリ (例: `codex-x86_64-unknown-linux-musl`) が入っているため、解凍後に `codex` へリネームするのが一般的です。

</details>

### ChatGPT プランでの Codex 利用

<p align="center">
  <img src="./.github/codex-cli-login.png" alt="Codex CLI login" width="50%" />
  </p>

`codex` を実行し **Sign in with ChatGPT** を選択してください。Plus、Pro、Team のいずれかの ChatGPT アカウントが必要で、追加料金なしで最新モデル (例: `gpt-5`) を利用できます。(Enterprise は近日対応予定)

> 重要: 以前 Codex CLI を API キーによる従量課金で使用していた場合、以下の手順で移行してください:
>
> 1. CLI をアップデートし、`codex --version` が `0.20.0` 以降であることを確認
> 2. `~/.codex/auth.json` を削除 (Windows の場合は `C:\Users\USERNAME\.codex\auth.json`)
> 3. 再度 `codex login` を実行
>
> ログインフローで問題が発生した場合は [この Issue](https://github.com/openai/codex/issues/1243) で報告してください。

### "ヘッドレス" マシンでの接続

現在、ログイン処理では `localhost:1455` でサーバーを起動します。Docker コンテナや VPS などの GUI がない環境で利用する場合の方法は以下の通りです。

#### ローカルで認証し資格情報をコピー

1. GUI 環境のマシンで `codex login` を実行し認証を完了します。
2. 認証ファイル `~/.codex` ディレクトリをヘッドレス環境にコピーします。
3. ヘッドレス環境で `codex` を実行すると認証済みで利用できます。

#### VPS やリモート経由で接続

SSH トンネルなどを利用して `localhost:1455` へ接続し、ブラウザからログインを完了させます。

### 従量課金の代替: OpenAI API キーを使用

ChatGPT プランを使用したくない場合は、OpenAI API キーによる従量課金で Codex CLI を利用できます。

#### 特定の認証方法を強制する (上級者向け)

環境変数や設定で使用する認証方式を固定することも可能です。詳細はドキュメントを参照してください。

### Codex の自律性レベルを選択

Codex CLI は操作許可のレベルを設定できます。用途に応じて選択してください。

#### 1. 読み書き

Codex がファイルの読み書きやコマンド実行を行う際、毎回確認が求められます。

#### 2. 読み取り専用

ファイルの読み込みのみ許可します。書き込みやコマンド実行は行いません。

#### 3. 高度な設定

`config.toml` で個別に許可範囲を細かく指定できます。

#### 承認なしで実行できますか?

設定によってはすべての操作を自動承認できますが、推奨されません。

#### `config.toml` での微調整

個別のファイルやコマンドに対する権限を細かく設定可能です。

### プロンプト例

いくつかの例を用意しています。複雑な操作は細かい手順を指定することでより正確に実行できます。

## プロンプトを入力として実行

対話的にではなく、1 回のプロンプトで Codex CLI を実行することもできます。`codex -p "プロンプト内容"` のように指定します。

## オープンソースモデルの利用

Codex CLI は OpenAI 以外のオープンソースモデルにも対応しています。モデルのセットアップ方法や互換性についてはドキュメントを参照してください。

### プラットフォームのサンドボックス化の詳細

CLI は安全性のためサンドボックス環境下で動作します。実行時にはネットワークアクセスが制限される場合があります。

## 実験的技術に関する注意

Codex CLI は実験的なソフトウェアであり、予期せぬ動作をする可能性があります。重要な環境での利用は慎重に行ってください。

## システム要件

- Node.js 22 以上
- 対応 OS: macOS、Linux
- ネットワーク接続

## CLI リファレンス

`codex --help` で利用可能なコマンド一覧やオプションを確認できます。

## メモリとプロジェクトドキュメント

Codex はプロジェクト内のドキュメントを参照し、作業履歴を保存する機能を持ちます。詳細は `docs/` ディレクトリを参照してください。

## 非対話/CI モード

CI 環境などで対話を行わずにコマンドを実行するモードを提供しています。`--ci` オプションを使用してください。

## Model Context Protocol (MCP)

Codex CLI は MCP をサポートしており、外部ツールとの連携が容易です。

## トレース/詳細ログ

デバッグのため詳細なログを取得できます。`--verbose` フラグを使用してください。

### DotSlash

外部コマンドを安全に実行するための仕組みである DotSlash に対応しています。

## 設定

詳細な設定は `~/.codex/config.toml` を編集するか、コマンドラインオプションで指定します。

## FAQ

よくある質問と回答をまとめています。問題が解決しない場合は Issue を作成してください。

## Zero data retention (ZDR) の利用

ZDR モードでは、OpenAI のサーバーにデータが保存されません。設定方法はドキュメントを参照してください。

## Codex オープンソース基金

オープンソースコミュニティを支援するための基金です。寄付の方法や利用例を紹介しています。

## コントリビュート

このプロジェクトは活発に開発中であり、今後大きく変更される可能性があります。

**現在、外部からのバグ修正やセキュリティ修正を優先してレビューしています。**

新機能の提案や既存機能の変更を行いたい場合は、まず Issue を作成し、OpenAI チームメンバーの承認を得てから取り組んでください。

承認を得ずに行われた新しい貢献は、現在のロードマップと一致しない場合や他の優先事項/今後の機能と競合する場合はクローズされることがあります。

### 開発ワークフロー

- `main` からトピックブランチを作成します (例: `feat/interactive-prompt`)
- 変更点は焦点を絞り、関連しない修正は別 PR とします
- lint 警告やテスト失敗がないことを確認します

### インパクトの高いコード変更を書く

1. **Issue から始める:** 新規 Issue を立てるか既存の議論にコメントし、解決策に合意してからコードを書きます。
2. **テストの追加/更新:** 新機能やバグ修正にはテストを追加し、変更前は失敗、変更後は成功することを確認します。
3. **挙動を文書化:** ユーザーに影響する変更は README や `codex --help`、関連プロジェクトの例を更新してください。
4. **コミットは原子的に:** 各コミットはコンパイルでき、テストが通る状態であるべきです。

### プルリクエストを開く

- PR テンプレートを埋めるか同等の情報 (What? Why? How?) を含める
- すべてのチェックをローカルで実行 (`cargo test && cargo clippy --tests && cargo fmt -- --config imports_granularity=Item`)
- `main` と最新に同期し、マージコンフリクトを解消しておく
- マージ可能な状態になったら **Ready for review** に設定

### レビュー手順

1. メンテナーがメインレビュアーとして割り当てられます
2. 事前に議論・承認されていない新機能を追加する PR は閉じられる可能性があります
3. 変更依頼が来ることがあります。個人的なことではありません。私たちは一貫性と長期的な保守性を重視します
4. PR が基準を満たしたら、メンテナーが squash-and-merge を行います

### コミュニティバリュー

- **親切で包括的に:** 他者を尊重し、[Contributor Covenant](https://www.contributor-covenant.org/) に従います。
- **善意を前提に:** 文章でのコミュニケーションは難しいため、寛容さを心がけます。
- **教え合い学び合う:** 混乱を招く点を見つけたら Issue や PR で改善を提案してください。

### ヘルプを得る

セットアップで問題が生じた場合やアイデアのフィードバックが欲しいときは、Discussion を開くか関連 Issue に参加してください。喜んでお手伝いします。

一緒に Codex CLI を素晴らしいツールにしましょう。**Happy hacking!** :rocket:

### Contributor license agreement (CLA)

すべてのコントリビューターは CLA に同意する必要があります。手順は次のとおりです:

1. プルリクエストを開く
2. 次のコメントを投稿 (以前署名済みの場合は `recheck` と返信)

   ```text
   I have read the CLA Document and I hereby sign the CLA
   ```

3. CLA-Assistant ボットが署名を記録し、ステータスチェックが通過します。

特別な Git コマンドやメール添付、コミットフッターは不要です。

#### クイックフィックス

| シナリオ             | コマンド                                         |
| -------------------- | ------------------------------------------------ |
| 最後のコミットを修正 | `git commit --amend -s --no-edit && git push -f` |

**DCO チェック** は PR 内のすべてのコミットにフッターが付いているまでマージをブロックします (squash の場合は 1 つだけ)。

### `codex` のリリース

_管理者のみ_

`main` にいてローカル変更がないことを確認してから次を実行します:

```shell
VERSION=0.2.0  # 例: 0.2.0-alpha.1 など有効な Rust バージョン
./codex-rs/scripts/create_github_release.sh "$VERSION"
```

これは `codex-rs/Cargo.toml` の `version` を `$VERSION` に設定したコミットを `main` の上に作成します ( `main` 上では `version = "0.0.0"` のまま)。

このコミットは `rust-v${VERSION}` タグでプッシュされ、[リリースワークフロー](.github/workflows/rust-release.yml) を起動します。これにより `$VERSION` という名前の GitHub Release が作成されます。

生成された GitHub Release が問題ない場合は **pre-release** のチェックを外し、最新リリースにします。

Homebrew の [`Formula/c/codex.rb`](https://github.com/Homebrew/homebrew-core/blob/main/Formula/c/codex.rb) を更新する PR を作成します。

---

## セキュリティ & 責任ある AI

脆弱性を発見したりモデル出力について懸念がある場合は、**security@openai.com** へメールしてください。迅速に対応します。

---

## ライセンス

このリポジトリは [Apache-2.0 License](LICENSE) の下でライセンスされています。
