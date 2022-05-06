FROM node:18

RUN mkdir react
WORKDIR /react

COPY package.json /react
RUN npm install

COPY . /react

RUN npm run build

EXPOSE 3000

CMD [ "npm", "run", "start:prod" ]