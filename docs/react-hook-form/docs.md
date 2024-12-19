## useForm
- フォームを管理するためのカスタムフック
- https://react-hook-form.com/docs/useform

### mode
- `mode: 'onChange'`
  - 入力値が変更されるたびにバリデーションが実行される
  - リアルタイムでエラーフィードバックを提供

```ts
const form = useForm<InputVariables>({
  defaultValues,
  mode: 'onChange',
  resolver: zodResolver(schema),
});
```

### values
- 外部の状態やサーバーデータによってフォームの値を動的に更新する必要がある場合に使用
- resetOptionsが設定されていない限り、valuesプロパテ ィはdefaultValuesプロパテ ィを上書きします

## getValues
- フィールドの値を取得する
- https://react-hook-form.com/docs/useform/getvalues
- watchとの主な違い
  - getValues
    - 送信時やボタンクリック時など、特定のタイミングでの値を取得する時に有効
    - パフォーマンスが重要で、再レンダリングを避けたい場合に使う
  - watch
    - 値の変更をリアルタイムで監視する
