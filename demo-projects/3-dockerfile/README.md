## Demo Project - Dockerfile - Build your own Docker Image

### Topics of the Demo Project
Dockerize Nodejs application and push to private Docker registry

### Technologies Used
- Docker
- Node.js
- Amazon ECR

### Project Description
- Write Dockerfile to build a Docker image for a Nodejs application
- Create private Docker registry on AWS (Amazon ECR) 
- Push Docker image to this private repository

### Steps to build a docker image from the application

Step 1: Create an image of the application in the app folder:
```sh
docker build -t user-profile:1.0.0 . 
```

### Steps to create a private Docker registry on AWS

Step 1: [Create an AWS account](https://aws.amazon.com/de/premiumsupport/knowledge-center/create-and-activate-aws-account/)

Step 2: Install the AWS CLI
```sh
brew update
brew install awscli

# verify that the shell can find and run the aws command
which aws
aws --version
```

Step 3: [Configure AWS](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)
- Go to Services > IAM > Users and create a new user called `admin`, provide access to the AWS management console, select 'I want to create an IAM user' and click on 'Next', select 'Attach existing policies directly', select 'AdministratorAccess' and click on 'Next', check the data on the summary page and click 'Create User'. Download the csv-file containing the console login URL and the console password.
- Logout as root user and open the console login URL (in the csv-file) and enter the username (`admin`) and password (also in the csv-file) to login as the new admin user. Change the password.
- Go to Services > IAM > Users, select the admin user, open the tab 'Security login information', go to 'Access keys' and click on 'Create access keys', select 'Command Line Interface CLI' and click on 'Create access keys'. Download the csv-file containing the access key ID and the secret access key.
- Execute `aws configure` and enter the access key ID, the secret access key, the region (`eu-central-1`) and the output format (e.g. `json`, or `yaml`).

Step 4: Create an ECR Service:\
ECR (Elastic Container Registry) is the service on AWS you can use to create a Docker regristry.
- Login to the AWS account and open the [Management Console](https://eu-central-1.console.aws.amazon.com/console/home?region=eu-central-1#)
- Go to Services > Container > Elastic Container Registry
- Click on 'Create a repository / Get Started'
- Select 'Private' repository and give it a name (`user-profile`)
- Leave all other form fields unchanged an click on 'Create repository'


### Steps to push the image to this private repository

Step 1: Authenticate the Docker client for the private registry (`docker login`):
```sh
aws ecr get-login-password --region eu-central-1 | docker login --username AWS --password-stdin 369076538622.dkr.ecr.eu-central-1.amazonaws.com
```

Step 2: Tag the image to be pushed to the private repository\
Because we want to push this image to a private registry (not Docker Hub), we have to tag (mark) it with the fully qualified image name (containing the repository name):
```sh
docker tag user-profile:1.0.0 369076538622.dkr.ecr.eu-central-1.amazonaws.com/user-profile:1.0.0
```

Step 3: Push the image to the private registry:
```sh
docker push 369076538622.dkr.ecr.eu-central-1.amazonaws.com/user-profile:1.0.0
```
