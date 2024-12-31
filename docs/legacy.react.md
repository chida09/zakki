React では、イベントを表す props には on[Event] という名前、イベントを処理するメソッドには handle[Event] という名前を付けるのが慣習となっています。

https://ja.legacy.reactjs.org/tutorial/tutorial.html

リストが再レンダーされる際、React はそれぞれのリスト項目の key について、前回のリスト項目内に同じ key を持つものがないか探します。もし以前になかった key がリストに含まれていれば、React はコンポーネントを作成します。もし以前のリストにあった key が新しいリストに含まれていなければ、React は以前のコンポーネントを破棄します。もし 2 つの key がマッチした場合、対応するコンポーネントは移動されます。key はそれぞれのコンポーネントの同一性に関する情報を React に与え、それにより React は再レンダー間で state を保持できるようになります。もしコンポーネントの key が変化していれば、コンポーネントは破棄されて新しい state で再作成されます。

key はグローバルに一意である必要はありません。コンポーネントとその兄弟の間で一意であれば十分です

https://ja.legacy.reactjs.org/tutorial/tutorial.html#picking-a-key


複数行に分割する場合には、自動セミコロン挿入の落とし穴にはまらないように括弧で囲むことをおすすめします

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

https://ja.legacy.reactjs.org/docs/introducing-jsx.html

JSX はオブジェクトの表現である

Babel は JSX を React.createElement() の呼び出しへとコンパイルします。
以下の 2 つの例は等価です：

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

React.createElement() はバグの混入を防止するためにいくつかのチェックも行いますが、本質的には以下のようなオブジェクトを生成します：

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

このようなオブジェクトは “React 要素” と呼ばれます。これらは画面に表示したいものの説明書きとして考えることができます。React はこれらのオブジェクトを読み取り、DOM を構築して最新に保ちます。

https://ja.legacy.reactjs.org/docs/introducing-jsx.html#jsx-represents-objects