# jenkins

Jenkins is a popular automation server. We use jenkins to build the application images and deploy them to the Kubernetes cluster.  

## Building the Jenkins image

We need to install some build tools inside the Jenkins server. Hence, we are customizing the default image using Dockerfile. 

1. To build the image 

```
docker build -t shithindas/jenkins:tagname .
```

2. Login into Dockerhub repository using `docker login` command and push the image to remote repository

```
docker push shithindas/jenkins:tagname
```

## Configure Jenkins 

#### Prerequisite

Docker runtime should be installed on the server. You can refer the tutorial [Install on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

### Starting the Jenkins server

Volume is used to make sure that we don't lose your Jenkins data when the container gets restarted. We are exposing our Jenkins server on port `8080`. SSH into the desired server and execute the following steps to complete the setup.

1. Start the container

We are mounting docker socket and binary to the container because we have to access docker command from inside Jenkins container.(For building nginx app images) 

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

### Jenkins plugins

Install the following Jenkins plugins. You can do this via Manage Jenkins → Manage Plugins. Search the required plugin, click on `Install without restart` and wait until is done.

- Docker Pipelines plugin
- SSH Agent plugin

### Secrets and SSH keys

- Configure SSH Key in Jenkins for cloning the private repositories
  Create the SSH key pair using `ssh-keygen` command and add the private key via Manage Jenkins -> Manage Credentials -> Domains(global) -> Add Credentials. Add the username and private key here. Provide Secret ID as `github_user`
- Configure the Docker Hub login credentials in Jenkins secrets
  Add the Docker Hub login credentials as kind "Username with password". Provide secret id as `dockerhub_user`
- Configure SSH Private key for accessing Kubernetes server from Jenkins
  Add the pem key of kubernetes server as kind "SSH Username with private key". Provide secret id as `k8s_ubuntu`
- Configure Kubectl configuration as secret file. Provide secret id as `kubeconfig`



