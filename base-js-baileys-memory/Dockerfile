# Image size ~ 400MB
FROM node:20-bullseye-slim AS builder

WORKDIR /app

RUN corepack enable && corepack prepare pnpm@latest --activate
ENV PNPM_HOME=/usr/local/bin

COPY . .

COPY package*.json *-lock.yaml ./

RUN apt-get update && \
    apt-get install -y \
    python3 \
    make \
    g++ \
    git && \
    pnpm install && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

FROM node:20-bullseye-slim AS deploy

WORKDIR /app

# ARG PORT = 3008
# ENV PORT=$PORT
# EXPOSE $PORT

ENV PORT=3008
EXPOSE 3008

COPY --from=builder /app ./
COPY --from=builder /app/*.json /app/*-lock.yaml ./

RUN corepack enable && corepack prepare pnpm@latest --activate 
ENV PNPM_HOME=/usr/local/bin

RUN npm cache clean --force && pnpm install --production --ignore-scripts \
    && groupadd -g 1001 nodejs && useradd -u 1001 -g nodejs -s /bin/bash -m nodejs \
    && rm -rf $PNPM_HOME/.npm $PNPM_HOME/.node-gyp


CMD ["npm", "start"]