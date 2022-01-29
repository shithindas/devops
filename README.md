# jenkins

Jenkins is a popular automation server. We use jenkins to build the application images and deploy them to the Kubernetes cluster.  

## Building the image

We need to install some build tools inside the Jenkins server. Hence, we are customizing the default image using Dockerfile. 

1. To build the image 

```
docker build -t shithindas/jenkins:tagname .
```

2. Login into Dockerhub repository using `docker login` command and push the image to remote repository

```
docker push shithindas/jenkins:tagname
```

## Running Jenkins as Docker

#### Prerequisite

Install Docker via tutorial [Install on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

### Starting the Jenkins server

Volumes are used to make sure that we don't lose your Jenkins data. We are exposing our Jenkins server on port `8080`. SSH into the desired server and execute the following steps

1. Start the container

To access docker from inside Jenkins docker container, we are mounting docker socket and binary

```
mkdir -p /home/ubuntu/jenkins_home
sudo docker container run -d -p 8080:8080 -v /home/ubuntu/jenkins_home:/var/jenkins_home \
  --group-add $(stat -c '%g' /var/run/docker.sock) -v /var/run/docker.sock:/var/run/docker.sock \
  -v $(which docker):/usr/bin/docker --name jenkins-local shithindas/jenkins:latest
```
2. Access the Jenkins server via http://<server-IP>:8080

3. You can retrieve the default admin password via:

```
sudo docker exec jenkins-local cat /var/jenkins_home/secrets/initialAdminPassword
```

4. Complete the initial setup via selecting click on `Install suggested plugins` on the Customize Jenkins page.

### Jenkins plugins and  other secrets
Install the Docker Pipelines plugin on Jenkins:

Manage Jenkins → Manage Plugins.

Search Docker Pipelines, click on Install without restart and wait until is done.

Configure the Docker Hub login credentials in Jenkins secrets


Configure SSH Key in Jenkins:
Now add the private key to the Jenkins server. So go to Manage Jenkins -> Manage Credentials -> Domains(global) -> Add Credentials. Add the username and private key here.
