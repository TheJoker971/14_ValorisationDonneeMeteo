FROM dhi.io/node:25-dev AS build

WORKDIR /app

COPY package*.json ./
RUN npm install --no-audit --no-fund

COPY . .
RUN npm run build


FROM dhi.io/node:25

ENV NODE_ENV=production
WORKDIR /app

COPY --from=build /app/.output /app/.output

EXPOSE 3000
CMD ["node", ".output/server/index.mjs"]
