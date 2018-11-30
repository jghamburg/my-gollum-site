# My way to Amarillo - or a better local development with k8s (part 1)  

## Summary for the Impatient  

This article describes the setup of a local dns service to call k8s services from the host machine. It is validated on macos High Sierra 10.13.6. With this setup you can use fqn service names to resolve redirect problems on your local development machine.  
This article is based on solutions on macos but contains references to Linux as well.

## The story begins  

As developer of the portal pirates team I work a little over one year with container orchestration. First on the basis of Mesos / Marathon and DC/OS and now for the last two month on basis of Kubernetes, aka k8s.  

During all this time we were searching for a reasonable setup of local development environment.  
On basis of k8s now the time is right to redefine the phrase  

> Works on my machine!  
> — Lonely Developer

![Network Topology](./Network-Topology.jpg)

To setup a local k8s cluster using Docker for Desktop or Minikube is straight forward. And is left to the humble reader of this article.  
Simple enough but ...   
Running a service mesh of multiple components building a self contained system (SCS) worked straight out of the box.  -  (referencing each other by using k8s service definitions).   
The interesting part started out as I tried to reference different self contained systems as required by developing a central portal service and an associated vertical system providing a business service.  
The simples way to install this scenario on a local devops machine is to match access points of the different SCS to `localhost` with a specific port per service. From a browser perspective this works. But if one container tries to resolve a redirect configuration of the kind

```
http://localhost:8090/myservice
```

Now you are traped!   

> What would I give for a real DNS server running on my machine!  

With DNS names resolving to individual ip addresses and not on the loopback device would definitely solve the problem! - Like it does on our k8s cluster running on AWS with an external DNS.    

So where do I get a full fledged DNS server for my local machine?  
Wait! - Running inside of k8s cluster I have the kubeDNS up and running. This dns server resolves the service names associated to the domain `.cluster.local`.   

## dnsmasq to the rescue.   

By using the service `dnsmasq` provided for Mac OS and linux machines you can setup a local dns server listening for the domain `.cluster.local` and reference to the kubeDNS server to resolve the standard service names inside the k8s cluster. E.g. 

```
http://proxy.local.svc.cluster.local
```

This references the entry point of our portal service.   

To retrieve kubeDNS details:  

```bash
kubectl get svc -n kube-system kube-dns
# NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
# kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP   37d
```

### setting up dnsmasq (macos)  

```bash
# install binaries
brew install dnsmasq
# setup directory structure
mkdir -pv $(brew --prefix)/etc/
# setup svc.cluster.local domain
echo 'address=/.svc.cluster.local/10.96.0.10' >> $(brew --prefix)/etc/dnsmasq.conf
# change port for High Sierra
echo 'port=53' >> $(brew --prefix)/etc/dnsmasq.conf
# autostart - now and after reboot
sudo brew services start dnsmasq
```

To restart the service later on you can use  

```bash
sudo brew services restart dnsmasq
# Stopping `dnsmasq`... (might take a while)
# ==> Successfully stopped `dnsmasq` (label: homebrew.mxcl.dnsmasq)
# ==> Successfully started `dnsmasq` (label: homebrew.mxcl.dnsmasq)
```

Now setup the required resolver details

```bash
sudo mkdir -v /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev'
```

That's it! You can run 

```bash
scutil --dns 
# DNS configuration
# resolver #1
#   nameserver[0] : 8.8.8.8
#   nameserver[1] : 8.8.4.4
#   if_index : 5 (en0)
#   flags    : Request A records
#   reach    : 0x00000002 (Reachable)
# ...
# resolver #9
#   domain   : svc.cluster.local
#   nameserver[0] : 10.96.0.10
#   flags    : Request A records
#   reach    : 0x00000002 (Reachable)
# ...
```

to show all of your current resolvers, and you should see that all requests for a domain ending in .dev will go to the DNS server at 127.0.0.1.

And here you see a resolution example:

```bash
dig @127.0.0.1 auth.local.svc.cluster.local
# ; <<>> DiG 9.10.6 <<>> @127.0.0.1 auth.local.svc.cluster.local
# ...
# ;; ANSWER SECTION:
# auth.local.svc.cluster.local. 0	IN	A	10.96.0.10
# ...
```

## The missing Link.   

How do I connect to the k8s network on my macos?  
After a lot of research I found a solution that worked for me: [docker-tuntap-osx].  
The basic idea is to provide a new network device and tunnel the traffic for the ip subnet containing all the service ip inside of my local k8s cluster.   

```bash
git clone https://github.com/AlmirKadric-Published/docker-tuntap-osx.git
cd docker-tuntap-osx
./sbin/docker_tap_install.sh
# perhaps you have to restart the docker daemon on your host by hand after crash
# After the ocker daemon is up and running again you will need to bring up the network interfaces every time the docker Host Virtual Machine is restarted:
./sbin/docker_tab_up.sh
# 
# and don't forget to add network route configuration for k8s services  
route add -net 10.92.0.0/12 10.75.0.2  
```

Now the traffic route is open for a final test. Lets say a service proxy is running in the namespace local on the k8s cluster. And you check results from a shell on your local machine.

```bash
kubectl get svc -n local proxy
# NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
# proxy     ClusterIP   10.107.76.229   <none>        80/TCP    34d
dig @127.0.0.1 proxy.local.svc.cluster.local
# ...
# ;; ANSWER SECTION:
# proxy.local.svc.cluster.local. 0 IN	A	10.96.0.10
curl —verbose http://proxy.local.svc.cluster.local
```

# Resumee.   

Now I am able to install complex service meshes from different self contained systems on my local machine and use redirect techniques inside and outside of the k8s cluster. 


In the next installment I will talk about possibilities to debug and develop components while these are associated to other services already running inside of my local k8s cluster. - Why should I want to do so?  Hey man, the world isn‘t always perfect!   

# RESOURCES.  

* [Setup dnsmasq on OSX][Setup dnsmasq on OSX]
* [docker-tuntap-osx][docker-tuntap-osx]
* [Access Minikube services from Host on OSX][Access Minikube services from Host on OSX]  

[Setup dnsmasq on OSX]: https://gist.github.com/brablc/f48fef6336765212360ed3de66034b90   
[docker-tuntap-osx]: https://github.com/AlmirKadric-Published/docker-tuntap-osx  
[Access Minikube services from Host on OSX]: https://stevesloka.com/access-minikube-services-from-host/  
