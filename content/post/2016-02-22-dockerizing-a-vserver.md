---
title: Dockerizing a VServer or How to escape the local port hell
layout: post
date: 2016-02-22
draft: true
categories:
  - Docker

---
Having a private virtual server is relatively affordable.
If you have at least some experience with system administration, running an own server gives you some advantages.
You can gain some experience with linux, get some insight into how linux servers work and you can choose which services you use and you still know, where your data resides.

This all sounds promising, at least to me, and it also solves many problems where the services offered by some providers are not configured the way I like them to be.
If a Jabber server does not offer the extensions you want, the only way to get what you want is to run your own server.
But once you start configuring your server, you stack up more and more services you want to have.
This is where the problems start.

One example of an annoying problem comes up because more and more services run as *web services*.
The default port for web services is port 80 for unencrypted and 443 for SSL-encrypted traffic.
But since you want to run multiple services, only one of these services can listen on the default port.
For the others, you have to find alternative ports.
The de facto default alternative port for web services is 8080, but starting the the third service, you need to use ports, that are not standard.
This also means that changes get higher that you run into conflicts with other services.

The next problem is that all of these services should be accessible over the Internet and you want to protect your data when it is transmitted over the public network.
The needed SSL-encryption needs certificates, which are available for free from [StartSSL](https://www.startssl.com/) and [Let's Encrypt](https://letsencrypt.org/).
To simplify the configuration, you want to run a single web server, which acts as a proxy for the other web services and handles the SSL-encryption for all web services exposed by the server.
This, on the other hand, means, that all you web services need to be accessible on a different local port such that your proxy can distribute the access to the right service.

To summarize: The more services you stack up on your server, the more difficult it gets to tame the local port hell.
Every service needs a new port, many services have the same default ports and reconfiguring the services to use different ports is sometime rather difficult.


## Docker Containers

On my search for an alternative technique for server administration, I came upon a relatively new technology called [Docker](https://www.docker.com/).
Docker is called a container manager.
Instead of organizing services as processes on a single server or virtual machines, all services are deployed as a container.
A container contains a service together with the context required to run the service.
This can mean that specific system libraries are available in the container or that specific programs are available in the container that are needed for the service to be operational.
The idea is the same as for virtual machines: Processes are isolated from each other such that dependencies of different services do not clash even though both services run on the same server.
This is a comfortable way to run multiple services on a single physical server.
The difference between virtual machines and containers is, that for virtual machines the resources usually have to be fixed before starting the VM.
Most virtual machines do not use all the resources allocated for them which wastes valuable resources and restricts the number of services that can run concurrently on the same server.
The concept of containers solves this issue.

A container is intended to be light-weight in contrast to virtual machines.
Usually, a container only executes a single service which reduces the resources needed by the container.
These resources like memory, computation power and disk space are dynamically allocated to a container.
Since a container should be responsible for only a single service, multiple containers have to cooperate, since more complex service depend on other service to work.
A business application usually needs a database to work, sometimes multiple databases or additional services like a mail server have to be provided to the application.
Both virtual machines and containers run on a virtual environment independent of the hardware.
This allows to move VMs and containers from a physical machine to the other and run them without modification.
[Docker Swarm](https://www.docker.com/products/docker-swarm) and [Google Kubernetes](http://kubernetes.io/) allow to distribute Docker containers to different physical servers to balance the load of each server.
If a server encounter to high load, a new instance of a service container can be spawned and the load can be distributed among the servers running the same container.

For those of us who have a virtual server, spawning multiple instances of a container to distribute the load is not a use case.
But containers can also be used to solve the local port hell.


## Using Docker containers to manage local ports

TODO
