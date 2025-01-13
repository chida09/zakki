# CSSスタイリングのベストプラクティス

## 良くない例
1. 要素に直接スタイルを指定する方法：
```html
<!-- HTML -->
<h1>サンプルタイトル</h1>

<!-- CSS -->
h1 { color: red; }
```

**問題点**: もし後でh1をh2に変更する必要が出てきた場合、CSSも修正が必要になります。

## 推奨される方法
2. クラスを使用してスタイルを指定する方法：
```html
<!-- HTML -->
<h1 class="title">サンプルタイトル</h1>

<!-- CSS -->
.title { color: red; }
```

**メリット**: 
- HTMLのタグを変更しても（h1→h2など）、CSSは修正不要
- コードの保守が簡単
- 再利用性が高い

## 例外
以下の要素は直接スタイルを指定しても問題ありません：
- `<a>` リンク
- `<input>` 入力フォーム
- `<textarea>` テキストエリア

理由：これらの要素は他の要素に変更される可能性が低いためです。

## まとめ
タグを直接指定するのではなく、クラスを使ってスタイリングすることで、より柔軟で保守しやすいCSSを書くことができます！


# normalize.css
「normalize.css」というCSSもあります。こちらもブラウザ間での表示の差異をなくすという目的はreset.cssと同じですが、「リセット（初期化）」ではなく「ノーマライズ（統一する）」という名前のとおり、UAスタイルシートの装飾を極力活かしながらブラウザ間の差異のみを埋めていくというアプローチが異なります。


# remとemの違い

`font-size`プロパティの値の単位に使用されることが多いのは`px`や`em`だった。  
CSS3から新しく`rem`という単位が追加された。  
従来の単位である`em`は親要素の`font-size`を1とした倍率を指定する  
`rem`は「root + em」の名前が示すとおり、ルート要素（HTML文書ならhtml要素）の`font-size`を1とした倍率を指定する

```html
<div class="parent">
  親要素のテキスト
  <div class="child">
    子要素のテキスト
  </div>
</div>
```

## emを使用した場合
```css
html {
  font-size: 10px;
}
.parent {
  font-size: 1.2em;    /* 10px(html) * 1.2 = 12px */
}
.child {
  font-size: 1.2em;    /* 12px(parent) * 1.2 = 14.4px */
}
```

## remを使用した場合
```css
html {
  font-size: 10px;
}
.parent {
  font-size: 1.2rem;   /* 10px(html) * 1.2 = 12px */
}
.child {
  font-size: 1.2rem;   /* 10px(html) * 1.2 = 12px */
}
```

親要素の`font-size`プロパティの値を意識せず、常にhtml要素に対する倍率を指定すればいいため管理が楽になる。  
それに加えて、html要素の`font-size`プロパティの値を変更することで、`rem`を使用した箇所すべての`font-size`を相対的に変更することができるため、絶対指定の簡潔さと相対指定の柔軟性を併せ持つことができる  



# マージンの相殺
ブロックレベルの要素の上下のマージンに起こる「マージンの相殺」と呼ばれる挙動がある。  
`display: inline-block;`の要素や`float`がかかっている要素では起きない。  
この現象はブロックレベル要素の上下方向のマージンにのみ適用され、左右のマージンには影響しない

## 隣接したマージンの相殺

隣接した要素の上下のマージンは相殺される。  
マージンが20pxの要素同士が隣り合った場合、その間隔は40pxではなく20pxになる。

```html
<div class="box"></div>
<div class="box"></div>
```

```css
.box {
    margin: 20px;
}
```

この場合、各boxの上下左右に20pxのマージンが設定されryが、隣接する要素間では相殺が発生し、実際の間隔は20pxとなる。

## マージンの値が異なる場合

マージンの値が異なる場合には、大きいほうのマージンが要素の間隔となる。

```html
<div class="box"></div>
<div class="box-large"></div>
```

```css
.box-large {
    margin: 30px;
}
```

この場合、上のboxは20pxのマージン、下のbox-largeは30pxのマージンを持つが、隣接する部分では大きい方の30pxが適用される。

# CSS box-sizingプロパティの解説

box-sizingプロパティは、要素の最終的なサイズがどのように計算されるかを制御する重要なCSSプロパティです。

## 初心者向けの説明

### 通常の箱（content-box）の場合
* あなたが「50cmの箱が欲しい」と言って、50cmの箱を買ったとします
* その箱に「クッション材を2.5cmずつ入れる」と決めた場合
* 結果として箱全体は「55cm」になってしまいます（50cm + 2.5cm × 2）
* これが、CSSのデフォルトの動作（content-box）です

### 新しい考え方の箱（border-box）の場合
* 最初から「クッション材を含めて50cmにしたい」と考えます
* 箱全体が50cmで、その中にクッション材（2.5cm × 2）を入れると
* 実際の収納スペースは自動的に45cmになります
* これが「box-sizing: border-box」の考え方です

## 技術的な説明

### content-box（デフォルト）の特徴
```css
.element {
  width: 300px;
  padding: 20px;
  border: 1px solid #000;
  box-sizing: content-box;
  /* 実際の幅: 300px + (20px * 2) + (1px * 2) = 342px */
}
```
* widthやheightで指定したサイズは、コンテンツ領域だけに適用
* paddingとborderの値は、指定したwidthやheightの値の外側に追加
* 実際の要素幅は「指定width + padding + border」で計算

### border-boxの特徴
```css
.element {
  width: 300px;
  padding: 20px;
  border: 1px solid #000;
  box-sizing: border-box;
  /* 実際の幅: 300px（この中にpadding, borderが含まれる） */
}
```
* widthやheightで指定したサイズに、paddingとborderの値が含まれる
* コンテンツ領域は自動的に「width - padding - border」で計算
* より予測可能なレイアウト設計が可能

## ベストプラクティス
```css
/* Modern Border Box Reset */
*, *::before, *::after {
  box-sizing: border-box;
}
```
* ほとんどのモダンなCSSフレームワークでこのリセットを採用
* レスポンシブデザインの実装が容易
* パーセンテージベースのレイアウトで特に有効
