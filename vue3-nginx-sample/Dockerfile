ARG NODE_VERSION=24.8.0-alpine
ARG PNPM_VERSION=10.15.1
ARG NGINX_VERSION=alpine3.21

FROM node:${NODE_VERSION} AS builder

WORKDIR /app

RUN --mount=type=cache,target=/root/.npm \
    npm install -g pnpm@${PNPM_VERSION}

COPY . .

RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=pnpm-lock.yaml,target=pnpm-lock.yaml \
    --mount=type=cache,target=/root/.local/share/pnpm/store \
    pnpm install --frozen-lockfile

RUN pnpm run build-only

FROM nginxinc/nginx-unprivileged:${NGINX_VERSION} AS runner

ENV TZ=Asia/Shanghai

USER nginx

COPY nginx.conf /etc/nginx/nginx.conf

COPY --chown=nginx:nginx --from=builder /app/dist /usr/share/nginx/html
COPY --chown=nginx:nginx --from=builder /app/ccGXJNPYn1.txt /usr/share/nginx/html

EXPOSE 80

ENTRYPOINT ["nginx", "-c", "/etc/nginx/nginx.conf"]
CMD ["-g", "daemon off;"]