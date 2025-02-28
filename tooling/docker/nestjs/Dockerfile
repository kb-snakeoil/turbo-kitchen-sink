# Optimized for running on CI/CD

ARG NODE_VERSION=22
FROM node:${NODE_VERSION}-bookworm-slim AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable


# NestJS workd with `pnpm deploy` command 
FROM base AS build
ARG APP_NAME
COPY . /build
WORKDIR /build
RUN --mount=type=cache,id=pnpm-store,target=/pnpm/store pnpm install --frozen-lockfile
RUN pnpm run build --filter=${APP_NAME}
RUN pnpm deploy --filter=${APP_NAME} --prod /prod/${APP_NAME}


FROM node:${NODE_VERSION}-alpine AS runner
ARG APP_NAME
WORKDIR /app
COPY --from=build /prod/${APP_NAME} ./
ENV PORT=8000
EXPOSE ${PORT}
CMD ["node", "dist/index.cjs"]
