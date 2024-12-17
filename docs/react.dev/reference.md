## API
### act
- React のテストヘルパーで、コンポーネントの更新を適用してからアサーションを行うために使用します
- ブラウザでの React の実際の動作により近いテストを実現できます
- 主なユースケース
  - テスト内でコンポーネントのレンダリング
  - テスト内でイベントのディスパッチ（ユーザーの操作、クリックやキー入力などを人工的に発生させること）
- https://ja.react.dev/reference/react/act

### forwardRef
- 今までの React では ref をコンポーネントの Props として渡す際に、forwardRef を使ってラップする必要がありました。
- しかし、React v19 からは forwardRef が不要になり、ref を直接渡すことができるようになりました。

```tsx
// React v18 まで
const Button = React.forwardRef(({ onClick }, ref) => {
  return <button ref={ref} onClick={onClick} />;
});

// React v19 から
const Button = ({ onClick, ref }) => {
  return <button ref={ref} onClick={onClick} />;
};
```

- https://azukiazusa.dev/blog/shorts/53WOkRFJq2m0UvpiEH4RV8/
- https://ja.react.dev/reference/react/forwardRef

### lazy
- lazy を使うことで、あるコンポーネントが初めてレンダーされるまで、そのコードの読み込みを遅延させることができます。

```ts
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

- このパターンを用いる場合は、遅延インポートしようとしているコンポーネントが default でエクスポートされている必要があります。
- コンポーネントのコードがオンデマンドで読み込まれるようになったので、読み込みの最中には何を表示するべきかを指定する必要があります
- これは、遅延コンポーネントまたはその親のいずれかを <Suspense> バウンダリでラップすることで行うことができます。
- https://ja.react.dev/reference/react/lazy

## memo
- React は通常、親コンポーネントが再レンダーされると常にコンポーネントを再レンダーします。
- memo を使用すると、新しい props が古い props と同じである限り、親が再レンダーされても React によって再レンダーされないコンポーネントを作成できます。

```ts
import { memo } from 'react';

const SomeComponent = memo(function SomeComponent(props) {
  // ...
});
```

- https://ja.react.dev/reference/react/memo

### props の変更を可能な限り減らす
- memo を使用すると、コンポーネントは props のいずれかが浅い (shallow) 比較で前回と等しくない場合に再レンダーされます。
- つまり、React はコンポーネントのすべての props を前回の値と Object.is を使用して比較します。
- Object.is(3, 3) は true ですが、Object.is({}, {}) は false です。
- https://ja.react.dev/reference/react/memo#minimizing-props-changes
