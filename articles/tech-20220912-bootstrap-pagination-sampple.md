---
title: "bootstrapを使ったpaginationのサンプル"
emoji: "🔢"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [java, bootstrap5, jquery, javascript, thymeleaf]
published: true
---

# 環境

- Java17（SpringBoot/Thymeleaf）
- bootstrap5
- fontawesome（次ページとかの矢印に使ってるだけ）

# 経緯

**pager？pagination？**

個人的に最初に聞いたことがあるのは`pager`なのですが、bootstrapでは`pagination`となっているので後者で進めます。

一覧画面によくあるpagination。検索サイトにもある1画面に表示する件数を予め決めておき、溢れた分を次のページにして行き来ができるアレです。
開発を進めている際によくある一覧画面を作ることになったのですが、調べてみても検索ワードが悪かったのか自分が求めているものが中々ヒットせず、作るのに少し苦労したので備忘録として残しておきます。

# 結論

他サイトの記事[^1]を参考にしつつ、自分が作りたかったpaginationがこちら。

```javascript:pagination.js
/**
 * paginationを生成
 * @param maxPage 計算済みの最大ページ数
 * @param visiblePage 現在のページから表示する前後のページ数
 */
function createPager(maxPage, visiblePage) {
  // ページ数を取得
  let page = Number(getQueryParam('page'));
  if (page < 1) {
    page = 1;
  }

  // paginationの設定
  let pagination = $('<ul class="pagination justify-content-center">')

  // 最初のページ
  let first = createPagerLink(1, '<i class="fa-solid fa-angles-left"></i>', page !== 1)
  pagination.append(first)
  // 前のページ
  let back = createPagerLink(page - 1, '<i class="fa-solid fa-angle-left"></i>', page > 1)
  pagination.append(back)

  // ページ番号
  let startPage = page - visiblePage
  let endPage = page + visiblePage
  if (startPage < 1) {
    endPage += -(startPage - 1) // 1未満のページ数をendPageに加算
    startPage = 1
  } else if (maxPage < endPage) {
    startPage -= (endPage - maxPage) // maxPage以上のページ数をstartPageに加算
    if (startPage < 1) {
      startPage = 1
    }
    endPage = maxPage
  }
  for (let i = startPage; i <= endPage && i <= maxPage; i++) {
    let number = createPagerLink(i, i, true)
    if (i === page) {
      number.addClass('active')
      number.css('pointer-events', 'none')
    }
    pagination.append(number)
  }

  // 後のページ
  let next = createPagerLink(page + 1, '<i class="fa-solid fa-angle-right"></i>', page < maxPage)
  pagination.append(next)
  // 最後のページ
  let last = createPagerLink(maxPage, '<i class="fa-solid fa-angles-right"></i>', page !== maxPage)
  pagination.append(last)

  // 生成したpaginationを設定
  $('#pagination').html(pagination);
}

// URLから指定したパラメータを取得
function getQueryParam($key) {
  if (1 < document.location.search.length) {
    const query = document.location.search.substring(1);
    const parameters = query.split('&');
    for (let i = 0; i < parameters.length; i++) {
      // パラメータ名とパラメータ値に分割する
      const element = parameters[i].split('=');
      if (element[0] === $key) {
        return element[1];
      }
    }
  }
  return null;
}

// paginationのリンクを生成
function createPagerLink(page, label, isEnable) {
  let pageLink = $(`<a class="page-link" href="?page=${page}">`).html(`<div class="text-center" style="width: 1.5rem">${label}</div>`)
  let pageItem = $('<li class="page-item">').html(pageLink)
  if (!isEnable) {
    pageItem.addClass('disabled')
  }
  return pageItem
}
```

```html:list.html
<div class="container-fluid pt-3">
  <!-- list -->
  <div class="row">
    <div class="col">
      <table class="table">
        ...
      </table>
    </div>
  </div>
  <!-- pagination -->
  <div class="row">
    <nav class="col" id="pagination"></nav>
  </div>
</div>

<script th:inline="javascript" type="text/javascript">
  /*<![CDATA[*/
  $(function () {
    // paginationを生成
    let maxPage = /*[[${maxPage}]]*/ 2
    let visiblePage = /*[[${visiblePage}]]*/ 2
    createPager(maxPage, visiblePage)
  })
  /*]]>*/
</script>
```

paginationはjavascriptを使って組み立て、最終的に`<nav id="pagination"></nav>`に埋め込まれます。

そして見た目がこちら。

**1/10ページ目の見た目**
![1/10ページ目の見た目](https://storage.googleapis.com/zenn-user-upload/0db1601710e1-20220915.png)
**6/10ページ目の見た目**
![6/10ページ目の見た目](https://storage.googleapis.com/zenn-user-upload/bfb955de5372-20220915.png)
**10/10ページ目の見た目**
![10/10ページ目の見た目](https://storage.googleapis.com/zenn-user-upload/dc026862fd69-20220915.png)

そう、最初と最後のページにいてもpaginationの横幅が変動しないモノが作りたかったのです。

# 説明

## 最大ページ数の計算

今回、Javaでサーバー側を開発しているため、DBから一覧に表示するためのリストを取得し、画面を表示する前に最大ページ数を計算しています。
計算ロジックは言うまででもないかなぁ、とは思いますが一応載せておくとこんな感じ。

```java
// 一覧に表示するリストを取得
List<Entity> results = service.find();
// 最大のページ数を計算
double maxPage = 1;
if (results.size() > 0) {
    double a = (double) results.size() / (double) pageSize; // pageSizeには1ページで表示する件数を設定
    maxPage = Math.ceil(a); // 小数点以下を切り上げ
}
```

## paginationの生成

ブラウザにページが表示されるタイミングで`createPager`を呼び出し、paginationの生成が始まります。
引数はコメントにもある通り`最大ページ数`と`現在のページから表示する前後のページ数`を指定します。

`createPager`ではまずURLのクエリパラメータにある`page`から現在表示しているページ数を取得します。
取得できなかったらデフォルトで最初のページである`1`を設定します。

```javascript
// ページ数を取得
let page = Number(getQueryParam('page'));
if (page < 1) {
  page = 1;
}
```

### paginationリストを組み立て

ここからbootstrap5の[pagination](https://getbootstrap.jp/docs/5.0/components/pagination)に合わせてリストを作っていきます。

先に矢印やページ数を詰めるための要素を作っておきます。

```javascript
let pagination = $('<ul class="pagination justify-content-center">')
```

`justify-content-center`があるのは画面で中央に表示させるためです。

ここからpaginationのリンクを生成していきますが、処理を共通化させたかったので`createPagerLink`を作成しています。

```javascript
// paginationのリンクを生成
function createPagerLink(page, label, isEnable) {
  let pageLink = $(`<a class="page-link" href="?page=${page}">`).html(`<div class="text-center" style="width: 1.5rem">${label}</div>`)
  let pageItem = $('<li class="page-item">').html(pageLink)
  if (!isEnable) {
    pageItem.addClass('disabled')
  }
  return pageItem
}
```

引数には`移動先のページ数(page)`, `表示するラベル(label)`, `リンクの活性状態(isEnable)`を指定していきます。

### 最初のページ, 前のページ

最初のページである1ページへ移動するリンクと、現在表示しているページから1つ前のページへ移動するリンクです。

```javascript
// 最初のページ
let first = createPagerLink(1, '<i class="fa-solid fa-angles-left"></i>', page !== 1)
pagination.append(first)
// 前のページ
let back = createPagerLink(page - 1, '<i class="fa-solid fa-angle-left"></i>', page > 1)
pagination.append(back)
```

1ページ目を表示するため、最初のページへ移動するリンクには1を固定で指定し、前のページには現在のページから-1の値を指定します。
リンクの活性状態についても1ページ目以外にいる場合に活性としています。
（どっちも同じタイミングで`true`になるので、ここの活性状態は検討の余地あり）

### 現在のページ, 前後のページ

数字部分のページのリンクを作成します。

```javascript
// ページ番号
let startPage = page - visiblePage
let endPage = page + visiblePage
if (startPage < 1) {
  endPage += -(startPage - 1) // 1未満のページ数をendPageに加算
  startPage = 1
} else if (maxPage < endPage) {
  startPage -= (endPage - maxPage) // maxPage以上のページ数をstartPageから減算
  if (startPage < 1) {
    startPage = 1
  }
  endPage = maxPage
}
for (let i = startPage; i <= endPage && i <= maxPage; i++) {
  let number = createPagerLink(i, i, true)
  if (i === page) {
    number.addClass('active')
    number.css('pointer-events', 'none')
  }
  pagination.append(number)
}
```

リンクを作成する部分については単純で、`startPage`から`endPage`までのページ数をfor文で回しています。
リンクの活性状態についてもどれも有効となるため`true`としていますが、現在のページについてはリンクは不要なのとclassに`active`を追加するため、別でclassとcssを設定しています。

`startPage`, `endPage`のデフォルトの値として、現在表示しているページから前後に表示するページを計算しますが、最初や最後のページだった場合にマイナスやmaxPage以上となって欠けてしまったページ数を逆側に拡張するために条件を追加しています。

`startPage`が1未満となった場合は`endPage`に欠けてしまったページ数を加算したいため、`startPage - 1`で0からのマイナス分として計算しておき、計算結果に`-`を付けておくことで負数を正数に反転することができるので、この値を`endPage`に加算していきます。

`endPage`がmaxPage以上となった場合は`startPage`から欠けてしまったページ数を減算したいため、こちらも同じ要領で`endPage - maxPage`で溢れた分のページを計算し、`startPage`を減算していきます。
この時に、例えば`現在のページから表示する前後のページ数`に`2`が設定されているとして、`maxPage`が`3`、`現在表示しているページ`が`3`だった場合、`startPage`には`5(endPage) - 3(maxPage)`で`2`が算出され、`1`だった`startPage`から引かれて1未満となってしまうため、この場合は`1`を設定するようにしています。
因みに`endPage`についてはfor文にて`i <= maxPage`があるため上限を超えることはありません。

### 次のページ, 最後のページ

最初のページ, 前のページとあまり変化ないので説明は省略します。

```javascript
// 後のページ
let next = createPagerLink(page + 1, '<i class="fa-solid fa-angle-right"></i>', page < maxPage)
pagination.append(next)
// 最後のページ
let last = createPagerLink(maxPage, '<i class="fa-solid fa-angles-right"></i>', page !== maxPage)
pagination.append(last)
```

こちらについてもリンクの活性状態は検討の余地あり。

### paginationを埋め込み

ここまで組み立てたHTMLを`<nav id="pagination"></nav>`へ埋め込みます。

```javascript
// 生成したpaginationを設定
$('#pagination').html(pagination);
```

# 感想

よく見る部品なのでたくさん情報が転がっていると思っていたのですが、意外に求めているものがなくてびっくりしました。
久々にロジックを考えさせられたので、夢中になった部分もあり、ちょっと楽しかったです。

[^1]: [BootstrapとJavascriptを使用したページネーションのサンプル | 年収アップへの道](https://miyanetdev.com/archives/361)
