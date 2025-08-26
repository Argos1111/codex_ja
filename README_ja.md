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
      - [ローカルで認証し、資格情報を「ヘッドレス」マシンにコピー](#ローカルで認証し資格情報をヘッドレスマシンにコピー)
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
  - [セキュリティと責任ある AI](#セキュリティと責任ある-ai)
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

現在のログインプロセスでは `localhost:1455` でサーバーを起動します。Docker コンテナやリモートマシンに `ssh` 接続しているなど、ブラウザが使えない「ヘッドレス」な環境では、ローカルマシンのブラウザで `localhost:1455` を開いても _ヘッドレス_ マシン上で動いているウェブサーバーには自動で接続されません。そのため、以下のいずれかの方法を利用してください。

#### ローカルで認証し、資格情報を「ヘッドレス」マシンにコピー

最も簡単な方法は、ローカルマシンで `codex login` を実行し、ブラウザから `localhost:1455` にアクセスできる状態で認証フローを完了させることです。認証が終わると、`auth.json` ファイルが `$CODEX_HOME/auth.json` に作成されます（Mac/Linux では `$CODEX_HOME` は `~/.codex`、Windows では `%USERPROFILE%\.codex` が既定値です）。

`auth.json` ファイルは特定のホストに紐づいていないため、ローカルで認証フローを済ませたら、その `$CODEX_HOME/auth.json` をヘッドレス環境にコピーすれば、その環境でも `codex` がそのまま利用できます。Docker コンテナにファイルをコピーする場合は次のようにします。

```shell
# substitute MY_CONTAINER with the name or id of your Docker container:
CONTAINER_HOME=$(docker exec MY_CONTAINER printenv HOME)
docker exec MY_CONTAINER mkdir -p "$CONTAINER_HOME/.codex"
docker cp auth.json MY_CONTAINER:"$CONTAINER_HOME/.codex/auth.json"
```

リモートマシンに `ssh` 接続している場合は、[`scp`](https://en.wikipedia.org/wiki/Secure_copy_protocol) を使うとよいでしょう。

```shell
ssh user@remote 'mkdir -p ~/.codex'
scp ~/.codex/auth.json user@remote:~/.codex/auth.json
```

または次のワンライナーを試してみてください。

```shell
ssh user@remote 'mkdir -p ~/.codex && cat > ~/.codex/auth.json' < ~/.codex/auth.json
```

#### VPS やリモート経由で接続

ローカルにブラウザがないリモートマシン（VPS/サーバー）で Codex を実行する場合、ログインヘルパーはリモートホスト上の `localhost:1455` でサーバーを起動します。ローカルのブラウザでログインを完了するには、ログインフローを開始する前にそのポートを自分のマシンへ転送してください。

```bash
# ローカルマシンから実行
ssh -L 1455:localhost:1455 <user>@<remote-host>
```

その SSH セッション内で `codex` を実行し、「Sign in with ChatGPT」を選択します。表示された URL（`http://localhost:1455/...` になります）をローカルブラウザで開くと、通信がリモートサーバーへトンネルされます。

### 従量課金の代替: OpenAI API キーを使用

従量課金で利用したい場合は、環境変数として OpenAI API キーを設定し、API キーによる認証を行うこともできます。

```shell
export OPENAI_API_KEY="your-api-key-here"
```

補足:

- このコマンドは現在のターミナルセッションだけにキーを設定します（推奨）。今後のすべてのセッションでも利用したい場合は、`export` 行をシェルの設定ファイル（例: `~/.zshrc`）に追加してください。
- 既に ChatGPT でサインインしている場合、Codex はデフォルトで ChatGPT のクレジットを使用します。API キーを使いたい場合は、`/logout` コマンドで ChatGPT の認証情報をクリアしてください。

#### 特定の認証方法を強制する (上級者向け)

両方の方法が利用可能な場合に、Codex がどちらの認証方式を優先するかを明示的に選択することもできます。

- 常に API キーを使用したい場合（ChatGPT 認証が存在する場合でも）は、次のように設定します。

```toml
# ~/.codex/config.toml
preferred_auth_method = "apikey"
```

CLI から一時的に上書きすることもできます。

```bash
codex --config preferred_auth_method="apikey"
```

- ChatGPT 認証を優先したい場合（デフォルト） は、次のように設定します。

```toml
# ~/.codex/config.toml
preferred_auth_method = "chatgpt"
```

補足:

- `preferred_auth_method = "apikey"` で API キーが存在する場合、ログイン画面は表示されません。
- `preferred_auth_method = "chatgpt"`（デフォルト）の場合、ChatGPT の認証があればそれを優先し、API キーしかない場合は API キーを使用します。アカウントの種類によっては API キーモードが必須の場合もあります。

### Codex の自律性レベルを選択

Codex の実行には、エージェントの行動を厳しく制限するデフォルトのサンドボックスでの利用を常に推奨します。デフォルトのサンドボックスでは、ワークスペース外のファイル編集やネットワークアクセスが禁止されています。

新しいフォルダで Codex を起動すると、そのフォルダがバージョン管理されているかどうかを検出し、次の 2 つの自律レベルのいずれかを提案します。

#### **1. 読み書き**

- Codex は、許可を求めずにワークスペース内でコマンドを実行し、ファイルを書き込むことができます。
- 他のフォルダへの書き込みやネットワークアクセス、git の更新など、サンドボックスで保護されている操作を行う場合は、ユーザーの許可が必要です。
- 既定では、ワークスペースには現在のディレクトリに加え、`/tmp` などの一時ディレクトリも含まれます。`/status` コマンドでワークスペースに含まれるディレクトリを確認できます。この挙動のカスタマイズ方法はドキュメントを参照してください。
- 上級者向け: `codex --sandbox workspace-write --ask-for-approval on-request` を実行すると、この構成を手動で指定できます。
- バージョン管理されているフォルダでは、このモードが推奨デフォルトです。

#### **2. 読み取り専用**

- Codex は、許可を求めずに読み取り専用のコマンドを実行できます。
- ファイルの編集やネットワークアクセスなど、サンドボックスで保護されている操作を行うには、ユーザーの許可が必要です。
- 上級者向け: `codex --sandbox read-only --ask-for-approval on-request` を実行すると、この構成を手動で指定できます。
- バージョン管理されていないフォルダでは、このモードが推奨デフォルトです。

#### **3. 高度な設定**

`--sandbox` オプションでサンドボックスを細かく制御でき、`--ask-for-approval` オプションで許可を求めるタイミングを調整できます。詳細は `codex help` を参照してください。

#### 承認なしで実行できますか?

はい。`--ask-for-approval never` を付けて非対話モードで実行すれば、一切の承認なしで動作させることができます。このオプションはすべての `--sandbox` オプションと併用できるため、自律性のレベルは完全に制御できます。指定した制約の範囲で可能な限り最善の試行が行われます。例:

- 同じワークスペースで多数のエージェントを並行実行し質問に回答させる場合は、`codex --ask-for-approval never --sandbox read-only` を使用します。
- エージェントに非対話で時間をかけて最良の結果を出させたいが、挙動には厳しいガードレールを維持したい場合は、`codex --ask-for-approval never --sandbox workspace-write` を使用します。
- エージェントに完全な自律性を危険な形で与えたい場合は、`codex --ask-for-approval never --sandbox danger-full-access` を使用します。これは重要な安全機構を無効化するため、隔離された環境で Codex を実行する場合を除き推奨しません。

#### `config.toml` での微調整

```toml
# approval mode
approval_policy = "untrusted"
sandbox_mode    = "read-only"

# full-auto mode
approval_policy = "on-request"
sandbox_mode    = "workspace-write"

# Optional: allow network in workspace-write mode
[sandbox_workspace_write]
network_access = true
```

プリセットを **プロファイル** として保存することもできます。

```toml
[profiles.full_auto]
approval_policy = "on-request"
sandbox_mode    = "workspace-write"

[profiles.readonly_quiet]
approval_policy = "never"
sandbox_mode    = "read-only"
```

### プロンプト例

以下はいくつかのコピペできる簡単な例です。引用符内のテキストを自分のタスクに置き換えてください。[プロンプトガイド](https://github.com/openai/codex/blob/main/codex-cli/examples/prompting_guide.md)も参照してください。

| ✨  | あなたが入力する内容                                                              | 起こること                                                                               |
| --- | --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| 1   | `codex "Dashboard コンポーネントを React Hooks にリファクタリングして"`           | Codex がクラスコンポーネントを書き直し、`npm test` を実行して差分を表示します。          |
| 2   | `codex "users テーブルを追加する SQL マイグレーションを生成して"`                 | ORM を推測し、マイグレーションファイルを作成してサンドボックス化された DB で実行します。 |
| 3   | `codex "utils/date.ts の単体テストを書いて"`                                      | テストを生成・実行し、成功するまで繰り返します。                                         |
| 4   | `codex "git mv で *.jpeg を *.jpg に一括リネームして"`                            | ファイルを安全にリネームし、インポートや使用箇所を更新します。                           |
| 5   | `codex "この正規表現 ^(?=.*[A-Z]).{8,}$ が何をするか説明して"`                    | 手順を追った人間向けの説明を出力します。                                                 |
| 6   | `codex "このリポジトリを丁寧にレビューし、影響の大きい適切な PR を 3 つ提案して"` | 現在のコードベースでインパクトのある PR を提案します。                                   |
| 7   | `codex "脆弱性を探し、セキュリティレビュー報告書を作成して"`                      | セキュリティバグを見つけて説明します。                                                   |

## プロンプトを入力として実行

Codex CLI は、プロンプトを直接入力して実行することもできます。

```shell
codex "explain this codebase to me"
```

```shell
codex --full-auto "create the fanciest todo-list app"
```

これだけです。Codex がファイルを自動生成し、サンドボックス内で実行し、足りない依存関係をインストールして、結果をライブで表示します。変更を承認すると、作業ディレクトリにコミットされます。

## オープンソースモデルの利用

<details>
<summary><strong>他のモデルを利用するには <code>--profile</code> を使用</strong></summary>

Codex は、OpenAI Chat Completions（または Responses）API をサポートする他のプロバイダーも利用できます。

そのためには、まず `~/.codex/config.toml` にカスタム [プロバイダー](./config.md#model_providers) を定義します。たとえば、一般的な Ollama の設定は次のようになります。

```toml
[model_providers.ollama]
name = "Ollama"
base_url = "http://localhost:11434/v1"
```

`base_url` にはリクエストの完全な URL を構築するために `/chat/completions` が付加されます。

`Authorization: Bearer SECRET` 形式のヘッダーが必要なプロバイダーでは、`env_key` を指定して、リクエスト時に `SECRET` として使用する環境変数を指示できます。

```toml
[model_providers.openrouter]
name = "OpenRouter"
base_url = "https://openrouter.ai/api/v1"
env_key = "OPENROUTER_API_KEY"
```

Responses API を利用するプロバイダーも、定義に `wire_api = "responses"` を追加することでサポートされます。Azure 経由で OpenAI モデルにアクセスする場合がその例で、さらにリクエスト URL に追加する `query_params` を指定する必要があります。

```toml
[model_providers.azure]
name = "Azure"
# この URL には適切なサブドメインを設定してください。
base_url = "https://YOUR_PROJECT_NAME.openai.azure.com/openai"
env_key = "AZURE_OPENAI_API_KEY"  # または "OPENAI_API_KEY"、使用する方を指定。
# 新しいバージョンは responses API をサポートしているようです。https://github.com/openai/codex/pull/1321 を参照してください。
query_params = { api-version = "2025-04-01-preview" }
wire_api = "responses"
```

使用したいプロバイダーを定義したら、次のようにデフォルトのプロバイダーとして設定できます。

```toml
model_provider = "azure"
```

> [!TIP]
> さまざまなモデルやプロバイダーを試す場合は、次のように各構成に _プロファイル_ を定義しておくと便利です。

```toml
[profiles.o3]
model_provider = "azure"
model = "o3"

[profiles.mistral]
model_provider = "ollama"
model = "mistral"
```

これにより、`--profile o3` や `--profile mistral` といった 1 つのコマンドライン引数で複数の設定をまとめて上書きできます。

</details>

Codex は `--oss` フラグを付けることで、Ollama のような OpenAI 互換の OSS ホストに対して完全にローカルで実行できます。

- インタラクティブ UI:
  - codex --oss
- 非対話（プログラム）モード:
  - echo "Refactor utils" | codex exec --oss

`--oss` 利用時のモデル選択:

- `-m/--model` を省略すると、Codex はデフォルトで `-m gpt-oss:20b` を使用し、ローカルに存在するか確認します（必要に応じてダウンロードします）。
- 別のサイズを選択するには以下のいずれかを指定します。
  - -m "gpt-oss:20b"
  - -m "gpt-oss:120b"

自身の OSS ホストを Codex に指定する:

- 既定では `--oss` は http://localhost:11434/v1 に接続します。
- 別のホストを使う場合は、Codex を実行する前に以下のいずれかの環境変数を設定します。
  - CODEX_OSS_BASE_URL（例）:
    - CODEX_OSS_BASE_URL="http://my-ollama.example.com:11434/v1" codex --oss -m gpt-oss:20b
  - またはホストが localhost の場合は CODEX_OSS_PORT:
    - CODEX_OSS_PORT=11434 codex --oss

上級者向け: 環境変数の代わりに、`~/.codex/config.toml` で組み込みの `oss` プロバイダーを上書きして設定を永続化できます。

```toml
[model_providers.oss]
name = "Open Source"
base_url = "http://my-ollama.example.com:11434/v1"
```

---

### プラットフォームのサンドボックス化の詳細

デフォルトで Codex CLI は、システムを保護するために制限されたサンドボックス内でコードやシェルコマンドを実行します。

> [!IMPORTANT]
> すべてのツール呼び出しがサンドボックス化されるわけではありません。具体的には、**信頼された Model Context Protocol (MCP) ツールの呼び出し** はサンドボックスの外で実行されます。
> これは意図的なものです。MCP ツールはユーザーによって明示的に設定・信頼されており、しばしば問題追跡システムやデータベース、メッセージングシステムなどの**外部アプリケーションやサービス**に接続する必要があります。
> サンドボックス外で実行することで、Codex がサンドボックスの制限に阻まれることなくこれら外部システムと統合できるようにしています。

Codex がサンドボックスポリシーを実装する仕組みは OS によって異なります。

- **macOS 12+** では **Apple Seatbelt** を使用し、指定された `--sandbox` に対応するプロファイル (`-p`) を用いて `sandbox-exec` でコマンドを実行します。
- **Linux** では Landlock/seccomp API の組み合わせを用いて `sandbox` 設定を強制します。

Docker のようなコンテナ化された環境で Linux を実行している場合、ホスト／コンテナの設定が必要な Landlock/seccomp API をサポートしていないとサンドボックスが機能しないことがあります。その場合は、望むサンドボックスの保証を提供するよう Docker コンテナを構成し、コンテナ内で `codex` を `--sandbox danger-full-access`（またはより単純に `--dangerously-bypass-approvals-and-sandbox` フラグ）付きで実行することを推奨します。

---

## 実験的技術に関する注意

Codex CLI は積極的に開発が進められている実験的なプロジェクトです。まだ安定しておらず、バグや未完成の機能を含んでいたり、破壊的な変更が行われる可能性があります。私たちはコミュニティと共にオープンに開発を進めており、次のようなものを歓迎します。

- バグ報告
- 機能リクエスト
- プルリクエスト
- ポジティブな雰囲気

Issue の提出や PR の送付（貢献方法は後述）で改善にご協力ください。

---

## システム要件

| 要件                     | 詳細                                                                    |
| ------------------------ | ----------------------------------------------------------------------- |
| オペレーティングシステム | macOS 12+、Ubuntu 20.04+/Debian 10+、または Windows 11（**WSL2 経由**） |
| Git（任意だが推奨）      | 組み込み PR ヘルパーを利用するには 2.23 以上                            |
| RAM                      | 最低 4 GB（推奨 8 GB）                                                  |

---

## CLI リファレンス

| コマンド           | 目的                        | 例                              |
| ------------------ | --------------------------- | ------------------------------- |
| `codex`            | 対話型 TUI                  | `codex`                         |
| `codex "..."`      | 対話型 TUI の初期プロンプト | `codex "fix lint errors"`       |
| `codex exec "..."` | 非対話の「自動化モード」    | `codex exec "explain utils.ts"` |

主なフラグ: `--model/-m`, `--ask-for-approval/-a`.

---

## メモリとプロジェクトドキュメント

`AGENTS.md` ファイルを使って Codex に追加の指示やガイダンスを与えることができます。Codex は次の場所で `AGENTS.md` を探し、上から順にマージします。

1. `~/.codex/AGENTS.md` — 個人向けのグローバルガイダンス
2. リポジトリルートの `AGENTS.md` — プロジェクト全体の共有ノート
3. 現在の作業ディレクトリ内の `AGENTS.md` — サブフォルダ／機能固有の指示

---

## 非対話/CI モード

パイプラインで Codex をヘッドレス実行できます。GitHub Actions の例:

```yaml
- name: Update changelog via Codex
  run: |
    npm install -g @openai/codex
    export OPENAI_API_KEY="${{ secrets.OPENAI_KEY }}"
    codex exec --full-auto "update CHANGELOG for next release"
```

## Model Context Protocol (MCP)

`~/.codex/config.toml` に [`mcp_servers`](./codex-rs/config.md#mcp_servers) セクションを定義することで、Codex CLI を MCP サーバーに接続するよう設定できます。これは Claude や Cursor が JSON 設定ファイルで `mcpServers` を定義する方法に似ていますが、Codex は JSON ではなく TOML を使用する点が少し異なります。例:

```toml
# 重要: トップレベルのキーは `mcpServers` ではなく `mcp_servers` です。
[mcp_servers.server-name]
command = "npx"
args = ["-y", "mcp-server"]
env = { "API_KEY" = "value" }
```

> [!TIP]
> まだ実験的ですが、`codex mcp` を使うと Codex CLI 自身を MCP の _サーバー_ として起動することもできます。`npx @modelcontextprotocol/inspector codex mcp` のような MCP クライアントから起動し、`tools/list` リクエストを送ると、`codex` という 1 つのツールがあり、任意の設定を上書きできる汎用 `config` マップなど様々な入力を受け付けることが確認できます。ぜひ試してみて、GitHub の Issue でフィードバックをお寄せください。

## トレース/詳細ログ

Codex は Rust で書かれているため、ログの挙動は `RUST_LOG` 環境変数で制御できます。

TUI のデフォルトは `RUST_LOG=codex_core=info,codex_tui=info` で、ログメッセージは `~/.codex/log/codex-tui.log` に書き込まれます。そのため、別ターミナルで次のコマンドを実行しておけば、書き込まれるログを監視できます。

```
tail -F ~/.codex/log/codex-tui.log
```

これに対し、非対話モード（`codex exec`）のデフォルトは `RUST_LOG=error` で、メッセージは標準出力に直接表示されるため、別ファイルを監視する必要はありません。

設定オプションの詳細については、Rust の [`RUST_LOG`](https://docs.rs/env_logger/latest/env_logger/#enabling-logging) ドキュメントを参照してください。

---

### DotSlash

GitHub のリリースには `codex` という名前の [DotSlash](https://dotslash-cli.com/) ファイルも含まれています。DotSlash ファイルを利用すると、開発プラットフォームに関係なく全てのコントリビューターが同じバージョンの実行ファイルを使用できるよう、軽量なコミットをリポジトリに追加できます。

</details>

<details>
<summary><strong>ソースからビルドする</strong></summary>

```bash
# リポジトリをクローンし、Cargo ワークスペースのルートに移動します。
git clone https://github.com/openai/codex.git
cd codex/codex-rs

# 必要であれば Rust ツールチェーンをインストールします。
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
rustup component add rustfmt
rustup component add clippy

# Codex をビルドします。
cargo build

# サンプルのプロンプトで TUI を起動します。
cargo run --bin codex -- "explain this codebase to me"

# 変更を加えた後はコードが整っているか確認します。
cargo fmt -- --config imports_granularity=Item
cargo clippy --tests

# テストを実行します。
cargo test
```

</details>

---

## 設定

Codex は [`codex-rs/config.md`](./codex-rs/config.md) に記載されている豊富な設定オプションをサポートしています。

デフォルトでは `~/.codex/config.toml` から設定を読み込みます。

個々の `codex` 実行で一時的に値を設定・上書きしたい場合は、`--config` を利用できます。

---

## FAQ

<details>
<summary>OpenAI が 2021 年に Codex というモデルを公開しましたが、これは関係がありますか？</summary>

2021 年に OpenAI は、自然言語のプロンプトからコードを生成する AI システムである Codex を公開しました。その元の Codex モデルは 2023 年 3 月に廃止されており、この CLI ツールとは別物です。

</details>

<details>
<summary>どのモデルがサポートされていますか？</summary>

[Responses API](https://platform.openai.com/docs/api-reference/responses) で利用可能なモデルならどれでも使用できます。デフォルトは `o4-mini` ですが、`--model gpt-4.1` を渡すか、設定ファイルで `model: gpt-4.1` を指定して上書きできます。

</details>

<details>
<summary><code>o3</code> や <code>o4-mini</code> が動作しないのはなぜですか？</summary>

[API アカウントの認証](https://help.openai.com/en/articles/10910291-api-organization-verification) が必要で、認証しないとレスポンスのストリーミングや chain-of-thought の要約を API から受け取れない場合があります。それでも問題が解決しない場合はお知らせください。

</details>

<details>
<summary>Codex にファイルを編集させないようにするには？</summary>

Codex はモデルが生成したコマンドをサンドボックス内で実行します。提案されたコマンドやファイル変更が不適切に見える場合は、単に **n** と入力してコマンドを拒否するか、モデルにフィードバックを返してください。

</details>

<details>
<summary>Windows で動作しますか？</summary>

直接は動作しません。Codex は [Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/install) を必要とします。macOS と Linux（Node 22）でテストされています。

</details>

---

## Zero data retention (ZDR) の利用

Codex CLI は [Zero Data Retention (ZDR)](https://platform.openai.com/docs/guides/your-data#zero-data-retention) を有効にした OpenAI 組織でも利用できます。組織で ZDR が有効になっているにもかかわらず次のようなエラーが発生する場合:

```
OpenAI rejected the request. Error details: Status: 400, Code: unsupported_parameter, Type: invalid_request_error, Message: 400 Previous response cannot be used for this organization due to Zero Data Retention.
```

`codex` を `--config disable_response_storage=true` 付きで実行するか、毎回コマンドラインで指定したくない場合は `~/.codex/config.toml` に次の行を追加してください。

```toml
disable_response_storage = true
```

詳細は [`disable_response_storage` の設定ドキュメント](./codex-rs/config.md#disable_response_storage)を参照してください。

---

## Codex オープンソース基金

Codex CLI や他の OpenAI モデルを利用するオープンソースプロジェクトを支援する **100 万ドル規模** の取り組みを開始しました。

- 助成額は最大 **25,000 ドル分の API クレジット**。
- 申請は **随時審査** されます。

**興味がありますか？[こちらから応募してください](https://openai.com/form/codex-open-source-fund/)。**

---

## コントリビュート

このプロジェクトは活発に開発中であり、コードは今後大きく変更される可能性があります。

**現在は外部からのバグ修正やセキュリティ修正のレビューを優先しています。**

新機能を追加したい、あるいは既存機能の挙動を変更したい場合は、まず Issue を立てて提案し、OpenAI チームメンバーの承認を得てから実装に取りかかってください。

**このプロセスを踏まない新しい貢献は、現在のロードマップと一致しない場合や他の優先事項・予定機能と競合する場合にはクローズされる可能性があります。**

### 開発ワークフロー

- `main` からトピックブランチを作成します（例: `feat/interactive-prompt`）。
- 変更点は焦点を絞り、無関係な修正は別の PR としてください。
- 上記の [開発設定](#開発ワークフロー) に従い、変更に lint 警告やテスト失敗がないことを確認します。

### インパクトの高いコード変更を書く

1. **Issue から始める。** 新しい Issue を立てるか既存の議論にコメントし、コードを書く前に解決策について合意します。
2. **テストを追加/更新する。** 新機能やバグ修正には、変更前は失敗し変更後は成功するテストを用意してください。100% のカバレッジは必須ではありませんが、意味のあるアサーションを心がけましょう。
3. **挙動を文書化する。** ユーザーに影響する変更の場合は README や `codex --help`、関連するサンプルプロジェクトを更新してください。
4. **コミットは原子的に。** 各コミットはコンパイルでき、テストが通る状態であるべきです。レビューやロールバックが容易になります。

### プルリクエストを開く

- PR テンプレートに記入（または同等の情報を含める）— **What? Why? How?**
- **すべて**のチェックをローカルで実行する（`cargo test && cargo clippy --tests && cargo fmt -- --config imports_granularity=Item`）。ローカルで発見できたはずの CI 失敗はプロセスを遅らせます。
- ブランチが `main` と最新であり、マージコンフリクトを解消していることを確認します。
- マージ可能な状態になったと思ったら、PR を **Ready for review** に設定します。

### レビュー手順

1. メンテナーがメインレビュアーとして割り当てられます。
2. 事前に議論・承認されていない新機能を追加する PR は、[コントリビュート](#コントリビュート)を参照しつつ、クローズされる場合があります。
3. 変更をお願いすることがありますが、個人への批判ではありません。私たちは一貫性と長期的な保守性を重視します。
4. PR が基準を満たしたと合意されたら、メンテナーが squash-and-merge を行います。

### コミュニティバリュー

- **親切で包括的に。** 他者を尊重し、[Contributor Covenant](https://www.contributor-covenant.org/) に従います。
- **善意を前提に。** 文章でのコミュニケーションは難しいので、寛容さを心がけます。
- **教え合い学び合う。** 混乱を招く点を見つけたら Issue や PR で改善を提案してください。

### ヘルプを得る

プロジェクトのセットアップで問題が発生した場合、アイデアへのフィードバックがほしい場合、あるいは単に挨拶したい場合でも、Discussion を開くか関連する Issue に参加してください。喜んでお手伝いします。

一緒に Codex CLI を素晴らしいツールにしましょう。**Happy hacking!** :rocket:

### Contributor license agreement (CLA)

すべてのコントリビューターは CLA に同意する必要があります。手順は簡単です。

1. プルリクエストを開きます。
2. 次のコメントを投稿します（以前署名済みの場合は `recheck` と返信してください）。

   ```text
   I have read the CLA Document and I hereby sign the CLA
   ```

3. CLA-Assistant ボットがリポジトリに署名を記録し、ステータスチェックをパスさせます。

特別な Git コマンドやメール添付、コミットフッターは不要です。

#### クイックフィックス

| シナリオ             | コマンド                                         |
| -------------------- | ------------------------------------------------ |
| 最後のコミットを修正 | `git commit --amend -s --no-edit && git push -f` |

**DCO チェック** は、PR 内のすべてのコミットにフッターが付くまでマージをブロックします（squash の場合は 1 つだけで済みます）。

### `codex` のリリース

_管理者のみ_

`main` にいること、そしてローカルに変更がないことを確認してから次を実行します。

```shell
VERSION=0.2.0  # 例: 0.2.0-alpha.1 など有効な Rust バージョン。
./codex-rs/scripts/create_github_release.sh "$VERSION"
```

これにより、`codex-rs/Cargo.toml` の `version` を `$VERSION` に設定したローカルコミットが `main` の上に作成されます（`main` では `version = "0.0.0"` のままにしておきます）。

このコミットは `rust-v${VERSION}` タグでプッシュされ、[リリースワークフロー](.github/workflows/rust-release.yml) を起動します。これにより `$VERSION` という名前の新しい GitHub Release が作成されます。

生成された GitHub Release に問題がなければ、**pre-release** のチェックを外して最新リリースにします。

Homebrew の [`Formula/c/codex.rb`](https://github.com/Homebrew/homebrew-core/blob/main/Formula/c/codex.rb) を更新する PR を作成します。

---

## セキュリティと責任ある AI

脆弱性を発見したり、モデルの出力に懸念がある場合は **security@openai.com** までメールでお知らせください。速やかに対応します。

---

## ライセンス

このリポジトリは [Apache-2.0 License](LICENSE) の下でライセンスされています。
