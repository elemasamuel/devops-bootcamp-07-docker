## Demo Project - Nexus as Docker Container

### Topics of the Demo Project
Deploy Nexus as Docker container

### Technologies Used
- Docker
- Nexus
- DigitalOcean
- Linux

### Project Description
- Create and Configure Droplet
- Set up and run Nexus as a Docker container

### Steps to deploy and run Nexus in a Docker container on DigitalOcean
Step 1: Create droplet\
Login to your account on [DigitalOcean](https://cloud.digitalocean.com/login) and create a new Droplet having at least 4GB RAM and 80GB SSO disk. Add the existing firewall rule opening port 22 to this new Droplet. As an alternative, you can use the existing Droplet running the manually installed and configured Nexus instance. To stop this running Nexus instance, call `/opt/nexus-3.46.0-01/bin/nexus stop`.

Step 2: Install Docker\
SSH into this Droplet and install Docker by executing `apt update` and `snap install docker`.

Step 3: Create volume and pull/start the Nexus image\
Open [Docker Hub](https://hub.docker.com) and search for the 'sonatype/nexus3' image. Find the commands in the documentation to create a volume and start the container. Go back to the terminal of the DigitalOcean Droplet and execute them:
- `docker volume create --name nexus-data`
- `docker run -d -p 8081:8081 -p 8083:8083 --name nexus -v nexus-data:/nexus-data sonatype/nexus3`

Now Nexus is running (under the non root user named 'nexus') and can be accessed in the browser opening `http://<droplet-ip-address>:8081`.

If you want to find out, where the data of the container is stored on the host (i.e. where the folder referenced by the named volume 'nexus-data' is located), execute `docker inspect nexus-data` and read the "Mountpoint" property. In this folder you can find the data Nexus stores in its subfolder called 'sonatype-work'. You will find the initial admin password there, for example.