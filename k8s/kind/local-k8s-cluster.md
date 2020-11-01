# A local k8s cluster to your service  

For quite some time I am using Docker for Desktop on my Mac to provide a k8s cluster.  
Afer some discussions on local development with my colleagues I stumbled over kind - Kubernetes in Docker.  

What intrigued me is the possibility to configure multiple nodes and many other things. As a devops for AWS EKS services this gives me a wanderful experimental ground on my local machine. Before I then wander of to the realms of the clouds.  

	Do your local experiments before you go big in the cloud.

So how to start?  

Like the name indecates without a docker installation you are simply nothing.  
I will leave this to your own research. Me I use Docker for Desktop on Mac.  

To install kind just follow instructions on the [kind website](https://kind.sigs.k8s.io/).  
Or use this shortcut - no explanations here.  

```bash
brew install kind
# 🍺  /usr/local/Cellar/kind/0.9.0: 8 files, 9.2MB
```

To jump into the first usage I prepared a configuration file so I can use my networking extension like described in my article [local development](../k8s-local-development-1.md).  
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.18.8@sha256:f4bcc97a0ad6e7abaf3f643d890add7efe6ee4ab90baeb374b4f41a4c95567eb
networking:
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/12"
```

I use the prebaked docker images on dockerhub - check this for specific k8s versions or ignore it and use the latest.  

```bash
# time kind create cluster --config ./kind-cluster.yaml
# Creating cluster "kind" ...
#  ✓ Ensuring node image (kindest/node:v1.18.8) 🖼
#  ✓ Preparing nodes 📦
#  ✓ Writing configuration 📜
#  ✓ Starting control-plane 🕹️
#  ✓ Installing CNI 🔌
#  ✓ Installing StorageClass 💾
# Set kubectl context to "kind-kind"
# You can now use your cluster with:
# 
# kubectl cluster-info --context kind-kind
# 
# Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
# kind create cluster --config ./kind-cluster.yaml  5,35s user 2,52s system 24% cpu 31,696 total
kubectl cluster-info --context kind-kind
# Kubernetes master is running at https://127.0.0.1:54843
# KubeDNS is running at https://127.0.0.1:54843/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

