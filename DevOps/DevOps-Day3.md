# Volume Management

> [!tip] File => Image => Container
>
> - creating container from image will create a read-write layer on top of the image
> - any changes made to the container will be saved in the read-write layer (not in the image)
> - this read-write layer will be removed when the container is stopped as it exists in the memory

> [!danger] Volumes
>
> - instead of saving the data in the read-write layer, you can save it in a volume (outside the container) - in the host machine (disk)

> [!important] mount point
>
> - to read the data from the disk (host machine) so both the container and the host machine can access the data

> [!tip] named vs notnamed(anonymous) volumes
>
> - named volumes: you can name the volume and use it in multiple containers
> - not named volumes: you can use the volume only in the container that created it ( can't control it )

```bash
docker container ls -a
```

> [!tip] to list all containers (running and stopped)

```bash
docker ps -a
```

```bash
docker container run -d -P --name apache1 httpd
```

> [!tip] create a container from the httpd image and name it apache1
>
> - Vim is not installed in the container
> - to make all images have vim installed, we add it to the Dockerfile

```dockerfile
FROM httpd
RUN apt update -y
RUN apt install vim -y
COPY . /usr/local/apache2/htdocs/
# this will copy the files from the current directory to the container (html file)
```

> [!tip] to build the image

```bash
docker image build -t new_apache .
```

> [!tip] to create a container from the new image

```bash
docker container run -d -P --name apache2 new_apache:latest
```

```bash
docker container exec -it apache2 bash
```

> [!tip] to enter the container (bash)
>
> - now we can use vim to edit the files in the container

> [!tip] ways to create a volume

```bash
docker volume create <volume_name>
```

> - to create a named volume

> [!tip] to create a container with a volume `--volume` | `-v`
>
> - named volume
> - `my_volume` is the name of the volume
> - `/usr/local/apache2/htdocs/` is the path in the container - this path is constant in all apache containers
> - this will create a volume and the data will be saved in the volume (won't get deleted when the container is stopped)

```bash
docker container run -d -P --name apache3 --v my_volume:/usr/local/apache2/htdocs/ new_apache:latest
```

> [!tip] `--mount` flag
>
> - to create a volume and mount it to the container
> - new way to create volumes

```bash
docker volume ls
```

> [!tip] to list all volumes

```bash
docker volume inspect my_volume
```

> [!tip] to get more information about the volume
>
> - `Mountpoint` is the path in the host machine where the data is saved (linux)
> - in case of windows, the path will be in the WSL
> - `Name` is the name of the volume
> - `Driver` is the driver used to create the volume (local, ...) - default is local

```bash
docker volume create new_volume
```

> [!tip] to create a new volume
>
> - this will create a new volume with the name `new_volume` this will is local in the host machine not related to the container

> [!tip] attach vs detach volumes
>
> - mouting the volume to the container is like attaching the volume to the container
> - to get mounts of the container
> - docker will mount the volume to the container automatically if the volume is created before the container
> - if it is not created, docker will create it and mount it to the container
>
> ```bash
> docker container inspect apache3
> ```
>
> - `Mounts` is the list of the volumes attached to the container
> - `source` is the path in the host machine
> - `destination` is the path in the container
> - any volume can be mounted to any container
> - any volume can be attached to multiple containers (any changes made to the volume will be reflected in all the containers)
>   > [!danger] cannot write to the volume from multiple containers at the same time

> [!tip] unnamed volume
>
> - to create a volume without a name (docker will create a random name for it)
> - won't get deleted when the container is removed manually
>   to delete it add the `--rm` flag to the container creation command to remove the volume when the container is removed
> - can be used for exclusion
> - mongoDB/Dockerfile defines a volume for the database data (won't get deleted when the container is removed) - unnamed volume - `VOLUME ["/data/db"]` to store the data in the volume (host machine - disk) not in the container

```bash
docker container run -d -P --name apache4 -v /usr/local/apache2/htdocs/ new_apache:latest
```

> [!tip] mount point
>
> - instead of closed volume (known path in linux - but not in windows - managed by docker)
> - used generally in development
> - mount specific path in the host machine to the container
> - we can use `pwd` to get the current path - to used directly in the command we need to add quotes around it `"$(pwd)"`
> - to mount the current directory to the container
>
> ```bash
> docker container run -d -P --name apache_mount -v "$(pwd):/usr/local/apache2/htdocs/" new_apache:latest
> ```
>
> - now we can edit the files in the current directory and the changes will be reflected in the container
> - 2 way binding (changes in the container will be reflected in the host machine and vice versa) same as the volume.
> - we can specify `type` , `source` , `target` , `readonly` in the `--mount` flag
> - use quotes around the path to avoid errors

```bash
docker container run -d -P --name apache_mount --mount type=bind,source="$(pwd)",target=/usr/local/apache2/htdocs/ new_apache:latest
```

```bash
docker container run -d -P --name apache_readonly -v "$(pwd):/usr/local/apache2/htdocs/:ro" new_apache:latest
```

---

# Break

---

## Networking

> [!tip] NAT vs Bridge vs Host Only
>
> - NAT: network address translation - used in virtualization
> - Bridge: used in docker - creates a bridge between the host machine and the container
> - Host Only: used in virtualization - the host machine and the guest machine can communicate with each other

> [!tip]
>
> ```bash
> docker network ls
> ```

---

### Docker File

> [!tip] to containerize an application
>
> - create a Dockerfile in the root directory of the application
> - define the base image
> - define the working directory
> - copy the files to the container
> - define the command to run the application

```dockerfile
FROM node
WORKDIR /app
# define the working directory in the container
COPY . .
# copy all the files in the current directory to the container in the /app directory
RUN npm install
CMD ["node", "app.js"]
```

> [!tip] to build the image
>
> - `.` is the path of the Dockerfile
> - `my_node` is the name of the image

> ```bash
> docker image build -t my_node .
> ```

> [!tip] to create a container from the image
>
> - `my_node` is the name of the image
> - `node_app` is the name of the container
> ```bash
> docker container run -d -p 3000:3000 --name node_app my_node
> ```
> - we have to use `-p` instead of `-P` as the port is not defined in the Dockerfile (not exposed in the image) 

>[!tip] `-p` vs `-P`
>
> - `-p` is used to map the port of the container to the port of the host machine
> - `-P` is used to map the port of the container to a random port of the host machine


>[!done] `.dockerignore`
>
> - to exclude files from the image
> - add the files to the `.dockerignore` file
> - the files in the `.dockerignore` file won't be copied to the container
```.dockerignore
node_modules
# exclude the node_modules directory
```

>[!done] `ENV` in the Dockerfile
>
> - to define environment variables in the Dockerfile
> - like `PORT` , `DB_URL` , ...

>[!done] `ARG` in the Dockerfile
>
> - to define arguments in the Dockerfile

