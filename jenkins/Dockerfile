FROM jenkins/jenkins:lts
LABEL maintainer="shithindasmk@gmail.com"

# Set default options
ENV JAVA_OPTS="-Xmx2048m"

# Create Jenkins Log Folder
USER root
RUN apt-get update && apt-get install -y ansible

#Install kubectl 
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl 
RUN chmod +x ./kubectl &&  mv ./kubectl /usr/local/bin/kubectl

# drop back to the regular jenkins user
USER jenkins