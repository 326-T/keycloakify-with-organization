### STAGE 1: Base ###
FROM node:23-alpine AS base

RUN apk add maven
WORKDIR /app

### STAGE 2: Dependencies ###
FROM base AS deps

COPY package.json yarn.lock /app/
RUN yarn --frozen-lockfile

### STAGE 3: Build theme ###
FROM base AS builder

COPY --from=deps /app/node_modules /app/node_modules
COPY src /app/src
COPY public /app/public
COPY package.json yarn.lock index.html tsconfig.json tsconfig.node.json vite.config.ts /app/
RUN yarn build-keycloak-theme

### STAGE 4: Build keycloak ###
FROM quay.io/keycloak/keycloak:26.0.5

ENV KC_DB=postgres
ENV KC_FEATURES=organization
WORKDIR /opt/keycloak
COPY --from=builder /app/dist_keycloak/keycloak-theme-for-kc-all-other-versions.jar /opt/keycloak/providers/
RUN /opt/keycloak/bin/kc.sh build

ENTRYPOINT [ "/opt/keycloak/bin/kc.sh" ]