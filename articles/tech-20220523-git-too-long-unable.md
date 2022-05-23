---
title: "gitでtoo long unableが起きた時の解消法"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [windows, git]
published: true
---

# 環境

Windows 11

# 経緯

開発を進めているとDBのテーブル名からクラスの名前を付けることが多いと思います。(e.g. schema.FizzTable -> SchemaFizzRepository, FizzEntity, etc...)
今回もそのルールに従って開発を進め、ファイルをコミットしようとインデックスに追加しようとしたらエラーが出てきてインデックスに追加できませんでした。

ということで、**クソ長くなってしまったpathのファイルをインデックスに追加すること**ができれば今回のゴールになります。

```:エラー例
ファイルを追加するときに次の問題が発生しました: open("{くっそ長いpath}"): Filename too long unable to index file '{くっそ長いpath}'
```

# 結果

コマンドプロンプトやPowershell, git bashなどのgitコマンドが使える環境を管理者権限で起動し、以下を流します。

```batch
git config --system core.longpaths true
```

管理者権限で実行している理由は`--system`を指定しているから、です。
PC内の全てのgitリポジトリに反映されます。

抵抗がある方は`--global`や`--local`でより範囲を小さく指定することができます。

コマンド以外ではconfigファイルに`longpaths = true`を設定することでも解消できます。

# 説明

まず、Windowsではpathの文字数が260文字を超えると正しく処理を行ってくれないようです。
gitのインデックスに追加できなかったファイルに対して、コマンドプロンプトから`attrib`コマンドで属性を見ようとしても表示してくれません。
（こちらはWindows10の特定バージョン以降から設定を変更すれば、最長32,767文字まで表示されるようになります。）

これはWindowsのドキュメント[^1]にも記載されており、pathは以下のような構成を想定しています。

| 種別                         | 文字数 |
| ---------------------------- | ------ |
| ドライブ名(`C`や`D`など)     | 1      |
| `:\`                         | 2      |
| ドライブ名以下のファイルpath | 256    |
| 終了コード(EOFみたいなやつ)  | 1      |

合計260文字。これらはMS-DOS時代からの制限となっているようです。[^2]

[Git for Windows](https://github.com/git-for-windows/git)もWindowsの仕様にならって260文字制限をかけているため、pathが長すぎるとエラーが出てインデックスに追加できない事が起きます。[^3]
今では64bitのOSが主流となっているため、260文字制限はデフォルトだと有効となっていますが、設定によって解除することができます。

間違えて設定してしまったconfigは`git config --unset {設定名}`のコマンドで削除することができます。
（設定した場所によっては`--global`や`--system`のオプションも必要になります。）

# 感想

gitでインデックスに追加できなかった問題から調査を始めて、Windowsの歴史を少しかじることができました。
今の状態になっているのには何かしらの理由があるはずなので、こういう細かいところから知見を増やしていきたいですね。

# 参考

[Why does the 260 character path length limit exist in Windows? - stack overflow](https://stackoverflow.com/questions/1880321/why-does-the-260-character-path-length-limit-exist-in-windows)

[^1]: [Naming Files, Paths, and Namespaces - Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/fileio/naming-a-file#maximum-path-length-limitation)
[^2]: [Maximum Path Length Limitation - Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation)
[^3]: [Git cannot create a file or directory with a long path - git-for-windows/git](https://github.com/git-for-windows/git/wiki/Git-cannot-create-a-file-or-directory-with-a-long-path)
