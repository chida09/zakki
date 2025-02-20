- https://zenn.dev/akfm/books/nextjs-basic-principle

## データフェッチ on Server Components
- データフェッチはClient Componentsではなく、Server Componentsで行いましょう。
- クライアントサイドでデータフェッチを行うことは、多くの点でデメリットを伴う
  - パフォーマンスと設計のトレードオフ
    - クライアント・サーバー間の通信は、物理的距離や不安定なネットワーク環境の影響で多くの場合低速
    - REST APIにおいて通信回数を優先するとGod APIと呼ばれる責務が大きなAPIになりがち
    - 責務が小さい細粒度なAPIはChatty API(おしゃべりなAPI)と呼ばれ、通信回数が増える
    - 3rd partyライブラリの学習・責務設計・API側のセキュリティ対策など様々な開発コストが発生する
      - SWR, React Query, GraphQL, etc...
  - バンドルサイズの増加
    - 多岐にわたるコードがバンドルされクライアントへ送信される
    - クライアントサイドでデータフェッチを行うには、3rd partyライブラリ・データフェッチの実装・バリデーションなど
- App RouterはRSCをサポートしており、データフェッチはServer Componentsで行うことがベストプラクティスとされている
  - 


