新規でプロジェクトを作成する

```bash
npx create-next-app --example with-tailwindcss nextjs-trpc-crud-app
```

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
