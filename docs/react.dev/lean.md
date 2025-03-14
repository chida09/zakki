## コンポーネント設計
- 1回しか使わないようなUIコードやマークアップであってもOK
https://ja.react.dev/learn/your-first-component#nesting-and-organizing-components

## デフォルトエクスポート vs 名前付きエクスポート
- どちらでも良い
https://ja.react.dev/learn/importing-and-exporting-components#default-vs-named-exports

## JSXタグが複数あるときにラップしないといけない理由
- JSX は HTML のように見えますが、裏ではプレーンな JavaScript オブジェクトに変換されます。
- 関数から 2 つのオブジェクトを返したい場合、配列でラップしないといけませんよね。
- 2 つの JSX タグを返したい場合に別のタグかフラグメントでラップしないといけないのも、同じ理由です。

以下と同じ原理
```js
// これは構文エラー - 複数の値を直接返そうとしている
function example2() {
  return 42, "hello";  // カンマ演算子として解釈され、最後の値"hello"だけが返される
}

// 複数の値を返したい場合は、配列やオブジェクトでラップする必要がある
function example3() {
  return [42, "hello"]; // これは可能
}
```
- https://ja.react.dev/learn/writing-markup-with-jsx#why-do-multiple-jsx-tags-need-to-be-wrapped

## React はなぜ純粋性を重視するのか？
- 入力値が変化しない場合、レンダーをスキップすることでパフォーマンスを向上できます。
- これが問題ないのは、純関数は常に同じ出力を返すため安全にキャッシュできるからです。
- https://ja.react.dev/learn/keeping-components-pure#why-does-react-care-about-purity

## Reactではコンポーネント固有のメモリのことをstateと呼ぶ
- コンポーネントは、現在の入力値、現在の画像、ショッピングカートの状態といったものを「覚えておく」必要があります。
- https://ja.react.dev/learn/adding-interactivity#state-a-components-memory

## イベントハンドラの命名
- 慣習的に、イベントハンドラは handle の後ろにイベントの名称をつなげた名前にすることが一般的です。
- onClick={handleClick}、onMouseEnter={handleMouseEnter} などがよく見られます。
- https://ja.react.dev/learn/responding-to-events#adding-event-handlers

## イベントハンドラに渡す関数は、渡すべきなのであって呼び出すべきではない
- OK: `<button onClick={handleClick}>`
- NG: `<button onClick={handleClick()}>`
- 2つ目の例では、handleClick() の末尾に () があるため、クリックを必要とせずレンダーの際にすぐに関数を実行します。
- これは、JSX の { と }の中の JavaScript はすぐに実行されるためです。
- https://ja.react.dev/learn/responding-to-events#adding-event-handlers

## イベントハンドラのプロップの命名
- 慣習として、イベントハンドラのプロップは on で始まり、次に大文字の文字が続くようにします。
- たとえば、Button コンポーネントの props である onClick は onSmash と命名することも可能です
- https://ja.react.dev/learn/responding-to-events#naming-event-handler-props

## React アプリでの画面更新は、3つのステップで行われる
1.レンダーのトリガ, 2.コンポーネントのレンダー, 3.DOM へのコミット

- 1.レンダーのトリガ（開始のきっかけ）
  - 2つの場合に発生します
  - コンポーネントの初回レンダー（アプリケーション起動時）
  - コンポーネントまたはその親の state が更新された時
- コンポーネントのレンダー（React によるコンポーネントの呼び出し）
  - React がコンポーネントを呼び出して、画面に表示すべき内容を決定します
- DOM へのコミット（実際の画面への反映）
  - 初回レンダー時：必要な DOM ノードをすべて作成
  - 再レンダー時：前回との差分のみを必要最小限の操作で更新
- この後、ブラウザが実際の画面の描画（ペイント）を行う
- https://ja.react.dev/learn/render-and-commit

## 「レンダーする」とは
- React があなたのコンポーネント（関数）を呼び出すということです。
- 関数から返される JSX は、その時点での UI のスナップショットのようなものです。
- その JSX 内の props、イベントハンドラ、ローカル変数はすべて、レンダー時の state を使用して計算されます。
- https://ja.react.dev/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time

## React ではなぜ state の変更が非推奨なのか？
- 最適化：React の一般的な最適化戦略（useMemo）は、前の props や state が次のものと同一である場合作業をスキップする、ということに依存しています。
- state を書き換えないことで、変更があったかどうかを非常に素早くチェックすることができます。prevObj === obj であれば、内部で何も変更されていないと自信を持って言えるようになります。
- https://ja.react.dev/learn/updating-objects-in-state#why-is-mutating-state-not-recommended-in-react
- スプレッド構文は「浅い」、つまり 1 レベルのみのコピーを行う。

## コンポーネントが多くの視覚状態を持つ場合
- それらをすべて 1 つのページに表示することが便利な場合があります。
```js
let statuses = [
  'empty',
  'typing',
  'submitting',
  'success',
  'error',
];
```
- https://ja.react.dev/learn/reacting-to-input-with-state#displaying-many-visual-states-at-once

## props を state にコピーしない
- 親コンポーネントから渡された props と同期されなくなってしまう
- state は最初のレンダー時にのみ初期化されるため
- https://ja.react.dev/learn/choosing-the-state-structure#don-t-mirror-props-in-state

## メモリ使用量の改善
- 理想的には、削除された場所アイテム（およびその子アイテム！）自体も「テーブル」オブジェクトから削除して、メモリ使用量を改善するとよいでしょう。
https://ja.react.dev/learn/choosing-the-state-structure#improving-memory-usage
- 常にコンポーネント関数はトップレベルで宣言し、定義をネストしないようにしてください。
- https://ja.react.dev/learn/preserving-and-resetting-state

## state をリセットする方法は、2 つあります。
- 1. コンポーネントを異なる位置でレンダーする
  - 条件式でComponentを切り替える
  - state は DOM から削除されるたびに破棄される
- 2. key を使って各コンポーネントに明示的な識別子を付与する
  - Componentに異なるkeyの値を指定する
- https://ja.react.dev/learn/preserving-and-resetting-state


## 削除されたコンポーネントの state の保持
- state をリフトアップすることで、親コンポーネントで保持することができます。
- この方法では、重要な情報を保持しているのは親の方なので、子コンポーネントが削除されても問題ありません。
https://ja.react.dev/learn/preserving-and-resetting-state#preserving-state-for-removed-components


## リデューサ関数の例
```tsx
export default function TaskApp() {
  const [tasks, setTasks] = useState(initialTasks);

  function handleAddTask(text) {
    setTasks([
      ...tasks,
      { id: nextId++, text, done: false },
    ]);
  }

  function handleChangeTask(task) {
    setTasks(
      tasks.map((t) => {
        if (t.id === task.id) return task;
        return t;
      })
    );
  }

  function handleDeleteTask(taskId) {
    setTasks(tasks.filter((t) => t.id !== taskId));
  }

  return (
    <>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}
```

```ts
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [
        ...tasks,
        { id: action.id, text: action.text, done: false },
      ];
    }
    case 'changed': {
      return tasks.map((t) => {
        if (t.id === action.task.id) return action.task;
        return t;
      });
    }
    case 'deleted': {
      return tasks.filter((t) => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}
```
- https://ja.react.dev/learn/extracting-state-logic-into-a-reducer

## なぜリデューサと呼ばれるのか？
- リデューサによりコンポーネント内のコード量を「削減 (reduce)」することもできますが、
- 実際にはリデューサは配列で行うことができる reduce() という操作にちなんで名付けられています。
- https://ja.react.dev/learn/extracting-state-logic-into-a-reducer#why-are-reducers-called-this-way

## コンテクストを使う前に
- コンテクストを使う前に検討すべきいくつかの代替案があります。
- 1. まずは props を渡す方法から始めましょう
- 2. コンポーネントを抽出して、children を JSX として渡す方法を検討しましょう
  - もし、何らかのデータを、それを必要とせずただ下に流すだけの中間コンポーネントを何層も経由して受け渡ししているような場合、何かコンポーネントを抽出するのを忘れているということかもしれません。
  - たとえば、<Layout posts={posts} /> のような形で、データを直接使わないビジュアルコンポーネントに post のようなデータを渡しているのかもしれません。
  - 代わりに、Layout は children を props として受け取るようにし、<Layout><Posts posts={posts} /></Layout> のようにレンダーしてみましょう。
  - これにより、データを指定するコンポーネントとそれを必要とするコンポーネントの間のレイヤ数が減ります。
- https://ja.react.dev/learn/passing-data-deeply-with-context#before-you-use-context

## コンテクストのさまざまな用途
- テーマ：例えばダークモードのような
- 現在のアカウント：現在ログインしているユーザの情報
- ルーティング
- state 管理
- https://ja.react.dev/learn/passing-data-deeply-with-context#use-cases-for-context

## ref で値を参照する
- state の値を変更すると画面が更新されます
- ref の値を変更しても画面は更新されません
- ノードにフォーカスを当てたり、スクロールさせたり、サイズや位置を測定したりするなどの場合に使う
- Reactにはこれらを行う組み込みの方法が存在しないため、DOMノードを参照するrefが必要になる
- https://ja.react.dev/learn/escape-hatches

## エフェクトを使って同期を行う
- エフェクト (Effect) を使うことで、レンダー後にコードを実行することができます
- https://ja.react.dev/learn/escape-hatches#synchronizing-with-effects
- エフェクトは React のパラダイムからの避難ハッチです。React の外に「踏み出して」、何らかの外部システムと同期させることができるものです。
- 外部システムが関与していない場合（例えば、props や state の変更に合わせてコンポーネントの state を更新したい場合）、エフェクトは必要ありません。

## ref と state の違い
- カウンタボタンの例
  - count 値は表示されるものなので、state を使うのが適切です。
  - カウンタの値が setCount() でセットされると、React はコンポーネントを再レンダーし、画面が新しいカウントを反映するように更新されます。
  - もしこれを ref で実装しようとしても、React はコンポーネントを再レンダーしないため、カウントの変更は一切反映されません
- https://ja.react.dev/learn/referencing-values-with-refs#differences-between-refs-and-state

## React が ref をアタッチするタイミング
- React では、すべての更新は 2 つのフェーズに分けて行われます。
  - レンダー中に、React はコンポーネントを呼び出して画面に表示される内容を決定する。
  - コミット中に、React は DOM に変更を適用する。
- 一般的に、レンダー中に ref にアクセスすることは望ましくありません。
- 通常、ref にアクセスするのはイベントハンドラからです。
https://ja.react.dev/learn/manipulating-the-dom-with-refs#when-react-attaches-the-refs

## エフェクトのライフサイクル
- 画面に追加されたとき、コンポーネントはマウントされます。
- （大抵はインタラクションに応じて）新しい props や state を受け取ったとき、コンポーネントは更新されます。
- 画面から削除されたとき、コンポーネントはアンマウントされます。
- これは、コンポーネントの考え方としては良いですが、エフェクトの考え方としては良くありません。
- エフェクトは、現在の props や state に外部システムをどのように同期させるのかを記述します。
https://ja.react.dev/learn/lifecycle-of-reactive-effects#the-lifecycle-of-an-effect

## エフェクトが再同期できることを React はどのように確認するのか
- 開発環境(development mode)でのみ、useEffectは意図的に2回実行される。
- 例: https://codesandbox.io/p/sandbox/r3pxtv?file=%2Fsrc%2FApp.js%3A11%2C31

```ts
useEffect(() => {
  const fetchUsers = async () => {
    console.log("fetchUsers"); // fetchUsersが2回出る

    try {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/users"
      );
      if (!response.ok) throw new Error("データの取得に失敗しました");
      const data = await response.json();
      setUsers(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  fetchUsers();
}, []);
```

- 2つのログは開発時のみ表示されます。開発時には、React は常に各コンポーネントを 1 度再マウントします。
- 開発時には、React はエフェクトを即座に再同期させて、エフェクトの再同期が正しく行われることを確認します。
- この動作は、ドアの鍵が正しくかかるか確認するために、ドアを 1 度余分に開け閉めしてみることに似ています。
- ReactのStrictModeをOFFにすると、開発環境でも1回のログ出力に変わる
- https://ja.react.dev/learn/lifecycle-of-reactive-effects#how-react-verifies-that-your-effect-can-re-synchronize

## エフェクトはリアクティブ (reactive) な値に “反応” する
- 以下の例では、エフェクトが 2 つの変数 (serverUrl と roomId) を利用していますが、依存配列には roomId のみが指定されています。

```ts
const serverUrl = 'https://localhost:1234';

function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.connect();
    return () => {
      connection.disconnect();
    };
  }, [roomId]);
  // ...
}
```

- なぜ serverUrl は依存配列に追加しなくて良いのでしょうか？
- これは、再レンダーが起こっても、決して serverUrl が変化することはないからです。
- したがって、依存配列に追加しても意味がありません。
- 結局のところ、指定する依存値は、時間によって変化して初めて意味があるのです！
- https://ja.react.dev/learn/lifecycle-of-reactive-effects#effects-react-to-reactive-values

## リアクティブな値とリアクティブなロジック
- コンポーネントの本体部分で宣言された props、state および変数のことをリアクティブな値 (reactive value) と呼びます。
- リアクティブな値は、useEffectの依存配列に含める必要があります。
- イベントハンドラ内のロジックはリアクティブではない。
  - ユーザが同じ操作（クリックなど）を再度行わない限り、再度実行されることはない。
  - イベントハンドラは値の変化に「反応」することなく、リアクティブな値を読み取ることができる。
- エフェクト内のロジックはリアクティブである。
  - フェクトがリアクティブな値を読み取る場合、依存配列としてそれを指定する必要がある。
  - その後再レンダーによって値が変化した場合、React は新しい値でエフェクトのロジックを再実行する。
- https://ja.react.dev/learn/separating-events-from-effects#reactive-values-and-reactive-logic

## エフェクトイベントの宣言
- useEffectEvent という特別なフックを使うことで、エフェクトからこの非リアクティブなロジックを分離することができます。
- https://ja.react.dev/learn/separating-events-from-effects#declaring-an-effect-event

## オブジェクトからプリミティブ値を読み取る
- props からオブジェクトを受け取ることがあります。

```ts
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
    return () => connection.disconnect();
  }, [options]); // ✅ All dependencies declared
  // ...
```

- しかし、親コンポーネントがオブジェクト作成をレンダー中に行っているかもしれないという心配があります。
```tsx
<ChatRoom
  roomId={roomId}
  options={{
    serverUrl: serverUrl,
    roomId: roomId
  }}
/>
```

- これにより、親コンポーネントの再レンダーのたびに、エフェクトによる再接続が発生してしまいます。
- これを修正するには、エフェクトの外側でオブジェクトから情報を読み取っておき、オブジェクトや関数自体を依存値として持たせないようにします。

```ts
function ChatRoom({ options }) {
  const [message, setMessage] = useState('');

  const { roomId, serverUrl } = options;
  useEffect(() => {
    const connection = createConnection({
      roomId: roomId,
      serverUrl: serverUrl
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]); // ✅ All dependencies declared
  // ...
```
- ロジックは少し繰り返しになります（エフェクト外でオブジェクトから値を読み取り、エフェクト内で同じ値を持つオブジェクトを作成している）。
- しかし、エフェクトが実際に依存している情報が何なのかが、非常に明確になります。

https://ja.react.dev/learn/removing-effect-dependencies#read-primitive-values-from-objects

## レンダー中に呼び出されるすべての関数を use プレフィックスで始めるべきか？
- 関数の内部で 1 つ以上のフックを使用している場合は、use プレフィックスを付ける（つまりフックにする）必要があります。
- 原理上は、他のフックを呼び出さないフックを作成することは可能です。
- 混乱を招き余計な制限が加わるため、このようなパターンは避けるのが賢明です。
- こうすれば、コンポーネントはこのコードを条件分岐内で呼び出すことができなくなります。中でフック呼び出しを実際に追加したときに、このことが重要になります。
- https://ja.react.dev/learn/reusing-logic-with-custom-hooks#should-all-functions-called-during-rendering-start-with-the-use-prefix
