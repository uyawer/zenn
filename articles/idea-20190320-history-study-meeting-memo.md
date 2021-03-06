---
title: "2019/03/20 プログラミング・パラダイムの歴史とテクニック@社内"
emoji: "📚"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["勉強会", "メモ", "歴史"]
published: true
---

:::message
こちらの記事は**2019/03/20**に社内で行われた勉強会のメモとなります。
一度自身のブログで公開した経緯はありますが、現在そちらは非公開としています。
また、メモのため独自の表現や誤った解釈が含まれている場合があります。
:::

# 機械式計算機

## 最初のプログラマ

- [Augusta Ada King](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%82%A4%E3%83%80%E3%83%BB%E3%83%A9%E3%83%96%E3%83%AC%E3%82%B9)（オーガスタ・エイダ・キング）
- [Charles Babbage](https://ja.wikipedia.org/wiki/%E3%83%81%E3%83%A3%E3%83%BC%E3%83%AB%E3%82%BA%E3%83%BB%E3%83%90%E3%83%99%E3%83%83%E3%82%B8)（チャールズ・バベッジ）

## 機械仕掛けの計算機

- [階差機関](https://ja.wikipedia.org/wiki/%E9%9A%8E%E5%B7%AE%E6%A9%9F%E9%96%A2)
- [解析機関](https://ja.wikipedia.org/wiki/%E8%A7%A3%E6%9E%90%E6%A9%9F%E9%96%A2)
  - 資金枯渇で開発されなかった
  - 四則演算とかやるつもりだった
  - パンチカードでやってた
- bombe
  - 機械・電気式計算機
  - 歯車の位置を替えて計算する形だった

## 電子計算機

- [Alan Mathieson Turing](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%A9%E3%83%B3%E3%83%BB%E3%83%81%E3%83%A5%E3%83%BC%E3%83%AA%E3%83%B3%E3%82%B0)（アラン・マシスン・チューリング）
  - 計算理論の基礎となる数多くのアイデアをまとめた
  - 生きてる間には評価されなかった
  - 最近映画化された
- [John von Neumann](https://ja.wikipedia.org/wiki/%E3%82%B8%E3%83%A7%E3%83%B3%E3%83%BB%E3%83%95%E3%82%A9%E3%83%B3%E3%83%BB%E3%83%8E%E3%82%A4%E3%83%9E%E3%83%B3)（ジョン・フォン・ノイマン）
  - 原爆に関わってた
  - ほとんどのコンピュータの基本アーキテクチャとなるプログラム内蔵方式について論文にまとめた

## パラダイム以前ざっくり

- インデントは一段のみ
  - if文あっても全部同じ
- 制御構文は条件付きジャンプ命令のみ
  - 今でもCPUレベルではこれだけで動作している
- データ構造はアドレス計算で表現

## 構造化プログラミング

- [Edsger Wybe Dijkstra](https://ja.wikipedia.org/wiki/%E3%82%A8%E3%83%89%E3%82%AC%E3%83%BC%E3%83%BB%E3%83%80%E3%82%A4%E3%82%AF%E3%82%B9%E3%83%88%E3%83%A9)（エドガー・ダイクストラ）
  - 構造化プログラミングを提唱して炎上する
- [P. J. Plauger](https://en.wikipedia.org/wiki/P._J._Plauger)（P.J.プローガ）
  - 非標準Cコンパイラの作者
  - データストリームに対する様々なロジックについて重要な書籍を残して
- [Donald Ervin Knuth](https://ja.wikipedia.org/wiki/%E3%83%89%E3%83%8A%E3%83%AB%E3%83%89%E3%83%BB%E3%82%AF%E3%83%8C%E3%83%BC%E3%82%B9)（ドナルド・エルビン・クヌース）
  - 文芸的プログラミングといって巨大なTeXシステムを一つのドキュメントとして記述した

## 構造とは

- 要素を「順次」「反復」「分岐」によって組み合わせたもの
- 計算手続きの構造とデータ構造の双方の表現に使われる

## 構造化設計・プログラミング

- アルゴリズムとデータ構造＝プログラム
- [段階的詳細化法](https://ja.wikipedia.org/wiki/%E6%AE%B5%E9%9A%8E%E7%9A%84%E8%A9%B3%E7%B4%B0%E5%8C%96%E6%B3%95)
- トップダウン・ボトムアップ
  - 細かいところから作って組み合わせる
- レイヤード・アーキテクチャ
- 依存性の分析
  - モジュール化

## オブジェクト指向

- [Alan Curtis Kay](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%A9%E3%83%B3%E3%83%BB%E3%82%B1%E3%82%A4)（アラン・カーティス・ケイ）
  - 初期のオブジェクト指向言語（Smalltalk）の開発を手動
- [Bertrand Meyer](https://ja.wikipedia.org/wiki/%E3%83%90%E3%83%BC%E3%83%88%E3%83%A9%E3%83%B3%E3%83%89%E3%83%BB%E3%83%A1%E3%82%A4%E3%83%A4%E3%83%BC)（バートランド・メイヤー）
- [Bjarne Stroustrup](https://ja.wikipedia.org/wiki/%E3%83%93%E3%83%A3%E3%83%BC%E3%83%8D%E3%83%BB%E3%82%B9%E3%83%88%E3%83%AD%E3%83%B4%E3%82%B9%E3%83%88%E3%83%AB%E3%83%83%E3%83%97)（ビャーネ・ストロヴストルップ）
  - 性善説にたった言語でC++を作成

## オブジェクト指向ざっくり

- カプセル化とオブジェクト・アイデンティティ
- 主語と述語
- モノ（Object）と特性
- シナリオとインタラクション

## 関数型言語

- [John McCarthy](https://ja.wikipedia.org/wiki/%E3%82%B8%E3%83%A7%E3%83%B3%E3%83%BB%E3%83%9E%E3%83%83%E3%82%AB%E3%83%BC%E3%82%B7%E3%83%BC)（ジョン・マッカーシー）
  - Lispを理論上の存在として机上のみ設計
- [Steve Russell](<https://en.wikipedia.org/wiki/Steve_Russell_(computer_scientist)>)（スティーブ・ラッセル）
  - 理論上の存在だったLispをアセンブリで作り上げてしまう（天才）
- [Brendan Eich](https://ja.wikipedia.org/wiki/%E3%83%96%E3%83%AC%E3%83%B3%E3%83%80%E3%83%B3%E3%83%BB%E3%82%A2%E3%82%A4%E3%82%AF)（ブレンダン・アイク）
  - Lispを作るために会社に呼ばれたけど騙された
  - 腹いせにJavascriptを設計・実装

## 関数型言語ざっくり

- 参照透過性
  - 何度読んでも同じ結果が返るような
- if, whileなどの制御構文がほぼ無い
- 遅延評価とデータストリーム
- First class function

## 実践（ペアプロ）

### [ライフゲーム](https://ja.wikipedia.org/wiki/%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B2%E3%83%BC%E3%83%A0)を作る

- とりあえず条件はなし（ルールに沿っていればおｋ）
- 言語はJavascript
- 役割をそれぞれが担う
  - ドライバー
    - 実際に手を動かしてコードを書く人
    - ナビゲーターのサポートの元で細かい部分を含め実装する
  - ナビゲーター
    - ドライバーのサポートをする人
    - ドライバーの分からないことは調査する

### 今回の成果物

https://github.com/uyawer/jstest01
