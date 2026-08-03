# MARKETINSIGHT-LIB

## 概要
MARKETINSIGHTのライブラリ

## ファイル構成

### pyproject.toml
- requirements.txtやNode.jsのpackage.jsonのようにライブラリのバージョンを記載する
- requirements.txt ではなく、pyproject.tomlを使うことで、他のモジュールから、 pip install -e . で import 可能にするため

#### セクション説明
| セクション名 | 何を定義している？ |
|---|---|
| `[build-system]` | （プロジェクトをパッケージ化（ビルド）するためのバックエンドツールを指定） |
| `[project]` | （name(プロジェクト名)、version(pyproject.tomlのバージョン)、requires-python(pythonのバージョン)、dependencies(必要な外部ライブラリのリスト)　　　） |
| `[tool.hatch.build.targets.wheel]` | （packages でソースコードがどのディレクトリにあるかを指定している
これにより pip install -e . したとき、src/marketinsight_lib/ がパッケージとして認識される　　　） |