# MarketInsight-Lib

MarketInsightAI の共通ライブラリです。

複数のリポジトリ（Backend / MCP-Agent / History / MCP-Tool）から使う
型定義・設定・共通処理をここに集約します。

> **現状：雛形のみ**
> ファイルは作成済みですが中身は空です。共通化はまだ行われておらず、
> 型定義は各リポジトリ内に散在しています。

## 役割（想定）

| ディレクトリ | 置くもの |
| --- | --- |
| `types/` | プロジェクト共通の型定義（`Message`, `AgentState` など） |
| `auth/` | Azure Entra ID 認証ヘルパー |
| `llm/` | Azure OpenAI クライアントの初期化・ラッパー |
| `config/` | 環境変数のロード、設定値の型定義 |
| `prompts/` | システムプロンプトのテンプレート管理 |

## ファイル構成（現状）

```
MarketInsight-Lib/
├── pyproject.toml
└── src/
    ├── marketinsight_lib/
    │   └── __init__.py       # 空
    └── types/                # 空ファイル（下記の注意点を参照）
        ├── __init__.py
        ├── message.py
        ├── agent.py
        └── tool.py
```

### 注意点

`pyproject.toml` の `packages` は `src/marketinsight_lib` のみを指定しています。

```toml
[tool.hatch.build.targets.wheel]
packages = ["src/marketinsight_lib"]
```

そのため **`src/types/` はインストール対象に含まれず、import できません**。
型定義を書くときは以下のいずれかにします。

- `src/marketinsight_lib/types/` に移動する（推奨。`from marketinsight_lib.types import Message`）
- `packages` に `"src/types"` を追加する（ただし `types` は標準モジュール名と衝突するため非推奨）

## セットアップ

```bash
cd /Users/estyle-180/Documents/study/MarketInsightAI
source .venv/bin/activate

pip install -e MarketInsight-Lib
```

依存は他リポジトリより先にインストールします（Lib が最下層）。

## 起動方法

単体で起動するものではありません。他のリポジトリから import して使うライブラリです。

インストール確認：

```bash
python -c "import marketinsight_lib; print(marketinsight_lib.__file__)"
pip show marketinsight-lib
```

## `pyproject.toml` について

`requirements.txt` や Node.js の `package.json` に相当するファイルです。
`requirements.txt` ではなく `pyproject.toml` を使うのは、
**他のリポジトリから `pip install -e .` で import 可能にするため**です。

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "marketinsight-lib"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "pydantic>=2.0",
]

[tool.hatch.build.targets.wheel]
packages = ["src/marketinsight_lib"]
```

### セクションの意味

| セクション | 定義している内容 |
| --- | --- |
| `[build-system]` | パッケージ化（ビルド）に使うバックエンドツールの指定 |
| `[project]` | `name`（パッケージ名）、`version`、`requires-python`、`dependencies`（外部ライブラリ） |
| `[tool.hatch.build.targets.wheel]` | `packages` でソースコードの場所を指定。これが無いと `pip install -e .` しても import できない |

### hatchling とは

Python パッケージのビルドツールです。
`pip install -e .` を実行したときに裏で動き、
「このフォルダを Python パッケージとして使えるようにする」作業をしてくれます。

### editable インストール（`-e`）

`-e` を付けると、コードをコピーせず**参照**でインストールされます。
そのため、Lib のファイルを編集した内容が、再インストールなしで
Backend や MCP-Agent 側に即反映されます。

## 今後の整備ポイント

- `requires-python = ">=3.12"` がプロジェクト全体の下限を決めている
  （他リポジトリは `>=3.10`）。揃えるか意図を明示するかを検討
- MCP-Agent の `clients/base.py` にある `CommonResponse` 等は、
  本来この Lib に置くべき共通型
- Backend の Pydantic モデル（`ChatRequest` など）も共通化の候補
- テストが未整備（`tests/` なし）
