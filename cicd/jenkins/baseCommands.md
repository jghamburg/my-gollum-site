# Evaluating Docker as Development Environment  

Basic inspiration and starting point was the book "DevOps 2.1 Toolkit" from Victor Farcic and his presentation taken from his web site [vfarcic.github.io][vfarcic]

Here starts the presentation of ["DevOps 2.1 Toolkit: Continuous Deployment with Jenkins and Docker Swarm"][docker-swarm-jenkins]

Collect a proposal to create a local development environment with the help of 

* Docker  
* Docker Compose  

## Jenkins  

A second entry point on dockerhub: [Jenkins on Dockerhub][docker-jenkins]  
Start out with basic commands to create a Jenkins environment.  

	docker run -p 8080:8080 -p 50000:50000 jenkinsci/jenkins:2.60.1

	docker run --name myjenkins -p 8080:8080 -p 50000:50000 -v /var/jenkins_home jenkinsci/jenkins:2.60.1


[vfarcic]: http://vfarcic.github.io  
[docker-jenkins]: https://hub.docker.com/_/jenkins/  
[docker-swarm-jenkins]: http://vfarcic.github.io/jenkins-swarm/workshop.html  
