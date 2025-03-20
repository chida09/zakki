## 書き方
- 複数行に分割する場合には、自動セミコロン挿入の落とし穴にはまらないように括弧で囲むことをおすすめします

```javascript
// 問題のあるコード（括弧なし）
const element =
  <h1>
    Hello, {formatName(user)}!
  </h1>

// JavaScriptはこのように解釈する可能性がある
const element = undefined;
<h1>
  Hello, {formatName(user)}!
</h1>;
```

- https://ja.legacy.reactjs.org/docs/introducing-jsx.html

## JSX はオブジェクトの表現である

- Babel は JSX を React.createElement() の呼び出しへとコンパイルします。
- 以下の 2 つの例は等価です

```tsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```tsx
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

- React.createElement() はバグの混入を防止するためにいくつかのチェックも行いますが、本質的には以下のようなオブジェクトを生成します：

```tsx
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

- このようなオブジェクトは “React 要素” と呼ばれます。
- これらは画面に表示したいものの説明書きとして考えることができます。
- React はこれらのオブジェクトを読み取り、DOM を構築して最新に保ちます。
- https://ja.legacy.reactjs.org/docs/introducing-jsx.html#jsx-represents-objects

以下のコードではページ上に “Hello, Sara” を表示します：
```tsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const root = ReactDOM.createRoot(document.getElementById('root'));
const element = <Welcome name="Sara" />;
root.render(element);
```

- https://ja.legacy.reactjs.org/docs/components-and-props.html#rendering-a-component

## コンポーネントのレンダーを防ぐ
- 稀なケースですが、他のコンポーネントによってレンダーされているにも関わらず、コンポーネントが自分のことを隠したい、ということがあるかもしれません。
- その場合はレンダー出力の代わりに null を返すようにしてください。
- https://ja.legacy.reactjs.org/docs/conditional-rendering.html#preventing-component-from-rendering


```tsx
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}
```

# React の流儀
https://ja.legacy.reactjs.org/docs/thinking-in-react.html

## 実装ステップ
1. UI をコンポーネントの階層構造に落とし込む
2. Reactで静的なバージョンを作成する
3. UI 状態を表現する必要かつ十分な state を決定する
4. state をどこに配置するべきなのかを明確にする
5. 逆方向のデータフローを追加する

## 1. UI をコンポーネントの階層構造に落とし込む
- 単一責任の原則 (single responsibility principle) があります
- これはすなわち、ひとつのコンポーネントは理想的にはひとつのことだけをするべきだということです。
- 将来、コンポーネントが肥大化してしまった場合には、小さなコンポーネントに分割するべきです。

## Step 2: Reactで静的なバージョンを作成する
- さて、コンポーネントの階層構造が決まったので、アプリの実装に取り掛かりましょう。
- 最初は、データモデルを受け取って UI の描画だけを行い、ユーザからの操作はできないというバージョンを作るのが、もっとも簡単でしょう。
- 表示の実装とユーザ操作の実装を切り離しておくことは重要です。
- 静的な（操作できない）バージョンを作る際には、タイプ量が多い代わりに考えることが少なく、ユーザ操作を実装するときには、考えることが多い代わりにタイプ量は少ないからです。
- 今回の静的なバージョンを作る上では一切 state を使わないでください。
- state はユーザ操作や時間経過などで動的に変化するデータを扱うために確保されている機能です。
- コンポーネントはトップダウンで作っても、ボトムアップで作っても問題ありません。
- シンプルなアプリでは通常トップダウンで作った方が楽ですが、大きなプロジェクトでは開発をしながらテストを書き、ボトムアップで進める方がより簡単です。

## Step 3: UI 状態を表現する必要かつ十分な state を決定する
- 今回のTODOリストでサンプルアプリを形作るすべてのデータについて考えてみましょう。
- 次のようなデータがあります。
  - 元となる商品のリスト
  - ユーザが入力した検索文字列
  - チェックボックスの値
  - フィルタ済みの商品のリスト
- それぞれについて見ていき、どれが state になりうるのかを考えてみます。
- 各データについて、3 つの質問をしてみましょう（YESなら、それは state ではありません）
  - 親から props を通じて与えられたデータでしょうか？
  - 時間経過で変化しないままでいるデータでしょうか？
  - コンポーネント内にある他の props や state を使って算出可能なデータでしょうか？
- というわけで、state と呼べるのは次の 2 つです。
  - ユーザが入力した検索文字列
  - チェックボックスの値



