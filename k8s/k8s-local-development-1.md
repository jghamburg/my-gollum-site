# My way to Amarillo - or a better local development with k8s (part 1)  

As developer of the portal pirates team I work a little over one year with container orchestration. First on the basis of Mesos / Marathon and DC/OS and now for the last two month on basis of Kubernetics, aka k8s.  

During all this time we were searching for a reasonable setup of local development environment.  
On basis of k8s now the time is right to redefine the phrase  

> Works on my machine!  
> — Lonely Developer

To setup a local k8s cluster using Docker for Desktop or Minikube is straight forward. And is left to the humble reader of this article.  
Simple enough but ...   
Running a service mesh of multiple components building a self contained system (SCS) worked straight out of the box.  -  (referencing each other by using k8s service definitions).   
The interesting part started out as I tried to reference different self contained systems as required by developing a central portal service and an associated vertical system providing a business service like Brandshop Updater Service in the context of the OTTO Brand Connect project (OBC).  
If you are installing all of this on a local machine of a developer the simplest way to do it is to match access points of the different SCS to `localhost` with a specific port. From a browser perspective this works, but if one container tries to resolve a redirect configuration of the kind

```http://localhost:8090/myservice
```

Now you are traped!   

> What would I give for a real DNS server running on my machine!  

With DNS names resolving to individual ip Adresses and not on the loopback device would definitely solve the problem! - Like it does on our k8s cluster running on AWS with an external DNS.    

So where do I get a full fledged DNS server for my local machine?  
Wait! - Running inside of k8s cluster I have the kubeDNS up and running. This dns server resolves the service names associated to the domain `.cluster.local`.   

## dnsmasq to the rescue.   

By using the service `dnsmasq`provided for Mac OS and linux machines you can setup a local dns server listening for the domain `.cluster.local` and reference to the kubeDNS server to resolve the standard service names inside the k8s cluster. E.g. 

```http://proxy.local.svc.cluster.local
```

This references the entry point of our portal service entry point.   

TODO: more details on setup and get details on kubeDNS ip e.a.  

## The missing Link.   

How do I connect to the k8s network on my macos?  
After a lot of research I found a solution that worked for me.  `docker-tuntap-osx`.  
The basic idea is to provide a new network device and tunnel the traffic for the ip subnet containing all the service ip inside of my local k8s cluster.   

```bash
git clone ...
cd docker-tuntap-osx
./sbin/docker_tap_install.sh
# perhaps you have to restart the docker daemon on your host by hand after crash
route add -net 10.92.0.0/12 10.75.0.2
```

Now the traffic route is open for a final test. Lets say a service proxy is running in the namespace local on the k8s cluster. And you check results from a shell on your local machine.

```bash
kubectl get svc -n local proxyxxx
# TODO list output
dig @127.0.0.1 proxy.local.svc.cluster.local
# TODO add output
curl —verbose http://proxy.local.svc.cluster.local
```

# Resumee.   

Now I am able to install complex service meshes from different self contained systems on my local machine. In the next installment I will talk about possibilities to debug and develop components while these are associated to other services already running inside of my local k8s cluster. - Why should I want to do so?  Hey man, the world isn‘t always perfect!   

# RESOURCES.  

[Setup dnsmasq on OSX](https://gist.github.com/brablc/f48fef6336765212360ed3de66034b90).   
[docker-tuntap-osx](https://github.com/AlmirKadric-Published/docker-tuntap-osx).  
[Access Minikube services from Host on OSX]().  