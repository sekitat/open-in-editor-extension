# Open in editor extension

To install dependencies:

```bash
bun install
```

`.env` に webpack devServer の PORT 番号（3035）を設定する。

To run:

```bash
bun run index.ts
```

`dist` にファイル一式が出力されます。

## launch-editor, cors middleware を追加する

```sh
$ yarn add cors launch-editor-middleware
```

`cors`, `launch-editor-middleware` をインストールして `webpack.dev.js` の devServer に `middleware`を追加する。

```js
const cors = require('cors');
const openInEditor = require('launch-editor-middleware')

...
  devServer: {
    ...
    setupMiddlewares: (middlewares) => {
      middlewares.unshift({
        middleware: cors()
      },
      {
        path: '/__open-in-editor',
        middleware: openInEditor()
      })

      return middlewares;
    }
```

## リクエスト先を port 3035 に設定する

VueDevTools の open in editor はデフォルトでポート 3000 番にリクエストを送るので、リクエスト先を Webpack DevServer の `http://localhost:3035/` に向ける設定を `app/javascript/packs/utility/create_vue_instance.ts` に追記する

```ts
if (process.env.NODE_ENV !== "production") {
  // App served on port 3000
  // Webpack devServer served on port 3035
  // @ts-ignore
  window.VUE_DEVTOOLS_CONFIG = {
    openInEditorHost: "http://localhost:3035/",
  };
}
```

devServer を再起動する

## Chrome に拡張機能を読み込む

`dist` フォルダにファイル一式がビルドされているので、Chrome > 拡張機能 > 「パッケージ化されていない拡張機能を読み込む」から dist を選択

## rails Routes を IndexedDB に保存する

1. 拡張機能の pin を固定して、「設定」から rails routes を出力したものをコピペして保存する。
2. devTools を開き直して、devTools の アプリケーション > IndexedDB > routes_db に routes が保存されていることを確認してください。

## 動かない場合

- 拡張機能の読み込みをし直した後は画面のリロードをしてください。
- API の一覧の取得は devTools が開いていないと動きません。devTools を開き直してください。
- Chrome 拡張機能画面でエラーが出ている可能性があります。
  パッケージを一度削除して、「パッケージ化されていない拡張機能を読み込む」から再度読み込みを行ってください。

This project was created using `bun init` in bun v1.0.11. [Bun](https://bun.sh) is a fast all-in-one JavaScript runtime.
