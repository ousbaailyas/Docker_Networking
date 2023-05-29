# Unlocking Container Connectivity: A Guide to Docker Networking Options

## Introduction

In the previous article, "Mastering Docker: A Comprehensive Guide to Containerization," we covered the fundamentals of Docker and how to work with containers effectively. Now, let's take a deep dive into Docker networking and explore advanced network configurations. In this article, we will discuss various network types and their use cases to help you become a Docker networking expert.

## What do you need?

Before we begin, make sure you have a solid understanding of Docker basics and have Docker installed on your system. Familiarity with networking concepts such as IP addressing, subnets, and routing will also be helpful.

## Let's do this!

We'll start our networking journey by exploring different network types in Docker and understanding their unique characteristics and benefits. Here are the networks we'll cover:

[TOC]

By the end of this article, you will have a solid understanding of these advanced Docker networking options and be equipped to design and deploy complex network architectures for your containerized applications.

Stay tuned for the next article in this series, where we will dive into container orchestration and explore how Docker Swarm and Kubernetes can take your containerization skills to the next level.

Remember, mastering Docker networking is crucial for building scalable and resilient containerized applications. Let's embark on this networking adventure together and unlock the full potential of Docker!

Note: If you haven't read the previous article, "Mastering Docker: A Comprehensive Guide to Containerization," I highly recommend checking it out before diving into this networking-focused article.

Keep an eye out for the upcoming article in the series: "Container Orchestration: Scaling and Managing Docker with Swarm and Kubernetes."

## Default Bridge Network

The default bridge network is a built-in networking option provided by Docker. It allows containers to communicate with each other using IP addresses on the same bridge network. Here's an explanation of the default bridge network and how to create and connect containers using it:

```
host -------------------------------------------------------|
|      container ----------------------                     |
|      |                              |                     |
|      |   eth0 172.17.0.2            |                     |
|      --------------------------------                     |
|                      |                                    |
|                     veth                                  |
|                      |                                    |
|                    docker0                                |
|                      |                                    |
|                    routing                                |
|                      |                                    |
|             |---------------------|                       |
|             | ech0 192.168.0.2    |                       |
|-----------------------------------------------------------|
```

```
docker network ls     
NETWORK ID     NAME                    DRIVER    SCOPE
ecd3c562f2a5   bridge                  bridge    local # Default
5c9d57ff4d13   host                    host      local 
92ceab827098   none                    null      local
```

### Explanation

- The default bridge network is created automatically when Docker is installed.
- Each container connected to the default bridge network is assigned an IP address from the network's address space.
- The **Bridge** network assigns IPs in the range of *172.17.x.x* to the containers within it.
- Containers on the default bridge network can communicate with each other using these assigned IP addresses.

How to Create and Connect Containers on the Default Bridge Network:

- To create a container on the default bridge network, you can use the `--network bridge` option when running the `docker run` command.
- By default, when you run a container without specifying a network, it is connected to the default bridge network.
- Containers connected to the default bridge network can communicate with each other using IP addresses.

Example:

```bash
docker run -d --name apache-web --hostname apacheweb -p 80:80 httpd:latest; docker run --tty --interactive --name kalirolling --hostname kalirolling -p 4433:443 kalilinux/kali-rolling 
```

In the above example, `apache-web ` and `kalirolling` are created and connected to the default bridge network sharing the same gateway.

Display information about bridge network:

```
docker network inspect bridge
```

Output

```json
[
    {
        "Name": "bridge",
        "Id": "ecd3c562f2a5e2990980c171f2bcb4ff2eee96c1a7eaa12a36cc7ed17c953cb9",
        "Created": "2023-05-27T15:13:38.593745704Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "0bb7d41c4c3af43ec286e32ac920f3a732055c5068e1ad6233775c4aab9344ef": {
                "Name": "kalilinuxOS",
                "EndpointID": "69c0450bb24fb100cb3f37fbc2b50d6a38fbc5731d903999e83be30305d72d0d",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "1f76df89959bcca241f51f71d6c293a611945fc09e2bb1f82147d34e758f080b": {
                "Name": "apache-web",
                "EndpointID": "fbb7e409e2e0a2a72087909b85328d5de0060feb8f67d964d59d1f374a8b021a",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "65535"
        },
        "Labels": {}
    }
]
```
Display information about created containers

```bash
docker container inspect apache-web
```

Output

```json
...
"Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "ecd3c562f2a5e2990980c171f2bcb4ff2eee96c1a7eaa12a36cc7ed17c953cb9",
                    "EndpointID": "69c0450bb24fb100cb3f37fbc2b50d6a38fbc5731d903999e83be30305d72d0d",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                }
            }
...
```

```bash
docker container inspect kalirolling
```

Output

```json
...
"Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "ecd3c562f2a5e2990980c171f2bcb4ff2eee96c1a7eaa12a36cc7ed17c953cb9",
                    "EndpointID": "fbb7e409e2e0a2a72087909b85328d5de0060feb8f67d964d59d1f374a8b021a",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
...
```

By utilizing the default bridge network, you can create and connect containers, allowing them to communicate seamlessly using IP addresses.

```
docker exec -it kalirolling bash
┌──(root㉿kalirolling)-[/]
└─# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=4.27 ms
```

## User-defined Bridge Network

A user-defined bridge network is a custom bridge network created by the user in Docker. It provides isolation and connectivity between containers on the same network. Here's an overview of user-defined bridge networks, how to create and manage them, and how containers communicate within the same bridge network:

```
host -------------------------------------------------------|
|  cont1 ------------- cont2 -------------  cont3 ----------|
|  | eth0 172.17.0.2 | | eth0 172.18.0.2 | | eth0 172.18.0..|
|  ------------------- ------------------  -----------------|
|        |                  |                     |         |
|      veth             -- veth                  veth       |
|        |              |                         |         |
|     docker0       mynetwork --------------------|         |
|        |              |                                   |
|        |----------- routing                               |
|                       |                                   |
|            |---------------------|                        |
|            | ech0 192.168.0.2    |                        |
|-----------------------------------------------------------|
```

Overview of User-defined Bridge Networks:

- User-defined bridge networks are created using the `docker network create` command.
- They allow containers to communicate with each other using container names as hostnames.
- User-defined bridge networks provide better isolation and control over network settings compared to the default bridge network.
- Containers connected to the same user-defined bridge network can freely communicate with each other.

### Creating and Managing Custom Bridge Networks

- To create a user-defined bridge network, you can use the `docker network create` command followed by the network name.
- You can specify additional options such as subnet, gateway, and driver when creating the network.
- Use the `docker network ls` command to list all available networks and view their details.
- The `docker network inspect` command provides detailed information about a specific network.

### Communication between Containers within the Same Bridge Network

- Containers connected to the same user-defined bridge network can communicate with each other using their container names.
- Each container's name is resolved to its corresponding IP address within the network.
- This enables seamless communication between containers without the need to expose specific ports.

Example:

```
docker network create mynetwork
```
```
docker network ls     
NETWORK ID     NAME                    DRIVER    SCOPE
ecd3c562f2a5   bridge                  bridge    local
5c9d57ff4d13   host                    host      local
92ceab827098   none                    null      local
17193effb774   mynetwork               bridge    local 
```

Display information about created network:

```
docker network inspect mynetwork
```

 Output

```json
[
    {
        "Name": "mynetwork",
        "Id": "8e90a5a80268f431cfc95000812cdcbabe1e389da70a041eff3e77dc93d25503",
        "Created": "2023-05-27T19:25:13.185583425Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

And

```bash
docker run -d --name apache-web --hostname apacheweb --network mynetwork --link apache-web:bridg-network -p 80:80 httpd:latest; docker run --tty --interactive --name kalirolling --hostname kalirolling --network mynetwork --link kalirolling:bridg-network -p 4433:443 kalilinux/kali-rolling
```

`--link` option: When running containers with the CLI, you can use the `--link` option to create a network link between containers. This allows you to reference the linked container by its hostname. For example:

```bash
docker exec -it kalirolling bash
┌──(root㉿kalirolling)-[/]
└─# ping apache-web
PING apache-web (172.18.0.2) 56(84) bytes of data.
64 bytes from apache-web.mynetwork (172.18.0.2): icmp_seq=1 ttl=64 time=1.13 ms
```

By utilizing user-defined bridge networks, you can create custom networks, manage network settings, and enable communication between containers using container names within the same network.

In Docker, the network named "host" is a special network mode that allows containers to share the network stack of the Docker host. When a container is connected to the "host" network, it bypasses the default network isolation provided by Docker and gains direct access to the host's network interfaces and ports.

## Host

When a container is running in "host" network mode:

```
                               Home Network
                                    |                         
                              Host interface
                                    |
        ---------------------------------------------------------
             |                      |                      |    |
        Container 1          Container 2         Container N   ...
             |                      |                      |
        eth0: <IP1>            eth0: <IP2>            eth0: <IPN>
```



1. Network namespace isolation: Normally, Docker creates a separate network namespace for each container, providing network isolation between containers. However, in "host" network mode, the container does not have its own network namespace. Instead, it uses the same network namespace as the Docker host. This means that the container shares the same IP address, network interfaces, and routing table as the host.
2. Port mapping: In "host" network mode, the container's network services are directly bound to the host's network interfaces. There is no need to explicitly publish or map ports, as the container's ports are automatically exposed on the host's IP address. This allows services running in the container to be accessed on the same port numbers as if they were running directly on the host.
3. Network performance: By using the "host" network mode, containers can achieve maximum network performance since they bypass the additional network layers and overhead introduced by Docker's default networking setup.

The "host" network mode is particularly useful when you need to achieve maximum network performance or when you want to run containerized applications that rely on specific host network configurations or dependencies. However, it's important to note that using the "host" network mode eliminates the network isolation between containers and exposes the container's services directly on the host's network interfaces, potentially increasing security risks.

To run a container in "host" network mode, you can use the `--network=host` option when running the `docker run` command.

```
bashCopy code
docker run --network=host my_image
```

It's worth mentioning that the "host" network mode is not available on all operating systems. It is primarily supported on Linux-based Docker hosts, as network namespace sharing is a feature provided by the Linux kernel.

## MACVLAN Network

The MACVLAN network in Docker allows you to create multiple virtual network interfaces with their own MAC addresses on a physical network interface. Here's an overview of the MACVLAN network, its advantages, how to configure and utilize it, and its trunking support with MACVLAN 802.1q:

```
Host --------------------------------------------
        Container 1                Container 2    
        eth0: <IP1>                eth0: <IP2>  
             |                           |
             |                           |
        macvlan10                    macvlan20
         eth0.10                      eth0.20
             |                           |
             |                           |
             ------------ eth0 -----------
                            |
                     external network
               |                          |
            VLAN10                      VLAN20
--------------------------------------------------
```

### Introduction to MACVLAN Network and Its Advantages

- MACVLAN is a network driver in Docker that provides a way to assign MAC addresses to containers.
- It allows containers to have their own unique MAC addresses, making them appear as separate devices on the network.
- MACVLAN provides better network performance and scalability compared to the default bridge network.
- It enables containers to directly communicate with other devices on the physical network.

### Configuring and Utilizing MACVLAN Networks

- MACVLAN networks can be created using the `docker network create` command with the `--driver macvlan` option.
- You need to specify the parent network interface and the subnet range when creating a MACVLAN network.
- Containers can be connected to the MACVLAN network using the `--network` option when running the container.
- Each container connected to the MACVLAN network will have its own MAC address and IP address from the specified subnet range.

### Trunking Support with MACVLAN 802.1q

- MACVLAN 802.1q provides support for VLAN trunking, allowing containers to be part of specific VLANs.
- Trunking enables containers to communicate with other devices within the same VLAN and provides isolation from other VLANs.
- To configure MACVLAN 802.1q, you need to specify the VLAN ID when creating the MACVLAN network.

Example:

```
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 mymacvlan
```

```
docker network ls     
NETWORK ID     NAME                    DRIVER    SCOPE
ecd3c562f2a5   bridge                  bridge    local
5c9d57ff4d13   host                    host      local
92ceab827098   none                    null      local
f8d537478199   mymacvlan               macvlan   local
17193effb774   mynetwork               bridge    local 
```

And

```bash
docker run -d --name apache-web --hostname apacheweb --network mymacvlan -p 80:80 httpd:latest; docker run --tty --interactive --name kalirolling --hostname kalirolling --network mymacvlan -p 4433:443 kalilinux/kali-rolling
```

In the above example, a MACVLAN network named "mymacvlan" is created, specifying the subnet, gateway, and parent interface. Containers `container1` and `container2` are then created and connected to the "mymacvlan" network.

By utilizing the MACVLAN network, you can assign unique MAC addresses to containers, provide direct network access, and enable VLAN trunking for enhanced network isolation and communication.

## IPVLAN (L3) Network

The IPVLAN (L3) network mode in Docker enables containers to communicate at the network layer (L3), allowing for separate network routing and IP addressing within a shared physical network. Here's an explanation of the IPVLAN (L3) network and how to leverage it for container communication:

```
                               Docker Host
                                    |
                            IPVLAN Interface
                                    |
        ---------------------------------------------------------
             |                      |                      |    |
        Container 1          Container 2         Container N   ...
             |                      |                      |
        eth0: <IP1>            eth0: <IP2>            eth0: <IPN>
```

### Exploring IPVLAN (L3) Network Mode

- IPVLAN (L3) is a network mode in Docker that provides separate network routing and IP addressing for containers within a shared physical network.
- Containers connected to an IPVLAN (L3) network can communicate with each other using unique IP addresses and have their own network routing table.
- This network mode is useful when you want to create isolated network segments with distinct routing capabilities for different containers.

### Leveraging IPVLAN Networks for Container Communication at L3:

- IPVLAN (L3) networks can be created using the `docker network create` command with the `--driver ipvlan` option and specifying the `--subnet` and `--gateway` options.
- Containers can be connected to the IPVLAN network using the `--network` option when running the container.
- Each container connected to the IPVLAN (L3) network will have its own IP address within the specified subnet and will be able to communicate with other containers via IP routing.

Example:

```bash
docker network create -d ipvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o ipvlan_mode=l3 myipvlan
docker run -d --name container1 --network myipvlan image1
docker run -d --name container2 --network myipvlan image2
```

In the above example, an IPVLAN (L3) network named "myipvlan" is created, specifying the subnet, gateway, and setting the `ipvlan_mode` to "l3". Containers `container1` and `container2` are then created and connected to the "myipvlan" network.

By utilizing IPVLAN (L3) networks, you can establish separate network routing and IP addressing for containers within a shared physical network. This allows for more flexible network configurations, isolation between containers, and efficient routing at the network layer.

## The Overlay Network

The overlay network in Docker is designed for multi-host container communication, allowing containers running on different Docker hosts to seamlessly communicate with each other. Here's an overview of the overlay network and how to work with it:

                Docker Host 1         Docker Host 2
                      |                     |
                Overlay Network       Overlay Network
                      |                    |
          -------------------------------------------
         |                     |                    |    
    Container 1         Container 2            Container N
         |                     |                     |
    eth0: <IP1>           eth0: <IP2>          eth0: <IPN>

### Overlay Networks in Docker:

- Overlay networks provide a scalable and flexible solution for container communication across multiple Docker hosts.
- They enable containers to communicate with each other using familiar networking constructs such as DNS resolution and service discovery.
- Overlay networks use an encapsulation mechanism to encapsulate container traffic and transport it across hosts.

### Creating Overlay Networks for Multi-Host Container Communication

- Overlay networks can be created using the `docker network create` command with the `--driver overlay` option.
- When creating an overlay network, you can specify options such as the subnet, gateway, and encryption settings.
- Containers can then be connected to the overlay network using the `--network` option when running the container.

### Working with Swarm Mode and Overlay Networks

- Overlay networks are particularly useful in Docker Swarm mode, where they enable container communication across the swarm.
- In Swarm mode, overlay networks can be created using the `docker network create` command or automatically by services defined in a Docker Compose file.
- Services running on different Docker Swarm nodes can communicate with each other seamlessly over the overlay network.

Example:

```bash
# Create an overlay network
docker network create --driver overlay myoverlay

# Run a service on the overlay network
docker service create --name myservice --network myoverlay myimage

# Scale the service across multiple nodes
docker service scale myservice=3
```

In the above example, an overlay network named "myoverlay" is created using the `docker network create` command. Then, a service named "myservice" is created using the `docker service create` command, and it is connected to the "myoverlay" network. Finally, the service is scaled to run on multiple nodes in the Docker Swarm.

By leveraging overlay networks, you can establish communication between containers running on different Docker hosts, enabling seamless and scalable multi-host container deployments. Overlay networks, particularly in conjunction with Docker Swarm mode, provide powerful capabilities for building distributed and highly available applications.

Happy networking with Docker!

Please don't forgot to visit relevant arctic about:

1. **"Mastering Docker: A Comprehensive Guide to Containerization"**: Once you have a solid understanding of Docker networking, it's highly recommended to dive into this comprehensive guide. It covers fundamental Docker concepts, container management, image creation, Docker Compose, container orchestration with Kubernetes, and more. This guide will further enhance your knowledge and proficiency in containerization. [Access the guide here.](https://example.com/mastering-docker)
2. **"Docker Compose: Simplifying Container Orchestration"**: Additionally, to simplify the management and orchestration of your multi-container applications, exploring Docker Compose is crucial. This article focuses specifically on Docker Compose, explaining its syntax, features, and benefits for defining and managing multi-container applications. [Read the article here.](https://example.com/docker-compose-orchestration)
