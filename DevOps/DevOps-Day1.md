> [!faq] Why do we need Version Control?
>
> - to keep track of changes
> - to collaborate with others

> [!important] Centralized Version Control System (CVCS)
>
> - is a single ==server== that contains all the ==versioned== files and the history of the changes
> - get the latest version of the files from the server
> - update the server with the changes

![](Pasted%20image%2020240430095149.png)

> [!tip] Distributed Version Control System (DVCS)
>
> - instead of the repository being on a single server, repositories are ==distributed== among all the users and can be ==synced== with repository on the server
> - every user has a ==local== copy of the repository with the full history of the project (workd as a server)
> - can rebuild the entire repository from any user's local copy
> - can work offline
>   > [!warning] problem with large files

![](Pasted%20image%2020240430095406.png)

> [!danger] Git
>
> - is a DVCS used to track changes in files

---

### Agile

![](ITI/DevOps/Pasted%20image%2020240501124304.png)

> [!tip] Waterfall vs Agile
>
> - propblem with Waterfall:
> - long time to deliver the product
> - no feedback from the customer
>   > [!done] Agile
>   >
>   > - plan, design, develop, test, deliver, feedback, repeat
>   > - cycle of development is short

![](ITI/DevOps/Pasted%20image%2020240501124431.png)

> [!tip] Scrum
>
> - is a framework for developing, delivering, and sustaining complex products
> - is a subset of Agile

![](ITI/DevOps/Pasted%20image%2020240501124658.png)

---

## Devops

> [!tip] Dev vs Ops || speed vs stability
>
> - Dev: want to release new features quickly
> - Ops: want to keep the system stable
> - with agile the raise of agile practices, the gap between Dev and Ops increased so DevOps was introduced to bridge the gap

> [!example] problems that could raise between Dev and Ops
>
> - performance issues: like developers having db and web server on the same machine so the production server will be slow

---

## physical servers

> [!danger] the problem with physical servers
>
> - expensive
> - hard to scale
> - security issues: 2 apps on the same server can access each other's data

> [!tip] Virtualization
>
> - is the process of creating a virtual version of something like a server, storage device, network or even an operating system

> [!important] Hypervisor
>
> - is a software that creates and runs virtual machines
> - hardware needs to support virtualization
> - 2 types of hypervisors:
> - Type 1: runs directly on the hardware (bare-metal) like VMware ESXi, Microsoft Hyper-V
> - Type 2: runs on the host operating system like VMware Workstation, Oracle VirtualBox

> [!danger] the problem with virtualization to host applications
>
> - still expensive (licensing, ...)
> - need to manage the OS (updates, patches, ...)

---

# Break

---

# Linux

> [!important] Linux File System
>
> - windowns: can have multiple partitions (C:, D:, E:, ...)
> - /: root directory (like C: in windows) - contains all the files and directories - is the top of the file system hierarchy
> - `/bin`: contains binary files (commands)
> - `/users`: contains user binaries
> - any path that starts with `/` is an absolute path

### Linux Kernel Features

> [!important] chroot
>
> - in the start of th 2000s, chroot was used to create a jail root for a user
> - change root
> - can create local root (jail root) and can be like a closed environment for a user

> [!warning] Namespaces & cgroups
>
> - cgoups: control groups - control permissions and resources
> - na mespaces: control pid(process id)
>   > [!done] instead of using virtualization
>   >
>   > - use namespaces and cgroups to isolate processes like permissions, memory access, ports, ...
>   > - using built-in features of the kernel to host multiple applications on the same machine and isolate them from each other (==linux containers==)
>   > - first mention of containers

---

# Docker

> [!tip] DotCloud
>
> - was a platform as a service company
> - PaaS: platform as a service - a cloud computing model that provides a platform allowing customers to develop, run, and manage applications without the complexity of building and maintaining the infrastructure
> - they built a tool called Docker (open source)
> - this tool manage linux containers (LXC) and abstracted the complexity of managing containers
> - can be used through API (CLI, HTTP, ...)
> - added the image concept to containers

---

### Docker Image

> [!warning] Hosting .NET app
>
> - need to install .NET SDK on the server
> - need to install IIS
>   > [!danger] problem
>   >
>   > - what if the app needs a specific version of .NET SDK
>
> > [!done] Docker Image
> >
> > - Dividing the changes into layers (like git)
> > - each layer is a change in the file system
> > - each layer is read-only
> > - Layers example: (base image(ubuntu), install .NET SDK, install IIS, deploy the app)
> > - we can use the same base image for multiple apps and only change the layers that are different like .NET SDK, ...
> > - when you run a container, a new layer is added on top of the image (read-write)
> > - when you stop the container, the read-write layer is removed
> > - when you run the container again, a new read-write layer is added

> [!tip] Library container
>
> - allows to deal with namespaces and cgroups without using linux containers (LXC)
> - cause LXC had problems due to different versions as it was not part of the kernel
> - this library container was the first step to create Docker

> [!warning] OCI (Open Container Initiative)
>
> - is a standard for containers: some rules and guidelines for containers
> - library container was created by Docker before OCI
> - but library container was not a standard
> - so docker created ==runC== which is a standard implementation of OCI
> - which is a wrapper around (library container)
> - Docker (library container, runC) => all written in Go
> - ==runC== not the same as Crun

> [!important] Docker inc
>
> - is the company that created Docker (software)

> [!warning] Containerd
>
> - `d` at the end of the name means daemon: a background process
> - is a container runtime
> - containerd is a daemon that manages the complete container lifecycle of its host system, from image transfer and storage to container execution and supervision to low-level storage to network attachments and beyond
> - containerd is available as a daemon for Linux and Windows, which can manage the complete container lifecycle of its host system: image transfer and storage, container execution and supervision, low-level storage, network attachments, and beyond
> - communicates with runC to run containers
> - there will be problems running containers with kubernetes
> - there are other container runtimes like (Crun, CRI-O, ...)

---

## Windows

> [!tip] Windows doesn't support containers like Linux
>
> - Windows used to have Hyper-V containers (full virtualization)
> - wsc (windows server containers) gen2 hypervisors => mini VM
> - windows containers are not as lightweight as linux containers (as they are full virtualization)

> [!tip] WSL (Windows Subsystem for Linux)
>
> - this will have a linux kernel (namespace, cgroups, ...)
> - can run any linux container on windows

### installation

> [!important] when you install docker, you will get 3 things:
>
> - Client (windows: CLI + GUI): calls the API
> - API: allows sytem calls | linux: socket
> - Docker Daemon: manages the containers (run, stop, ...)

> [!tip] Docker Desktop
>
> - contains the client, API, and daemon

> [!warning] Docker objects
>
> - ==docker file==: contains the instructions to build the image (like a recipe) - text file
> - ==docker image==: is the result of building the docker file (like a class) - read-only
> - ==docker container==: is the running instance of the image (like an object) - read-write layer is added on top of the image - read-only layers are shared between containers (pointer to the image)

>[!tip] Copy-on-write
>
> - when you run a container, a new read-write layer is added on top of the image
> - when you stop the container, the read-write layer is removed
> - any changes made to the container are saved in the read-write layer
> - when you run the container again, a new read-write layer is added

>[!warning] to edit the code in the container
> - 1. edit the code in the container this will affect only the container
> - 2. edit the code in the image and rebuild the image this will affect all the containers that are created from this image

>[!danger] to share images between developers
> 
> - 1. push the image to a registry (like Docker Hub) - public or private
> - 2. pull the image from the registry

> [!tip] Registry
>
> - is a central place where you can store and distribute images
> - used to pull images from it and push images to it
> - Docker Hub is a public registry
> - you can create your own private registry
> - companies usually use private registries to store their images (Azure Container Registry, ...)

---
## Docker Hub

> [!important] Docker Hub
>
> - is a public registry
> - you can pull images from it and push images to it
> - you can create your own private registry
> - companies usually use private registries to store their images (Azure Container Registry, ...)

 

 

 