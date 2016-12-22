# Evaluating Docker as Development Environment  

Base directory: /Users/jgellien/Documents/Projects/Docker-Starter/Jenkins  

Collect a proposal to create a local development environment with the help of 

* Docker  
* Docker Compose  

## Jenkins  

Einstiegsseite auf dockerhub: [Jenkins on Dockerhub](https://hub.docker.com/_/jenkins/)  
Start out with basic commands to create a Jenkins environment.  

	docker run -p 8080:8080 -p 50000:50000 jenkins

docker run --name myjenkins -p 8080:8080 -p 50000:50000 -v /var/jenkins_home jenkins