FROM node:lts-slim

WORKDIR /app/

COPY package.json /app/
COPY package-lock.json /app/
RUN npm ci

COPY . /app/

EXPOSE 3000

CMD npm start
