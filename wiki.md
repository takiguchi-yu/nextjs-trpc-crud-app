## Next.js を Monorepo としてセットアップする

新規でプロジェクトを作成する

```bash
npx create-next-app --example with-tailwindcss nextjs-trpc-crud-app
```

src フォルダーを作成します。プロジェクトを monorepo に変換するには、pages、styles、および public フォルダーを、新しく作成した src フォルダーに移動します。

### Prettier を導入する

```bash
pnpm add -D prettier
pnpm add -D eslint-config-prettier
```

eslint-config-prettier を有効化する

```diff
.eslintrc.json
{
-   "extends": "next/core-web-vitals"
+   "extends": ["next/core-web-vitals","prettier"]
}
```

VSCode の設定ファイルに設定を追加

```diff
.vscode/settings.json
{
"javascript.preferences.importModuleSpecifier": "non-relative",
+   "editor.formatOnSave": true,
+   "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

ESLint のプラグインを導入

- eslint-plugin-sort-keys-custom-order
  - type や object のキーを自動でソートしてくれるプラグイン

```bash
pnpm add -D eslint-plugin-sort-keys-custom-order
```

設定を追加する

```diff
.eslintrc.json
{
+   "plugins": ["sort-keys-custom-order"],
  "extends": ["next/core-web-vitals", "prettier"],
+    "rules": {
+      // For JS objects sorting
+      "sort-keys-custom-order/object-keys": [
+        "error",
+        { "orderedKeys": ["id", "name", "title"] }
+      ],
+      // For TS types sorting
+      "sort-keys-custom-order/type-keys": [
+        "error",
+        { "orderedKeys": ["id", "name", "title"] }
+      ]
+  }
}
```

```diff
.vscode/settings.json
{
  "javascript.preferences.importModuleSpecifier": "non-relative",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
+   "editor.codeActionsOnSave": ["source.fixAll.eslint"]
}
```

- eslint-plugin-simple-import-sort
  - import/export を自動でソートしてくれるプラグイン

```bash
pnpm add -D eslint-plugin-simple-import-sort
```

設定を追加

```diff
.eslintrc.json
{
-   "plugins": ["sort-keys-custom-order"],
+  "plugins": ["sort-keys-custom-order", "simple-import-sort"],
  "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    // For JS objects sorting
    "sort-keys-custom-order/object-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    // For TS types sorting
    "sort-keys-custom-order/type-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
+     "simple-import-sort/imports": "error",
+     "simple-import-sort/exports": "error"
  }
}
```

- eslint-plugin-import
  - eslint-plugin-simple-import-sort と組み合わせて使うことによって機能を拡張する

```bash
pnpm add -D eslint-plugin-import
```

設定を追加

```diff
.eslintrc.json
{
-   "plugins": ["sort-keys-custom-order", "simple-import-sort"],
+   "plugins": ["sort-keys-custom-order", "simple-import-sort", "import"],
  "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    // For JS objects sorting
    "sort-keys-custom-order/object-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    // For TS types sorting
    "sort-keys-custom-order/type-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error",
+     "import/first": "error",
+     "import/newline-after-import": "error",
+     "import/no-duplicates": "error"
  }
}
```

- eslint-plugin-unused-imports
  - 使用していないものを削除したり、足りないものを自動で追加してくれるプラグイン

```bash
pnpm add -D eslint-plugin-unused-imports
```

```diff
.eslintrc.json
{
- "plugins": ["sort-keys-custom-order", "simple-import-sort", "import"],
+ "plugins": ["sort-keys-custom-order", "simple-import-sort", "import","unused-imports"],
 "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    // For JS objects sorting
    "sort-keys-custom-order/object-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    // For TS types sorting
    "sort-keys-custom-order/type-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error",
    "import/first": "error",
    "import/newline-after-import": "error",
    "import/no-duplicates": "error",
+     "unused-imports/no-unused-imports": "error"
  }
}
```

```diff
.vscode/settings.json
{
　"javascript.preferences.importModuleSpecifier": "non-relative",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": [
+     "source.addMissingImports",
    "source.fixAll.eslint"
  ]
}
```

### Tailwind CSS の導入

```bash
pnpm add -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```diff
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
-   content: [],
+   content: ["./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

```diff
+ @tailwind base;
+ @tailwind components;
+ @tailwind utilities;
```

追加することで文字を 1 文字打つたびに補完を出してくれる。

```diff
.vscode/settings.json

{
　"javascript.preferences.importModuleSpecifier": "non-relative",
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": [
    "source.addMissingImports",
    "source.fixAll.eslint"
  ],
+   "editor.quickSuggestions": {
+     "strings": "on"
+   }
}
```

```bash
pnpm add -D eslint-plugin-tailwindcss
```

tailwindcss の記述を自動で整えてくれるようになる

```diff
.eslintrc.json

{
  "plugins": [
    "sort-keys-custom-order",
    "simple-import-sort",
    "import",
    "unused-imports",
+     "tailwindcss"
  ],
  "extends": [
    "next/core-web-vitals",
    "prettier",
+     "plugin:tailwindcss/recommended"
  ],
  "rules": {
    // For JS objects sorting
    "sort-keys-custom-order/object-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    // For TS types sorting
    "sort-keys-custom-order/type-keys": [
      "error",
      { "orderedKeys": ["id", "name", "title"] }
    ],
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error",
    "import/first": "error",
    "import/newline-after-import": "error",
    "import/no-duplicates": "error",
    "unused-imports/no-unused-imports": "error"
  },
+   "settings": {
+     "tailwindcss": {
+       "groupByResponsive": true,
+       "whitelist": []
+     }
+   }
}
```

プラグインを追加

- @tailwindcss/typography
  - 見た目をいい感じにしてくれるプラグイン

```
pnpm add -D @tailwindcss/typography
```

- @tailwindcss/forms
  - フォーム要素のスタイルを上書きして、デフォルトのフォームスタイルを提供してくれる。

```
pnpm add -D @tailwindcss/forms
```

- @tailwindcss/line-clamp
  - 指定した行数でテキストを切り捨てて省略して表示することができる。

```
pnpm add -D @tailwindcss/line-clamp
```

上記３つのプラグインを設定ファイルに追加

```diff
tailwind.config.js

/** @type {import('tailwindcss').Config} */
module.exports = {
 content: ["./src/**/*.{js,ts,jsx,tsx}"],
 plugins: [
     require("@tailwindcss/typography"),
     require("@tailwindcss/forms"),
     require("@tailwindcss/line-clamp"),
 ],
 theme: {
   extend: {},
 },
};
```

## Prisma のセットアップとデータベース モデルの作成

Prisma CLI とクライアントをインストールする

```bash
# インストール
pnpm install -D prisma && pnpm install @prisma/client
# Prismaを初期化（sqliteをデフォルトDBとして設定する）
npx prisma init --datasource-provider sqlite
```

Prosma Client を生成し Prisma スキーマをデータベースにプッシュする

```bash
pnpm run db:migrate && pnpm run db:push
# Prismaスタジオを起動して確認する
npx prisma studio
```

## Next.js tRPC API を作成する

```bash
pnpm add @trpc/server@next @trpc/client@next @trpc/react-query@next @trpc/next@next @tanstack/react-query @tanstack/react-query-devtools zod superjson
```

- @trpc/server– tRPC サーバーの作成用
- @trpc/client– tRPC クライアントの作成用
- @trpc/react-query– tRPC サーバーを React に接続します
- @trpc/next– tRPC ルーターを Next.js に接続します
- @tanstack/react-query– React 用のサーバー状態管理ライブラリ
- @tanstack/react-query-devtools– React Query の GUI

## Next.js tRPC クライアントを作成する

```bash
pnpm install tailwind-merge react-toastify react-hook-form date-fns @hookform/resolvers
```

- tailwind-merge– スタイルの競合なしに Tailwind CSS クラスをマージできます
- react-toastify– React アプリに通知を追加できます
- react-hook-form– フォーム検証ライブラリ
- date-fns– JavaScript 日付ユーティリティ ライブラリ
- @hookform/resolvers– 外部検証ライブラリを使用できるようにする React-Hook-Form リゾルバ。

# sqlite から postgresql に変更する

prisma/migration/と dev.db を削除したら以下のコマンドを実行する

```
npx prisma migrate dev --name "changed-sqlite-to-postgres" --create-only && npx prisma generate && npx prisma db push
```
