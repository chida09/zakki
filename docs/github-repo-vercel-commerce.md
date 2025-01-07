# 分かったこと
## リポジトリ構成

- app
- components
  - grid
    - index.tsx（コンポーネントが`Grid`の場合はulタグ、`GridItem`の場合はliタグで使い分けている）
  - layout
    - footer.tsx（Suspenseを利用）
- lib
  - shopify
    - index.ts（`isShopifyError`でcatch句の条件判定を使い分けている）
    - types.ts（型定義）
  - constants.ts
  - type-guards.ts
  - utils.ts

## React.ComponentProps
残余引数のpropsに`React.ComponentProps<typeof Image>`を指定することで、`props.src`を扱うことができる

```tsx
export function GridTileImage({
  isInteractive = true,
  ...props
}: {
  isInteractive?: boolean;
} & React.ComponentProps<typeof Image>) {
  {props.src}
}
```


# 後で調べたい構文など
## app/layout.tsx
```ts
import { cookies } from 'next/headers';
```

## components/cart

components/cart/cart-context.tsx  
```ts
import { useOptimistic } from 'react';
```

components/layout/navbar/index.tsx  
Suspenseの使い方
