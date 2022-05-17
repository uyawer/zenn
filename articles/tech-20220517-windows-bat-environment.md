---
title: "batファイルからシステム環境変数を更新する"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["windows", "bat", "環境変数"]
published: true
---

# 経緯

様々なJavaのプロジェクトを並行して進めている時にJavaのバージョンが異なるため、実行する時に`JAVA_HOME`を都度切り替えていたのが面倒でした。
ということで、**画面左下の【スタート】から【システム環境変数】と入力し、環境変数を書き換える**という手順をbatファイル化できればゴールです。

# 結果

こんな感じのbatファイルができました。

```batch
@echo off

setx /m JAVA_HOME "C:\tmp" > nul 2>&1

if %errorlevel% neq 0 (
  Powershell Start-Process -FilePath "%0" -Verb RunAs
  exit
)

set     JAVA_HOME="(JAVA_HOMEに設定するpath)"
setx /m JAVA_HOME "(JAVA_HOMEに設定するpath)"
@echo;
set JAVA_HOME
@echo;

pause
```

## 説明

結果だけで終わるのも悲しいのでbatファイルの中身を説明

```batch
@echo off
```

batファイルを書く時に良くあるおまじないですね。
記述しなくても内容に変化はありませんが、ファイルに書かれているコマンド一つ一つを丁寧に表示されてしまいうっとうしいので無駄な出力は非表示にします。

```batch
setx /m JAVA_HOME "C:\tmp" > nul 2>&1

if %errorlevel% neq 0 (
  Powershell Start-Process -FilePath "%0" -Verb RunAs
  exit
)
```

こちらではシステム環境変数`JAVA_HOME`に`C:\tmp`の設定を試行します。
下のif文でerrorlevel(終了コード)を判定し、`0(正常終了)`以外であれば失敗しているため、管理者権限で再実行します。

```batch
set     JAVA_HOME="(JAVA_HOMEに設定するpath)"
setx /m JAVA_HOME "(JAVA_HOMEに設定するpath)"
```

このbatファイルの肝である環境変数への設定です。
`set`で設定した値は永続的に保持されず、一時的な変数となってしまうため、システム環境変数に設定する場合は`setx`を使います。
オプションに指定している`\m`は`setx`のヘルプを参照すると以下のように記述されています。

> 変数をシステム全体 (HKEY_LOCAL_MACHINE)の環境で設定することを指定します。
> 既定では、HKEY_CURRENT_USER 環境で変数を設定するように設定されています。

システム全体の環境、つまりはシステム環境変数に設定することを表しています。
因みにオプションを外して`setx JAVA_HOME "(JAVA_HOMEに設定するpath)"`とした場合はユーザー環境変数へ`JAVA_HOME`が設定されます。

```batch
@echo;
set JAVA_HOME
@echo;
```

`@echo;`や`@echo+`というように、`@echo`の後ろに記号を付与するとコマンドプロンプトでは改行を表示することができます。
（一部例外があるようなので使える記号は確認しておきましょう）

`set JAVA_HOME`で今回設定した環境変数の出力をします。
前のブロックで一時的な変数として`set`を実行している理由は、ここで確認用に環境変数を表示させるようにしているためでした。
確認が不要であればこの部分と合わせて`set`側も消してしまいましょう。

```batch
pause
```

言わずもがな、な一時停止です。記述しなくても問題ありません。
記述していない場合は、batファイルの中身が少ないため、コマンドプロンプトが一瞬だけ表示されて消えます。

`JAVA_HOME`に限らず様々な理由でシステム環境変数への設定を行う場面があると思いますので、一つの案として参考にしてみてください。
