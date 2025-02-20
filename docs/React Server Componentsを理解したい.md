- https://zenn.dev/yuu104/articles/react-server-component

## CSR（Client Side Rendering）の復習

React では CSR 戦略が用いられてきました。  
CSR では、クライアントが受け取るのは次のような中身のない空の HTML ファイルでした。

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="root"></div>
    <script src="/static/js/bundle.js"></script>
  </body>
</html>
```

bundle.js には、React アプリケーションを構築するために必要なものが全て含まれています。  
  
JS がダウンロードされて解析が完了すると、React が動作し始め、アプリケーション全体のすべての DOM ノードを呼び出し、それを空の <div id="root"> に格納します。  
  
### 処理の流れ  
1. クライアントからWebサーバーへリクエストが送られる
1. Webサーバーから空の HTML と共に、CSS、JS が送信される
1. JS を実行して初期レンダリング
1. 必要なデータは API を叩いてフェッチする
1. データ取得後、際レンダリング

![画像1](/img//docs/react-server-component-1.png)

### 問題点
とてもシンプルではありますが、CSR にはJS バンドルが大きく、UI の初期表示に時間がかかる という問題があります。  
UI が表示されるまでの間、ユーザーは白い画面を見続けることになります。  

## Web ページのパフォーマンス指標
Web ページのパフォーマンス指標として、以下があります。
  
- FCP（First Contentful Paint）
  - ブラウザが最初のテキストや画像などのコンテンツを描画するまでの時間
  - 真っ白な画面ではなく、何かしらの表示（レイアウトなど）がある状態
- LCP（Largest Contentful Paint）
    - ページの主要なコンテンツが表示されるまでの時間
    - ユーザが関心のあるコンテンツが含まれている
    - DB からデータを取得し、UI にレンダリングされた状態
- TTI（Time To Interactive）
  - ページが完全にインタラクティブになるまでの時間
  - React がダウンロードされ、アプリケーションがレンダリングされ、ハイドレーションが行われる
  - ページ上の UI コンポーネントが反応し始め、ユーザーが入力できる状態

CSR では、FCP が遅くなってしまうのが問題です。そして、この問題を解決するのが SSR です。  

## SSR の復習
- 空の HTML ファイルを送信する代わりにサーバー側で初期の HTML を生成し、ハイドレーション用の JS と共にクライアントへ送信します。
- HTML には CSR と同様に `<script>` タグが含まれており、その JS でハイドレーションが行われ、サイトがインタラクティブな状態になります。
  
SSR では初期ロードの時点である程度のコンテンツが表示されるため、バンドルされた JS がダウンロードされ解析されている間でも、ユーザーは真っ白なページを見続ける必要はありません。そのため、CSR と比べて FCP が改善します。  

### 処理の流れ

1. クライアントからリクエストが送られる
1. 初期表示に必要なデータをサーバー側で API コール
1. API からのレスポンスによりデータを取得
1. サーバー側でレンダリングを行い、HTML を生成
1. HTML、CSS、JS をクライアントに送信
1. クライアントは HTML を表示し、ロードした JS を実行してハイドレードする

![画像2](/img//docs/react-server-component-2.png)

### SSR の魅力
SSR ではプリレンダリングにより FCP が改善されています。  
真っ白な画面ではなく、何かしらの表示（レイアウトなど）がある状態を素早く提供できるようになりました。  
  
しかし、必要なデータをクライアント側で取得している状態だと、LCP は改善されません。  
ユーザーはローディング画面を見るためにサイトにアクセスしているのではありません。ユーザーが望むのは、DB から取得した情報が表示されている UI です。  
  
Next.js をはじめとするフレームワークでは、この問題を解決するためにサーバー側でのデータ取得を可能にしています。
  
```tsx
// pages/products.js
import axios from "axios";

function Products({ products }) {
  return (
    <div>
      <h1>Available Products</h1>
      <ul>
        {products.map((product) => (
          <li key={product.id}>{product.name}</li>
        ))}
      </ul>
    </div>
  );
}

export async function getServerSideProps() {
  const res = await axios.get("https://api.example.com/products");
  const products = res.data;
  return {
    props: { products }, // will be passed to the page component as props
  };
}

export default Products;
```
  
getServerSideProps はサーバー上で実行されます。この関数が実行された結果が props としてコンポーネントに渡り、プリレンダリングが開始します。  
これにより、でユーザーが関心のあるコンテンツが初期表示時に含まれているため、LCP の問題が改善されます。  
  
### ハイドレーションとは
- インタラクティブ性の追加
  - サーバー側で生成された HTML に対し、イベントハンドラーやその他のスクリプトを結び付けます。
  - これにより、静的なページがインタラクティブなアプリケーションとして機能するようになります。
- 仮想ＤＯＭの生成と実ＤＯＭの同期
  - サーバーサイドでレンダリングされたコンテンツと、クライアントサイドでレンダリングされたコンテンツが同一か確認します。
  - 結果が一致する場合はクライアントサイドでのレンダリングをスキップ、不一致の場合はクライアントサイドで再度レンダリングをします。
  - ブラウザ専用APIをロジックに含ませたことが原因でハイドレーションエラーが起こることもあります。
  - 例えばcookieを使用して、コンポーネントの表示の有無のロジックを書いていた場合、プリレンダリング時はcookieが取得できないので、クライアント側が想定する結果と不一致になるケースがあります。
  - 対策として、useEffectを使いマウント後にcookieを取得するようにすることが挙げられます。

### SSR の問題点
SSR により、CSR が抱える問題を改善できましたが、まだ問題は残っています。
- ページ単位という制限
  - SSR では、サーバー側でのデータ取得とレンダリングがページ単位でしか機能しません
  - そのため、データ取得などが原因でサーバー側の処理が重くなると、プリレンダリングに時間がかかります
- 常にクライアント上でハイドレーションを行う
  - SSR を使用しても、最終的にはクライアント上で JS によるハイドレーションが必要です
  - 全てのコンポーネントは、たとえ不要な場合でも実 DOM との比較のために仮想 DOM の構築処理が行われます

## React Server Components とは？
- SC とは、サーバー側でのみ実行されるコンポーネントです。
- サーバー側でのみ実行されるので、JS バンドルには含まれません。
- そのため、SSR の問題点であった「常にクライアント上でハイドレーションを行う」を解決できます。
- そのため、コンポーネント上から直接外部 API のデータを取得してレンダリングすることが可能になり、
- コンポーネント単位でのデータ取得＆レンダリングをサーバー側で行うことができます。
- これは、SSR の問題点である「ページ単位という制限」を解決しています。

```tsx
export default async function ServerComponent() {
  const res = await fetch("https://example.com/posts");
  const posts = await res.json();

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map(() => (
          <li>
            <a href={post.url}>{post.title}</a>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### クライアントコンポーネント（CC）
- SC ではない、従来の React コンポーネントのことです。
- SC と区別するために、これまでのコンポーネントを CC と呼ぶようになりました。
- そのため、クライアント上でのみ実行されるコンポーネントのことではありません。
- SSR の場合、CC はサーバー側でも実行されるため、クライアントとサーバーの両方で実行できます。

### RSC を採用したレンダリングプロセス
- RSC のレンダリングは、2 つの段階（stage0 と stage1）に分けて考えることができます。
- stage0 : SC のレンダリング
- stage1 : CC のレンダリング
- 最初に stage0 である SC がサーバー側で実行され、その後 stage1 である CC がレンダリングされます。
- stage0 → stage1 の順で段階的に実行されます。

```tsx
// Server Component
async function Notes() {
  const notes = await db.notes.getAll();
  return (
    <div>
      {notes.map((note) => (
        <Expandable key={note.id}>
          <p note={note} />
        </Expandable>
      ))}
    </div>
  );
}

// Client Component
export default function Expandable({ children }) {
  const [expanded, setExpanded] = useState(false);
  return (
    <div>
      <button onClick={() => setExpanded(!expanded)}>Toggle</button>
      {expanded && children}
    </div>
  );
}
```

まず初めに、stage0 を実行して、SC をレンダリングします。

```tsx
<div>
  <Expandable key={1}>
    <p>this is the first note</p>
  </Expandable>
  <Expandable key={2}>
    <p>this is the second note</p>
  </Expandable>
  <!-- 省略 -->
</div>
```

次にstage1 を実行して CC をレンダリングします。

```tsx
<div>
  <div>
    <button onClick={イベントハンドラ}>Toggle</button>
    <p>this is the first note</p>
  </div>
  <div>
    <button onClick={イベントハンドラ}>Toggle</button>
    <p>this is the second note</p>
  </div>
  <!--...-->
</div>
```

- これですべてのコンポーネントのレンダリングが完了し、HTMl タグに変換されました。
- このように、RSC におけるレンダリングは「多段階計算」と捉えることができます。

### RSC の制約
- CC は SC をインポートできない  
- これまでの内容を理解していれば、当然ですよね。  
- CC のレンダリングは SC のレンダリング後に行われます。
- そのため、CC をレンダリングする段階では既に SC は存在せず、ただの HTML 要素に変換されています。

### RSC のメリット
- JS のバンドルサイズの削減
- データフェッチスピードの高速化
- 初期表示の改善
- セキュリティ
- 設計面のメリット
