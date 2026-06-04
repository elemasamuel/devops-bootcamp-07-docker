## Demo Project - Developing with Docker

### Topics of the Demo Project
Use Docker for local development

### Technologies Used
- Docker
- Node.js
- MongoDB
- MongoExpress

### Project Description
- Run a Node.js application on localhost and connect to MongoDB database container locally.
- Also run MongoExpress container as a UI of the MongoDB database.

The demo app shows a simple set up for a user profile app using 
- an index.html with pure JacaScript and CSS styles,
- a Node.js backend with express module,
- and a mongo db for data storage.

#### Steps to setup and start the application

Step 1 (optional): Download the required [mongo](https://hub.docker.com/_/mongo) and [mongo express](https://hub.docker.com/_/mongo-express) Docker images:
```sh
docker pull mongo
docker pull mongo-express
```

Step 2: Create a Docker Network:\
Containers that are running in the same Docker network can talk to each other using just the container name (no ip address or port number is needed).
```sh
docker network create mongo-network
```

Step 3: Start the mongo database container attached to this network:
```sh
docker run -d \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  --name mongodb \
  --network mongo-network \
  mongo
```

Step 4: Start the mongo express container attached to the same network:
```sh
docker run -d \
  -p 8081:8081 \
  -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
  -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
  -e ME_CONFIG_MONGODB_SERVER=mongodb \
  --network mongo-network \
  --name mongo-express \
  mongo-express
```

Step 5: Access the mongo-express page in your browser under `http://localhost:8081` and create a new database called `user-profile`. Within this database create a collection called `users`.

Step 6: Start the node.js application executing the following commands:
```sh
cd app
npm install
npm start
```

Step 7: Access the application in your browser under `http://localhost:3000` and edit a user profile. You should see the edited data in the collection `users` of the mongo database `user-profile`.
