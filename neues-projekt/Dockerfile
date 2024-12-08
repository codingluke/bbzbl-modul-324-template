# Mit AS builder geben wir dem Image eine Namen um darauf zuzugreifen
FROM node:lts-slim AS builder

# Install Chromium
# (Use Chromium from "Playwright" instead of Puppeteer, for getting ARM64 build, which is not provided by Puppeteer)
RUN mkdir -p /tmp/cli-chromium && \
  cd /tmp/cli-chromium && \
  npm i playwright@latest && \
  PLAYWRIGHT_BROWSERS_PATH=/usr/local/bin/pw-browsers npx playwright install --with-deps chromium && \
  ln -s $(find /usr/local/bin/pw-browsers -name "chrome" -executable | head -n 1) /usr/local/bin/chrome && \
  rm -rf /tmp/cli-chromium
ENV CHROME_BIN="/usr/local/bin/chrome"

WORKDIR /app

COPY . .

RUN npm ci && npm run test:ci && npm run build

# Ab hier beginnt das produktive Image!
FROM node:lts-slim

# Das LABEL muss hier gesetzt sein!
LABEL service="neues-ssg-projekt"

WORKDIR /app

# Hier kopieren wir nur den gebauten Code vom builder Image
COPY --from=builder /app/dist/neues-projekt/ .

ENV PORT=3000
EXPOSE 3000/tcp

CMD [ "node", "/app/server/server.mjs" ]
