## EC2 User Data (For Quick Init)

#!/bin/bash

sudo yum update

sudo yum install -y docker git

sudo systemctl start docker

sudo systemctl enable docker

sudo usermod -aG docker ec2-user

docker pull jenkins/jenkins:lts

mkdir -p /home/ec2-user/jenkins_home

sudo chown -R 1000:1000 /home/ec2-user/jenkins_home

docker network create jenkins

docker run -d \
  --name jenkins \
  --network jenkin \ 
  -p 8080:8080 -p 50000:50000 \
  -v /home/ec2-user/jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

sudo yum install -y amazon-ssm-agent

sudo systemctl enable amazon-ssm-agent

sudo systemctl start amazon-ssm-agent

## alpine/socat container to forward traffic from Jenkins's worker node to the other jenkins node. 

If installed locally use localhost otherwise leave only the ports
https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin
```
docker run -d --restart=always -p 2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
docker inspect <container_id> | grep IPAddress
```

# Pulling my docker-agent with python installed
docker pull barzfati/jenkins-inbound-agent-python:latest
