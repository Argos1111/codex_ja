<h1 align="center">OpenAI Codex CLI</h1>
<p align="center">ターミナルで動作する軽量なコーディングエージェント</p>

<p align="center"><code>npm i -g @openai/codex</code></p>

> [!IMPORTANT]
> これはCodex CLIの旧式TypeScript実装のドキュメントです。現在はRust実装に置き換えられています。詳細は[CodexリポジトリのルートのREADME](https://github.com/openai/codex/blob/main/README.md)を参照してください。

![Codex demo GIF using: codex "explain this codebase to me"](../.github/demo.gif)

---

<details>
<summary><strong>目次</strong></summary>

<!-- Begin ToC -->

- [実験的技術に関する免責事項](#experimental-technology-disclaimer)
- [クイックスタート](#quickstart)
- [Codexを選ぶ理由](#why-codex)
- [セキュリティモデルと権限](#security-model--permissions)
  - [プラットフォームのサンドボックス詳細](#platform-sandboxing-details)
- [システム要件](#system-requirements)
- [CLIリファレンス](#cli-reference)
- [メモリとプロジェクトドキュメント](#memory--project-docs)
- [非対話/CIモード](#non-interactive--ci-mode)
- [トレース/詳細ログ](#tracing--verbose-logging)
- [レシピ](#recipes)
- [インストール](#installation)
- [設定ガイド](#configuration-guide)
  - [基本設定パラメータ](#basic-configuration-parameters)
  - [カスタムAIプロバイダの設定](#custom-ai-provider-configuration)
  - [履歴設定](#history-configuration)
  - [設定例](#configuration-examples)
  - [完全な設定例](#full-configuration-example)
  - [カスタム指示](#custom-instructions)
  - [環境変数の設定](#environment-variables-setup)
- [FAQ](#faq)
- [ゼロデータ保持(ZDR)の利用](#zero-data-retention-zdr-usage)
- [Codexオープンソース基金](#codex-open-source-fund)
- [貢献](#contributing)
  - [開発ワークフロー](#development-workflow)
  - [HuskyによるGitフック](#git-hooks-with-husky)
  - [デバッグ](#debugging)
  - [高インパクトなコード変更を書く](#writing-high-impact-code-changes)
  - [プルリクエストを開く](#opening-a-pull-request)
  - [レビューの流れ](#review-process)
  - [コミュニティの価値観](#community-values)
  - [ヘルプを得る](#getting-help)
  - [貢献者ライセンス契約(CLA)](#contributor-license-agreement-cla)
    - [Quick fixes](#quick-fixes)
  - [`codex`のリリース](#releasing-codex)
  - [代替のビルドオプション](#alternative-build-options)
    - [Nixフレーク開発](#nix-flake-development)
- [セキュリティと責任あるAI](#security--responsible-ai)
- [ライセンス](#license)

<!-- End ToC -->

</details>

---

## 実験的技術に関する免責事項 {#experimental-technology-disclaimer}

Codex CLIは開発中の実験的プロジェクトです。まだ安定しておらず、バグや未完成の機能、破壊的な変更が含まれる可能性があります。コミュニティと共に公開で開発しており、以下を歓迎します:

- バグ報告
- 機能リクエスト
- プルリクエスト
- 良い雰囲気

問題報告やPRを通じて改善に協力してください（貢献方法は後述）。

## クイックスタート {#quickstart}

グローバルにインストール:

```shell
npm install -g @openai/codex
```

次にOpenAI APIキーを環境変数として設定します:

```shell
export OPENAI_API_KEY="your-api-key-here"
```

> **注:** このコマンドは現在のターミナルセッションでのみキーを設定します。シェルの設定ファイル（例: `~/.zshrc`）に`export`行を追加できますが、セッションごとの設定を推奨します。**ヒント:** プロジェクトのルートに`.env`ファイルを置くとさらに便利です:
>
> ```env
> OPENAI_API_KEY=your-api-key-here
> ```
>
> CLIは`dotenv/config`経由で`.env`から環境変数を自動的に読み込みます。

<details>
<summary><strong><code>--provider</code>で他のモデルを使用</strong></summary>

> CodexはOpenAI Chat Completions APIと互換性のある他のプロバイダーも使用できます。設定ファイルで指定するか、`--provider`フラグを使用します。利用可能な`--provider`の値:
>
> - openai (デフォルト)
> - openrouter
> - azure
> - gemini
> - ollama
> - mistral
> - deepseek
> - xai
> - groq
> - arceeai
> - その他OpenAI API互換プロバイダー
>
> OpenAI以外を利用する場合は、設定ファイルまたは環境変数でそのプロバイダーのAPIキーを設定する必要があります:
>
> ```shell
> # 例: export OPENROUTER_API_KEY="..."
> ```
>
> </details>

## Codexを選ぶ理由 {#why-codex}

Codexはターミナルから直接コードベースに対する質問や操作を行える、軽量で拡張性のあるエージェントです。ローカルファイルにアクセスし、プロジェクト専用のドキュメントを保持しながら、迅速に反復作業を行えます。

## セキュリティモデルと権限 {#security-model--permissions}

Codexは安全性を重視して設計されています。実行時には権限を最小限に抑え、外部ネットワークへのアクセスを制限するサンドボックス環境で動作します。

### プラットフォームのサンドボックス詳細 {#platform-sandboxing-details}

各プラットフォームでのサンドボックスの仕組みや制限事項の詳細については、公式ドキュメントを参照してください。

## システム要件 {#system-requirements}

- Node.js 20以上
- UNIX互換シェル (Linux, macOS)
- ネットワーク接続

## CLIリファレンス {#cli-reference}

`codex --help`で利用可能なコマンドやフラグの一覧を確認できます。

## メモリとプロジェクトドキュメント {#memory--project-docs}

Codexは作業ディレクトリ内にプロジェクト固有のメモリを保持し、過去のやり取りやドキュメントを参照できます。

## 非対話/CIモード {#non-interactive--ci-mode}

`--non-interactive`フラグを使用するとCI環境で自動的に実行できます。

## トレース/詳細ログ {#tracing--verbose-logging}

`--verbose`フラグや`CODEX_TRACE`環境変数を使うと、詳細なトレースログを取得できます。

## レシピ {#recipes}

典型的な利用方法の例は`recipes/`ディレクトリやドキュメントを参照してください。

## インストール {#installation}

上記の`npm install`に加え、必要に応じて`pnpm`や`nix`を利用したインストール方法もあります。

## 設定ガイド {#configuration-guide}

Codexは`codex.config.json`や`package.json`内の`codex`キーで設定できます。

### 基本設定パラメータ {#basic-configuration-parameters}

APIキーやプロバイダー、デフォルトモデルなどを指定します。

### カスタムAIプロバイダの設定 {#custom-ai-provider-configuration}

独自のエンドポイントや追加ヘッダーを指定して、OpenAI互換のサービスを利用できます。

### 履歴設定 {#history-configuration}

対話履歴の保持場所や最大件数を設定します。

### 設定例 {#configuration-examples}

一般的な設定パターンをいくつか示します。

### 完全な設定例 {#full-configuration-example}

全てのパラメータを含む設定ファイルの例です。

### カスタム指示 {#custom-instructions}

エージェントの振る舞いを変更する追加のシステムプロンプトを提供できます。

### 環境変数の設定 {#environment-variables-setup}

環境変数で設定を上書きすることもできます。

## FAQ {#faq}

よくある質問とその回答をまとめています。

## ゼロデータ保持(ZDR)の利用 {#zero-data-retention-zdr-usage}

`X-OpenAI-ZD`ヘッダーを設定することで、リクエストデータを保持しないモードを有効にできます。

## Codexオープンソース基金 {#codex-open-source-fund}

Codexはオープンソースコミュニティを支援するための基金を運営しています。詳細は公式サイトを参照してください。

## 貢献 {#contributing}

Codexへの貢献を歓迎します。以下のガイドラインに従ってください。

### 開発ワークフロー {#development-workflow}

リポジトリをフォークし、ブランチを作成して変更を加え、テストを実行してからPRを送ってください。

### HuskyによるGitフック {#git-hooks-with-husky}

コミット時に自動でフォーマットやリンターを実行します。

### デバッグ {#debugging}

`DEBUG`環境変数を設定して詳細なログを取得できます。

### 高インパクトなコード変更を書く {#writing-high-impact-code-changes}

変更の目的や影響を明確にし、レビューしやすいPRを作成してください。

### プルリクエストを開く {#opening-a-pull-request}

PRテンプレートに従い、変更内容とテスト結果を記述します。

### レビューの流れ {#review-process}

レビューでは品質、テスト、ドキュメントを重視します。

### コミュニティの価値観 {#community-values}

- 親切で包括的であること
- 善意を前提にすること
- 学び合い、教え合うこと

### ヘルプを得る {#getting-help}

問題があればDiscussionやIssueで質問してください。一緒に素晴らしいCLIを作りましょう。**Happy hacking!** :rocket:

### 貢献者ライセンス契約(CLA) {#contributor-license-agreement-cla}

全ての貢献者はCLAへの同意が必要です。

1. PRを作成
2. 次のコメントを投稿（以前に署名していれば`recheck`と返信）:

   ```text
   I have read the CLA Document and I hereby sign the CLA
   ```

3. CLA-Assistantボットが署名を記録し、ステータスチェックが合格します。

特別なGitコマンドやメールは不要です。

#### Quick fixes {#quick-fixes}

| シナリオ | コマンド |
| --- | --- |
| 直前のコミットを修正 | `git commit --amend -s --no-edit && git push -f` |

DCOチェックは全てのコミットに署名が含まれるまでマージをブロックします。

### `codex`のリリース {#releasing-codex}

新しいバージョンを公開するには、まずnpmパッケージをステージングします。`codex-cli/scripts/`内のヘルパースクリプトがサポートします。`codex-cli`フォルダで次を実行します:

```bash
pnpm stage-release

# 一時ディレクトリを指定する場合
RELEASE_DIR=$(mktemp -d)
pnpm stage-release --tmp "$RELEASE_DIR"

# Rust CLIバイナリを含む「fat」パッケージ
pnpm stage-release --native
```

ステージングされたフォルダで動作を確認し、問題なければ次を実行します:

```bash
cd "$RELEASE_DIR"
npm publish
```

### 代替のビルドオプション {#alternative-build-options}

#### Nixフレーク開発 {#nix-flake-development}

前提: Nix >= 2.4 で flakes を有効化。

開発シェルに入る:

```bash
nix develop .#codex-cli # codex-cli用
nix develop .#codex-rs # codex-rs用
```

このシェルはNode.jsを含み、依存関係をインストールし、CLIをビルドして`codex`コマンドを提供します。

直接ビルドして実行:

```bash
nix build .#codex-cli
nix build .#codex-rs
./result/bin/codex --help
```

フレークアプリ経由で実行:

```bash
nix run .#codex-cli
nix run .#codex-rs
```

flakesとdirenvの併用:

```bash
cd codex-rs
echo "use flake ../flake.nix#codex-cli" >> .envrc && direnv allow
cd codex-cli
echo "use flake ../flake.nix#codex-rs" >> .envrc && direnv allow
```

---

## セキュリティと責任あるAI {#security--responsible-ai}

脆弱性を発見した場合やモデル出力に懸念がある場合は、**security@openai.com** までご連絡ください。

---

## ライセンス {#license}

このリポジトリは[Apache-2.0 License](LICENSE)の下で提供されています。

