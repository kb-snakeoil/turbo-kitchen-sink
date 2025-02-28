# Optimized for running on CI/CD

ARG NODE_VERSION=22
FROM ghcr.io/normative-io/platform-docker-images/node:${NODE_VERSION}-builder AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable


# NextJS does not work with `pnpm deploy` command
FROM base AS build
ARG APP_NAME
COPY . /build
WORKDIR /build
RUN --mount=type=cache,id=pnpm-store,target=/pnpm/store pnpm install --frozen-lockfile
RUN pnpm run build --filter=${APP_NAME}
#RUN pnpm deploy --filter=${APP_NAME} --prod /prod/${APP_NAME}


FROM ghcr.io/normative-io/platform-docker-images/node:${NODE_VERSION}-runner AS runner
ARG APP_NAME
WORKDIR /app
COPY --from=build /build/apps/${APP_NAME}/public* ./apps/${APP_NAME}/public
COPY --from=build --chown=node:node /build/apps/${APP_NAME}/.next/standalone ./
COPY --from=build --chown=node:node /build/apps/${APP_NAME}/.next/static* ./apps/${APP_NAME}/.next/static
WORKDIR /app/apps/${APP_NAME}
USER node
ENV PORT=8000
EXPOSE ${PORT}
CMD ["node", "server.js"]
