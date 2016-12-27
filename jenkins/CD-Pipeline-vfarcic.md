# First experiments with CD-Pipelines  

Based on the [presentation of Victor Farcic][vfarcic-presentation] I started my exploration
on CD-Pipelines on my MacBook Pro.  
The experiment started on  

* MacBook Pro 15, i7, 16g memory, macOS Sierra 10.12.2  
* Parallels 12  
* docker 1.13.0-rc4, build 88862e7  
* docker-machine 0.9.0-rc2, build 7b19591  
* ...

## The basic setup  

	# set docker machine provider
	export MACHINE_PROVIDER=parallels
	git clone https://github.com/vfarcic/cloud-provisioning.git
	cd cloud-provisioning
	docker-machine create -d ${MACHINE_PROVIDER} registry
	docker-machine create -d ${MACHINE_PROVIDER} --engine-insecure-registry $(docker-machine ip   registry):5000 base
	git clone https://github.com/vfarcic/go-demo.git
	cd go-demo
	eval $(docker-machine env base)
	

[vfarcic-presentation]: http://vfarcic.github.io/jenkins-swarm/workshop.html  
