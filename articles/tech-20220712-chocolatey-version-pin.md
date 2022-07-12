---
title: "chocolateyでインストールしたソフトウェアを特定のバージョンに固定する"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [windows, chocolatey]
published: true
---

# 環境

Windows 11

# 経緯

ローカル環境でRadisを使ったプロジェクトの開発を行おうとし、chocolateyを使ってRadisを導入してみたものの上手くデータの読み書きができない…。
確認してみると、特定のバージョン以降での動作に変更があり動かないことが分かったので、Radisのバージョンを固定にする必要が出た。

ということで、**chocolateyでインストールしたソフトウェアのバージョンを固定すること**ができれば今回のゴールになります。

# 結果

chocolateyにはバージョンを固定するための機能として`pin`が用意されています。
<https://docs.chocolatey.org/en-us/choco/commands/pin>

`pin`を使うことによって、`chocolatey upgrade all -y`を実行した場合でも勝手にアップグレードされることが無くなります。

# 説明

今回は例としてRadis(64bit)を取り上げて説明します。
<https://community.chocolatey.org/packages/redis-64>

指定できるバージョンについては、各ソフトウェアのページにある`Version History`から確認できます。

## インストール時のバージョン指定

インストール時には以下の方法でバージョンを指定することができます。

### コマンドの場合

```ps1
choco install redis-64 --version=3.0.501
```

### configファイルの場合

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
    <!-- Redis3.0の最終版 -->
    <package id="redis-64" version="3.0.501" />
</packages>
```

## ソフトウェアのバージョンを固定

`pin add`からバージョンの固定を行います。

```ps1
choco pin add --name="'redis-64'" --version="'3.0.501'"
```

pinに追加されたソフトウェアの一覧は`pin list`から確認することができます。

```ps1
chocolatey pin list
```

こんな感じで出力されます。

```ps1
redis-64|3.0.501
```

pinを外したい時は`pin remove`を実行します。

```ps1
choco pin remove --name="'redis-64'"
```

# 感想

環境によってバージョンを固定しなければならないソフトウェアは様々な理由で存在すると思います。
（必ず最新版が安心安全で使いやすい、とかそんなことはない）

必要に応じてバージョンを固定することで、安定した開発環境を目指していきましょう。
