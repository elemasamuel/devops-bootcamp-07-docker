## Demo Project - Deploy Docker application with Docker Compose

### Topics of the Demo Project
Deploy Docker application on a server with Docker Compose

### Technologies Used
- Docker
- Amazon ECR
- Node.js
- MongoDB
- MongoExpress

### Project Description
- Copy Docker-compose file to remote server
- Login to private Docker registry on remote server to fetch our app image
- Start our application container with MongoDB and MongoExpress services using docker compose

### Steps to deploy the app using docker compose

Step 1: Create an image of the application in the app folder and push it to the private registry:\
Because the URL to access the mongo-db from the application is different when running the application in the same Docker network as the mongo-db (`mongodb://admin:password@mongodb` instead of `mongodb://admin:password@localhost:27017` or `mongodb://admin:password@host.docker.internal:27017`) we have to build a new version of the image (containing the adjusted `server.js` file) and push it to the private registry.

```sh
cd app
docker build -t user-profile:1.1.0 .

# login to private registry
aws ecr get-login-password --region eu-central-1 | docker login --username AWS --password-stdin 369076538622.dkr.ecr.eu-central-1.amazonaws.com

# tag the image
docker tag user-profile:1.1.0 369076538622.dkr.ecr.eu-central-1.amazonaws.com/user-profile:1.1.0

# and push it to the registry
docker push 369076538622.dkr.ecr.eu-central-1.amazonaws.com/user-profile:1.1.0
```

Step 2: Copy the `docker-compose.yaml` file:\
Switch to a server / directory where you want to run the application and copy the [docker-compose.yaml](./docker-compose.yaml) file into this directory.

Step 3: Run the application
```sh
# login to private registry
aws ecr get-login-password --region eu-central-1 | docker login --username AWS --password-stdin 369076538622.dkr.ecr.eu-central-1.amazonaws.com

# run docker-compose
docker-compose up -d
```

Test the application in the browser (`http://localhost:3000`, `http://localhost:8081`).
