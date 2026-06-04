FROM node:13-alpine

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=password

RUN mkdir -p /home/app

COPY ./app /home/app

# set default dir so that next commands executes in /home/app dir
WORKDIR /home/app

# execute npm install in /home/app (WORKDIR)
RUN npm install

# start the server
CMD ["node", "server.js"]
