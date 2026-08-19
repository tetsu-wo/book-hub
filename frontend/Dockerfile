# ==========================================
# 1. Baseステージ (共通の土台)
# ==========================================
FROM node:20-slim AS base
WORKDIR /app
# npm以外のパッケージ管理ツール（pnpm/yarn等）を使う場合はここで有効化
# RUN corepack enable

# ==========================================
# 2. Dependenciesステージ (依存関係のインストール)
# ==========================================
FROM base AS deps
COPY package*.json ./
# 本番用とビルド用の両方のパッケージをインストール
RUN npm install

# ==========================================
# 3. Buildステージ (ソースコードのビルドと最適化)
# ==========================================
FROM base AS builder
COPY --from=deps /app/node_modules ./node_modules
COPY . .
# 💡 ここで型同期（generate-apiなど）があれば実行する
# RUN npm run generate-api
# 本番用のビルド（Next.jsやViteなどのコンパイル・最適化）
RUN npm run build

# 不要な開発用ライブラリ（devDependencies）を削除し、本番用のみ残す
# (Next.jsのstandaloneモード等を使う場合は、このステップをスキップ・最適化できます)
RUN npm prune --production

# ==========================================
# 4. Runnerステージ (実行専用の軽量コンテナ)
# ==========================================
FROM node:20-slim AS runner
WORKDIR /app

# セキュリティ対策：rootユーザーでの実行を避け、nodeユーザーを使用
ENV NODE_ENV=production
USER node

# ビルド成果物と、本番用の軽量な node_modules だけをコピー
COPY --from=builder --chown=node:node /app/package*.json ./
COPY --from=builder --chown=node:node /app/node_modules ./node_modules
COPY --from=builder --chown=node:node /app/dist ./dist
# ⚠️ 上記の ./dist 部分は、フレームワーク（Next.jsなら .next 等）に合わせて変更してください

EXPOSE 3000
CMD ["npm", "run", "start"]
