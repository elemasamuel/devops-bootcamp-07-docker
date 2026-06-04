## Demo Project - Docker Volumes

### Topics of the Demo Project
Persist Data with Docker Volumes

### Technologies Used
- Docker
- Node.js
- MongoDB

### Project Description
- Persist data of a MongoDB container by attaching a Docker volume to it

### Steps to attach a Docker volume to the MongoDB container
Step 1: Start the containers using docker-compose. The `docker-compose.yaml` file has been extended by a volume definition for the mongodb container:

```sh
version: '3.9'
services:
  user-profile:
    ...
  
  mongodb:
    image: mongo
    networks: 
      - mongo-net
    ports:
      - 27017:27017
    environment:
      ...
    volumes:
      - mongo-data:/data/db # mount a named volume and map it to a directory inside the container

  mongo-express:
    ...

networks:
  mongo-net:

volumes:
  mongo-data: # define a named volume
```

When you open the application in the browser (`http://localhost:3000`), modify some data of the profile, shutdown the containers (`docker-compose down`) and restart them again (`docker-compose up -d`), the changes in the profile data will still be there.
