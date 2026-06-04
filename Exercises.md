## Exercises
<br />

Your team member has improved your previous static java application and added mysql database connection, to let users edit information and save the edited data.

They ask you to configure and run the application with Mysql database on a server using docker-compose.

<details>
<summary>Exercise 0: Create Git Repository</summary>
<br />

**Tasks:**

Create a git repository for the module exercises on GitHub.

Clone the git repository on https://gitlab.com/devops-bootcamp3/bootcamp-java-mysql
You will be working with this project for the next following modules.

You can check out the code changes and notice that we are using environment variables for the database and its credentials inside the application.

This is very important for 2 main reasons:
- First you don't want to expose the password to your database by hardcoding it into the app and checking it into the repository!
- Second, these values may change based on environment, so you want to be able to set them dynamically when deploying the application, instead of hardcoding them. 

**Steps to solve the tasks:**

```sh
# create a local repository and commit its content
mkdir devops-bootcamp-07-docker
cd devops-bootcamp-07-docker
touch README.md
touch Notes.md
touch Exercises.md
git init 
git add .
git commit -m "Initial commit"

# create git repository on GitHub and push your newly created local repository to it
git remote add origin git@github.com:fsiegrist/devops-bootcamp-07-docker.git
# rename master branch to main if necessary (default on GitHub)
git branch -M main
# push your newly created local repository to it
git push -u origin main

# clone the git repository containing the project the exercises are based on
git clone https://gitlab.com/devops-bootcamp3/bootcamp-java-mysql.git

# delete the .git directory to remove the association with the original repository
cd bootcamp-java-mysql
rm -rf .git

# commit and push the new folder
cd ..
git add bootcamp-java-mysql
git commit -m "Add exercise project"
git push
```

</details>

******

<details>
<summary>Exercise 1: Start Mysql container</summary>
<br />

**Tasks:**

First you want to test the application locally with mysql database. But you don't want install Mysql, you want to get started fast, so you start it as a docker container.

- Start mysql container locally using the official Docker image. Set all needed environment variables.
- Export all needed environment variables for your application for connecting with the database (check variable names inside the code)
- Build jar file and start the application. Test access from browser. Make some change.

**Steps to solve the tasks:**

Step 1: Check the required environment variables\
The environment variables our application needs to connect to the database can be found in the class [DatabaseConfig](./bootcamp-java-mysql/src/main/java/com/example/DatabaseConfig.java). They are
- DB_USER
- DB_PWD
- DB_SERVER
- DB_NAME

Open [Docker Hub](https://hub.docker.com/_/mysql) and check the respective names of the environment variables supported by the MySQL Docker image. They are
- MYSQL_USER
- MYSQL_PASSWORD
- n/a
- MYSQL_DATABASE

In addition the env varibale MYSQL_ROOT_PASSWORD is mandatory. It specifies the password that will be set for the MySQL root superuser account.

Step 2: Start MySQL in a Docker container
```sh
docker run --name mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_USER=admin \
  -e MYSQL_PASSWORD=admin \
  -e MYSQL_DATABASE=team-members \
  -p 3306:3306 \
  -d mysql:8.0.32
```

Step 3: Build the application
```sh
cd ./bootcamp-java-mysql
./gradlew build
```

Step 4: Export the environment variables needed by the application
```sh
export DB_USER=admin
export DB_PWD=admin
export DB_SERVER=localhost
export DB_NAME=team-members
```

Step 5: Start the application
```sh
java -jar ./build/libs/bootcamp-java-mysql-project-1.0-SNAPSHOT.jar
```
Open the application in your browser: [localhost:8080](http://localhost:8080) and edit a member.

</details>

******

<details>
<summary>Exercise 2: Start Mysql GUI container</summary>
<br />

**Tasks:**

Now you have a database, you want to be able to see the database data using a UI tool, so you decide to deploy phpmyadmin. Again, you don't want to install it locally, so you want to start it also as a docker container.

- Start phpmyadmin container using the official image.
- Access your phpmyadmin from browser and test logging in to your Mysql database 

**Steps to solve the tasks:**

Step 1: Read the documentation of the phpmyadmin image\
Open [Docker Hub](https://hub.docker.com/_/phpmyadmin) and check how to start the container and link it to the MySQL Docker container.

Step 2: Start the phpmyadmin Docker container
```sh
# make sure the mysql container is running
docker ps

# download the phpmyadmin image and start the container
docker run --name phpmyadmin -d --link mysql:db -p 8081:80 phpmyadmin:5.2.1
```

Step 3: Open phpmyadmin in the browser\
Open [localhost:8081](http://localhost:8081) in your browser and login with root:secret (see MYSQL_ROOT_PASSWORD in exercise 1) or admin:admin (see MYSQL_USER and MYSQL_PASSWORD in exercise 1). Open the 'team-members' database and browse the 'team-members' table. 

</details>

******

<details>
<summary>Exercise 3: Use docker-compose for Mysql and Phpmyadmin</summary>
<br />

**Tasks:**

You have 2 containers your app needs and you don't want to start them separately all the time. So you configure a docker-compose file for both:

- Create a docker-compose file with both containers
- Configure volume for your DB
- Test that everything works again

**Steps to solve the tasks:**

Step 1: Stop the running containers from the previous exercises
```sh
docker stop phpmyadmin
docker rm phpmyadmin
docker stop mysql
docker rm mysql
```

Step 2: Create `docker-compose.yaml` file\
Create a file called `docker-compose.yaml` with the following content:
```sh
version: '3.9'
services:
  mysql:
    image: mysql:8.0.32
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=secret
      - MYSQL_USER=admin    
      - MYSQL_PASSWORD=admin
      - MYSQL_DATABASE=team-members
    volumes:
      - mysql-data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin:5.2.1
    ports:
      - 8081:80
    environment:
      - PMA_HOST=mysql # defines the host name of the MySQL server
                       # (= service name for containers running in the same Docker network)
      - PMA_PORT=3306
      - MYSQL_ROOT_PASSWORD=secret

volumes:
  mysql-data:
```

Step 3: Start the containers user docker-compose
```sh
cd </path/to/directory/containing/docker-compose.yaml/>
docker-compose up -d
```

Step 4: Open phpmyadmin in the browser\
Open [localhost:8081](http://localhost:8081) in your browser and login with root:secret (see MYSQL_ROOT_PASSWORD in exercise 1) or admin:admin (see MYSQL_USER and MYSQL_PASSWORD in exercise 1).

</details>

******

<details>
<summary>Exercise 4: Dockerize your Java Application</summary>
<br />

**Tasks:**

Now you are done with testing the application locally with Mysql database and want to deploy it on the server to make it accessible for others in the team, so they can edit information.

And since your DB and DB UI are running as docker containers, you want to make your app also run as a docker container. So you can all start them using 1 docker-compose file on the server. So you do the following:

- Create Dockerfile for your java application

**Steps to solve the tasks:**

Step 1: Choose a suitable base image\
Open [Docker Hub](https://hub.docker.com/_/openjdk). You'll find a deprecation note saying
```
This image is officially deprecated and all users are recommended to find and use suitable replacements ASAP.
```
Below you'll find a list of recommended alternatives. We choose [Eclipse Temurin](https://hub.docker.com/_/eclipse-temurin). Open the "Tags" tab to see what tags are provided. Since we want to keep the image small and just need the jre to run the jar, we choose `eclipse-temurin:8-jre`.

Step 2: Create a Dockerfile\
Switch to the [bootcamp-java-mysql](./bootcamp-java-mysql/) folder and create a file called `Dockerfile` with the following content:
```sh
FROM eclipse-temurin:8-jre
# Deprecated base image:
# FROM openjdk:8-jdk-alpine

EXPOSE 8080

RUN mkdir /opt/bootcamp-java-mysql-app
RUN addgroup -S mygroup && adduser -S myuser -G mygroup
RUN chown -R myuser:mygroup /opt/bootcamp-java-mysql-app
USER myuser

COPY ./build/libs/bootcamp-java-mysql-project-1.0-SNAPSHOT.jar /opt/bootcamp-java-mysql-app

CMD ["java", "-jar", "/opt/bootcamp-java-mysql-app/bootcamp-java-mysql-project-1.0-SNAPSHOT.jar"]
```

</details>

******

<details>
<summary>Exercise 5: Build and push Java Application Docker Image</summary>
<br />

**Tasks:**

Now for you to be able to run your java app as a docker image on a remote server, it must be first hosted on a docker repository, so you can fetch it from there on the server. Therefore, you have to do the following:

- Create docker hosted repository on Nexus
- Build the image locally and push to the repository

**Steps to solve the tasks:**

Step 1: Create a Droplet running Nexus as described in [Nexus as Docker container](./demo-projects/7-nexus-as-docker-container/) and create a private docker registry as described in [Docker repo on Nexus](./demo-projects/6-nexus-docker-repository/) (step 1 - step 5).

Step 2: Create jar file
```sh
cd bootcamp-java-mysql
./gradlew build
```

Step 3: Create docker image\
`docker build -t 104.248.134.221:8083/java-app:1.0-SNAPSHOT .`

Step 4: Push image to remote private docker registry\
`docker login 104.248.134.221:8083` (username: jenkins / password: jenkins)\
`docker push 104.248.134.221:8083/java-app:1.0-SNAPSHOT`

</details>

******

<details>
<summary>Exercise 6: Add application to docker-compose</summary>
<br />

**Tasks:**

- Add your application's docker image to docker-compose. Configure all needed env vars.


Now your app and Mysql containers in your docker-compose are using environment variables.

- Make all these environment variable values configurable, by setting them on the server when deploying.

INFO: Again, since docker-compose is part of your application and checked in to the repo, it shouldn't contain any sensitive data. But also allow configuring these values from outside based on an environment

**Steps to solve the tasks:**

Step 1: Create `docker-compose.yaml` file
Switch to the [bootcamp-java-mysql](./bootcamp-java-mysql/) folder and create a file called `docker-compose.yaml` with the following content:
```sh
version: '3.9'
services:
  java-app:
    image: 104.248.134.221:8083/java-app:1.0-SNAPSHOT
    environment:
      - DB_SERVER=mysql
      - DB_USER=${MYSQL_USER}
      - DB_PWD=${MYSQL_PASSWORD}
      - DB_NAME=${MYSQL_DATABASE}
    ports:
      - 8080:8080
    depends_on:
      - mysql
    restart: always
    container_name: java-app

  mysql:
    image: mysql:8.0.32
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
    volumes:
      - mysql-data:/var/lib/mysql
    container_name: mysql

  phpmyadmin:
    image: phpmyadmin:5.2.1
    ports:
      - 8081:80
    environment:
      - PMA_HOST=mysql # defines the host name of the MySQL server
                       # (= service name for containers running in the same Docker network)
      - PMA_PORT=3306
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
    depends_on:
      - mysql
    restart: always
    container_name: phpmyadmin

volumes:
  mysql-data:
```

</details>

******

<details>
<summary>Exercise 7: Run application on server with docker-compose</summary>
<br />

**Tasks:**

Finally your docker-compose file is completed and you want to run your application on the server with docker-compose. For that you need to do the following:

- Set insecure docker repository on server, because Nexus is http
- Do docker login on the server to be allowed to pull the image
- Your application index.html has a hardcoded localhost as a HOST to send requests to backend. You need to fix that and set the server IP address instead, because the server is going to be the host when you deploy the application on a remote server. (Don't forget to rebuild and push the image and if needed adjust the docker-compose file)
- Copy docker-compose.yaml to the server
- Set the needed environment variables for all containers in docker-compose
- Run docker-compose to start all 3 containers

**Steps to solve the tasks:**

Step 1: Create a droplet to which the ava application will be deployed\
Login to your account on [DigitalOcean](https://cloud.digitalocean.com/login) and create a new Droplet (512MB RAM). Add firewall rule opening port 22 for IP address 31.10.157.107 (my local machine).\
ssh into the new droplet (`ssh root@165.227.162.246`) and execute the following commands:
```sh
# Install docker
apt update
snap install docker
```

Add `"insecure-registries": [ "104.248.134.221:8083" ]` to `/var/snap/docker/current/config/daemon.json`.
```sh
# restart Docker
snap restart docker

# check
docker info # should show the additional insecure registry at the end of the output
```

Step 2: Docker login\
`docker login 104.248.134.221:8083` (username: jenkins, password: jenkins)

Step 3: Adjust files
- Change the hardcoded HOST env var in [index.html](./bootcamp-java-mysql/src/main/resources/static/index.html), line 48 to `const HOST = "165.227.162.246";`
- Change version in [build.gradle](./bootcamp-java-mysql/build.gradle), line 8 to `1.1-SNAPSHOT`
- Change version in [Dockerfile](./bootcamp-java-mysql/Dockerfile): `sed -i '' -e 's/1.0-SNAPSHOT/1.1-SNAPSHOT/g' Dockerfile`
- Change version in [docker-compose.yaml](./bootcamp-java-mysql/docker-compose.yaml): `sed -i '' -e 's/1.0-SNAPSHOT/1.1-SNAPSHOT/g' docker-compse.yaml`

Step 4: Rebuild application and image and push image to repo
```sh
./gradlew build
docker build -t 104.248.134.221:8083/java-app:1.1-SNAPSHOT .
docker push 104.248.134.221:8083/java-app:1.1-SNAPSHOT
```

Step 5: Copy docker-compose file to remote server\
`scp docker-compose.yaml root@165.227.162.246:/root`

Step 6: Set env variables on remote server\
ssh into the remote server (`ssh root@165.227.162.246`) and set the following env variables:
```sh
export MYSQL_USER=admin
export MYSQL_PASSWORD=admin
export MYSQL_DATABASE=team_members
export MYSQL_ROOT_PASSWORD=secret
```

Step 7: Run docker compose file
```sh
cd /root
docker-compose up -d
```

</details>

******

<details>
<summary>Exercise 8: Open ports</summary>
<br />

**Tasks:**

Congratulations! Your application is running on the server, but you still can't access the application from the browser. You know you need to configure firewall settings. So do the following:

- Open the necessary port on the server firewall and
- Test access from the browser

**Steps to solve the tasks:**

Step 1: Open port 8080 on droplet\
Add a firewall rule opening port 8080 to the firewall created to open port 22 on the droplet running the docker containers.

Step 2: Open the browser and navigate to `http://165.227.162.246:8080` to see the running app.

</details>

******
