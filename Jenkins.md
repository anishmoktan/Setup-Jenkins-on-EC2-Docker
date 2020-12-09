1. Start by creating EC2 - free tier is fine

2. Make sure you put these commands in the user data commands:
```
    #!/bin/bash
    sudo yum update -y
    sudo amazon-linux-extras install docker -y
    sudo yum install docker git python3 -y
    sudo service docker start
    sudo usermod -a -G docker ec2-user
```
3. Connect to the EC2:
``` 
    ssh ec2-user@{IP} -i devbops_masterkey.pem
```
4. Verify that docker is running by:
```
    docker --version
```
5. Create a bridge network in Docker using the following docker network create command:
```
    docker network create jenkins
```
6. Run these commands to get the image for Jenkins:
```
    docker run \
    --name jenkins-docker \
    --rm \
    --detach \
    --privileged \
    --network jenkins \
    --network-alias docker \
    --env DOCKER_TLS_CERTDIR=/certs \
    --volume jenkins-docker-certs:/certs/client \
    --volume jenkins-data:/var/jenkins_home \
    --publish 2376:2376 \
    docker:dind
  ```
7. Run this command to tell docker to run this image in a container:
```
    sudo docker run -u 0 -d -p 8080:8080 -p 50000:50000 -v / data/jenkins:/var/jenkins_home jenkins/jenkins:lts
```
8. Verify that the docker container is running:
```
    docker ps
```
9. Get the password of the jenkins in the logs of the container by running:
```
    docker logs {container name}
```
9. Navigate to {Server IP}:8080 in web browser and paste the password (its the long string).

10. Install all suggested plugins.

11. To install Blue Ocean:
```
 Manage Jenkins -> Plugins -> Available Plugins -> Install Blue Ocean
```
12. Create a pipeline after installing blue ocean.
