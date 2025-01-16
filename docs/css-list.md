# フレックスボックス（flex）
- フレックスボックスは、アイテムを列方向や行方向など一次元に並べて表示するためのレイアウトモデル
- フレックスボックスはでは主軸や交差軸上の配置を指定することができる

## フレックスコンテナー
- display: flex または display: inline-flex を設定することで、その要素（親要素）がフレックスコンテナーになる
  - display: flex ブロックレベルのFlexコンテナーを作成（前後に改行が入る）
  - display: inline-flex インラインレベルのFlexコンテナーを作成（横に並べられる）
- フレックスボックスを使用してレイアウトされた文書の領域をフレックスコンテナーと呼ぶ
  - フレックスコンテナーを作成するには、その領域の display プロパティを flex に設定する

https://developer.mozilla.org/ja/docs/Web/CSS/CSS_flexible_box_layout

### justify-content
- フレックスコンテナーの中で、アイテムをどのように配置・間隔を取るかを決めます
- デフォルトでは横方向（左右）の配置を制御します

### align-items
- フレックスコンテナーの中で、アイテムを上下どの位置に配置するかを決めます
- デフォルトでは縦方向（上下）の配置を制御します

以下の場合、上下左右の中央に配置される

```css
.container {
  display: flex;
  align-items: center;
  justify-content: center;
}
```

## フレックスアイテムの折り返し
- フレックスボックスは一次元のレイアウトとして設計されており、アイテムを単一の行または列として扱います
- flex-wrap プロパティの初期値は nowrap です
- つまり、一連のフレックスアイテムがフレックスコンテナーと比べて幅が広くなると、はみ出してしまいます
- これを折り返すには `flex-wrap: wrap;` を指定します

https://developer.mozilla.org/ja/docs/Web/CSS/CSS_flexible_box_layout/Mastering_wrapping_of_flex_items

### flex-direction
- フレックスコンテナー内のアイテムをどの方向に並べるかを指定するプロパティ
- 文字を横並びから縦並びにしたい時などに使う

https://developer.mozilla.org/ja/docs/Web/CSS/justify-content

# grid
- 2次元レイアウトをHTML/CSS を使って自由に操作できる機能
- display: grid; または display: inline-grid; を指定することで、その要素は Grid Layout のコンテナになる

https://developer.mozilla.org/ja/docs/Glossary/Grid

# vertical-align
- インライン要素のみで利用できる
- 主な使用例は以下
  - テキストの中に画像やアイコンを配置する場合
    - テキストの途中でアイコンがある場合、中央揃えなのか、上方向に揃えるのか決める
  - テーブルのセルでテキストの上下位置を制御する場合
    - 横並びのテーブルのセルでテキストが1行だったり3行だったりする時に、中央揃えなのか、上方向に揃えるのか決める
