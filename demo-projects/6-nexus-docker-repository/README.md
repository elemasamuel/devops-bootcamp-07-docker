## Demo Project - Docker Repository on Nexus

### Topics of the Demo Project
Create Docker repository on Nexus and push to it

### Technologies Used
- Docker
- Nexus
- DigitalOcean
- Linux

### Project Description
- Create Docker hosted repository on Nexus
- Create Docker repository role on Nexus
- Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
- Build and Push Docker image to Docker repository on Nexus

### Steps to create a Docker repository on Nexus (DigitalOcean) and push our image
Step 1: Create repository\
Open the Nexus administration site (on the DigitalOcean Nexus droplet created in module 6), create a new repository of type 'docker (hosted)' and call it 'docker-registry'.

Step 2: Add privileges to a Nexus user\
In order to be able to execute `docker login` for this new repository, we need to create a new role (e.g. nx-docker') with the privilege 'nx-repository-view-docker-docker-registry-*' (docker-registry is the name of the new repository) and assign it to a nexus user (e.g. the user 'jenkins' created in the previous module). This user now has the privilege to execute `docker login` for the new repository.

Step 3: Configure a new port to access the repository\
The `docker login` command needs an ip address and a port. Until now we just have the URL `http://<nexus-droplet-ip>:8081/repository/docker-registry`. To make this repository accessible just via ip address and port, we open the repo in the settings area, check the 'HTTP' checkbox and add the port number 8083.

Step 4: Open the port in the droplet's firewall\
On the DigitalOcean admin site we have to add this port 8083 to the firewall rules (Custom, TCP, 8083, AllIpv4) that are defined for the nexus droplet.

Step 5: Activate Docker Bearer Token Realm in Nexus\
Back on the Nexus settings area, select Security > Realms and activate the 'Docker Bearer Token Realm'. (When `docker login` is executed for the first time against a specific Docker repository, we get an authentication token from that repository for our client. These tokens are stored in the file `~/.docker/config.json` and will then be used every time we interact with the related repository.)

Step 6: Allow insecure protocol for this registry\
By default Docker only allows requests from a client going to a secure (https) endpoint. Because our Nexus repository in this example use the http protocol, we have to configure Docker to allow thei insecure registry. On Linux clients this is done in the file `etc/docker/daemon.json` by adding
```sh
{
    "insecure-registries": ["<nexus-droplet-ip>:8083"]
}
```
If Docker was installed using snap, the daemon.json file is located under `/var/snap/docker/current/config/`.\
On a Mac we open the Docker Desktop settings, go to 'Docker Engine' and add the line `"insecure-registries": ["<nexus-droplet-ip>:8083"]` to it.

Step 7: Docker login\
Now we can finally call `docker login <nexus-droplet-ip>:8083` and enter username ('jenkins') and password of the Nexus user with the required docker registry privileges. The token returned by Nexus will be stored in `~/.docker/config.json` so that subsequent logins won't ask for username and password anymore.

Step 8: Push image\
Now that we are logged in to the Nexus Docker repository, we can push images to it as we did in video 11 (tag the image with the repository id and push it):
- `docker tag user-profile:1.1.0 <nexus-droplet-ip>:8083/user-profile:1.1.0`
- `docker push <nexus-droplet-ip>:8083/user-profile:1.1.0`