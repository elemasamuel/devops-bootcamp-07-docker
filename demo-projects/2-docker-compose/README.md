## Demo Project - Docker Compose - Run multiple Docker containers

### Topics of the Demo Project
Docker Compose - Run multiple Docker containers

### Technologies Used
- Docker
- MongoDB
- MongoExpress

### Project Description
- Write a Docker Compose file to run MongoDB and MongoExpress containers

#### Steps to setup and start the MongoDB and MongoExpress containers using Docker Compose

Step 1: Start the containers specified in the `docker-compose.yaml` file by executing the following command:
```sh
docker-compose up -d
```

Step 2 (optional): Access the mongo-express page in your browser under `http://localhost:8081` and create a new database called `user-profile`. Within this database create a collection called `users`.

Step 3 (optional): Start the node.js application executing the following commands:
```sh
cd app
npm install
npm start
```

Step 4 (optional): Access the application in your browser under `http://localhost:3000` and edit a user profile. You should see the edited data in the collection `users` of the mongo database `user-profile`.
