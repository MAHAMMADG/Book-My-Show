FROM node:18

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install --legacy-peer-deps

RUN npm install chokidar@3.5.3

COPY . .

ENV NODE_OPTIONS=--openssl-legacy-provider
ENV HOST=0.0.0.0
ENV PORT=3000

EXPOSE 3000

CMD ["npm","start"]
