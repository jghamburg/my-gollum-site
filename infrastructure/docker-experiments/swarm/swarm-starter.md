# Starting with docker swarm  

The basic goal is to install a local instance of jenkins ci on a docker swarm node.  
The ideas was taken from an [article by Victor Farcic][Victor Farcic: Uber-Jenkins].  
To start with docker swarm you need at least version 1.12 of docker.  
To initialize the swarm mode create a master node  
```
>docker swarm init
Swarm initialized: current node (onsmq2i8wpn3ttu01y5x6538k) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-1to1dugkh5u1q0o5iql7a6ldvjhsk549tuxtkclia7ylocibn1-0ahucx3isdrkookdqsh04cese \
    192.168.65.2:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```  

after this you can use this swarm node to create secrets (hidden credentials) for configuration references inside of docker files.  
```
echo "admin" | docker secret create jenkins-user -
echo "admin" | docker secret create jenkins-pass -
```

# References  
[Victor Farcic: Uber-Jenkins](https://github.com/vfarcic/vfarcic.github.io/blob/master/articles/uber-jenkins.md)  
