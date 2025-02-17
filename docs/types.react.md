## 一覧

```ts
import type * as React from "react"

// C
export type ComponentProps = React.ComponentProps<'div'>

export type ComponentRef =  React.ComponentRef<'div'>

// E
export type ElementType = React.ElementType

// F
type Props = { name: string }
export type FC = React.FC<Props>

export type FocusEvent =  React.FocusEvent

export type FunctionComponent = React.FunctionComponent

// I
export type IntrinsicElements = keyof React.JSX.IntrinsicElements

// J
export type JSXElementConstructor =  React.JSXElementConstructor<any>

// R
React.Ref

export type ReactElement =  React.ReactElement

export type ReactNode = React.ReactNode
```

## ComponentProps

```tsx
import React from 'react';

type InputProps = React.ComponentProps<'input'>;

// カスタム入力コンポーネント
// 標準のinput要素のすべてのプロパティを継承し、独自のスタイリングを追加
const CustomInput = ({ className, ...props }: InputProps) => {
  return (
    <input
      className={`border rounded-md px-3 py-2 ${className}`}
      {...props}
    />
  );
};

// 使用例
const ExampleForm = () => {
  return (
    <div>
      <CustomInput
        type="text"
        placeholder="お名前を入力"
        onChange={(e) => console.log(e.target.value)}
        required
      />
    </div>
  );
};
```

- https://react-typescript-cheatsheet.netlify.app/docs/react-types/componentprops/

## ComponentRef

```tsx
import React, { useRef } from 'react';

const MyForm = () => {
  // refの型が正しく推論される
  const inputRef = useRef<ComponentRef<'input'>>(null);

  const focusInput = () => {
    // inputRef.current は HTMLInputElement 型
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </div>
  );
};
```

## ElementType

ElementType は React で特定のプロパティを受け入れることができるコンポーネントの型を取得するためのユーティリティ型です。
主な使用パターンを見てみましょう。

1. 特定のプロパティを受け入れるコンポーネントを探す
```typescript
// src プロパティを受け入れるすべてのコンポーネント
type SrcComponents = ElementType<{ src: string }>;
// img, video, audio などの src を持つ要素の型が含まれる
```

2. すべてのコンポーネント型を取得
```typescript
// すべての可能なコンポーネント
type AllComponents = ElementType;
```

3. 特定のタグに絞り込む
```typescript
// media 関連の要素のみに制限
type MediaComponents = ElementType<{}, 'audio' | 'video' | 'img'>;
```

実践的な使用例：

```typescript
import React from 'react';

// src プロパティを必須とするコンポーネントを作る
function MediaWrapper<T extends ElementType<{ src: string }>>(
  props: {
    as: T,
    src: string
  }
) {
  const Component = props.as;
  return <Component src={props.src} />;
}

// 使用例
function App() {
  return (
    <div>
      <MediaWrapper as="img" src="/image.png" />
      <MediaWrapper as="video" src="/video.mp4" />
      {/* TypeErrorになる（buttonはsrcを受け付けない） */}
      {/* <MediaWrapper as="button" src="something" /> */}
    </div>
  );
}
```

ElementType の主な利点：

1. 型安全性：
- 指定したプロパティを確実に持つコンポーネントのみを許可
- 不適切なコンポーネントの使用を防止

2. 柔軟性：
- カスタムコンポーネントと組み込みHTML要素の両方に対応
- プロパティによるフィルタリングが可能

3. 汎用性：
- ジェネリックコンポーネントの作成に活用可能
- コンポーネントの再利用性を高める

この型は特に、汎用的なコンポーネントを作成する際や、特定のプロパティを必要とするコンポーネントを扱う際に非常に有用です。

## FC
- https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/function_components/
