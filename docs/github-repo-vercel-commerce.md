# 分かったこと
リポジトリ構成

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

# 後で調べたい構文など
- `import { cookies } from 'next/headers';`

# 調べたこと

## ComponentProps

- 本来のタグとして振る舞ってほしいコンポーネントの場合に使う

```tsx
<LogoIcon width="64" height="58" fill="white" />
```

```tsx
import clsx from 'clsx';

export default function LogoIcon(props: React.ComponentProps<'svg'>) {
  return (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      aria-label={`${process.env.SITE_NAME} logo`}
      viewBox="0 0 32 28"
      {...props}
      className={clsx('h-4 w-4 fill-black dark:fill-white', props.className)}
    >
      <path d="M21.5758 9.75769L16 0L0 28H11.6255L21.5758 9.75769Z" />
      <path d="M26.2381 17.9167L20.7382 28H32L26.2381 17.9167Z" />
    </svg>
  );
}
```

https://github.com/chida09/nextjs-commerce/blob/main/components/icons/logo.tsx#L3

## suppressHydrationWarning

