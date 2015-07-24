Docker Images
=============


## Docker Image Concepts

### Filesystem Basics
* A docker image is made up of filesystems layered over each other.
* The base is a boot filesystem, bootfs, which resembles the linux boot filesystem.
* When a container boots, the bootfs is moved into memory and unmounted.
* Docker layers a root filesystem, rootfs, on top of the boot filesystem.
* The rootfs can be one or more operating systems (ie. Debian, Fedora, Ubuntu filesystem).  
* The rootfs stays in read-only mode
* Docker uses a union mount to load more read-only filesystems onto the root filesystem.  
* A union mount is a mount that allows several filesystems to be mounted at one time to appear as one filesystem.
* The union mount overlays the filesystems on top of one another so the resulting filesystem contains files and directories
from any or all of the underlying filesystems
* Docker calls these filesystems images.  Images are layered on top of one another above the base image.
* Finally, docker mounts a read-write filesystem on top of any layers below.  This is where container processes will run.
* When a file from a parent filesystem is changed, it is written to the top read-write filesystem.  The read-only version 
still exists but is now hidden underneath the copy.  This is called "copy on write".

### Docker Image Tags
* Tags mark together a series of image layers that represent a specific image (ie. 12.04 tag for ubuntu)
* Specific tags of an image can be referred to using the repository name with a colon and a tag name (ie. ubuntu:12.04, ubuntu:latest, etc.)
* It is a best practice to build images from a specific tag.

### Docker Repositories
* Two types of repositories:  user repositories (contributed by individual users) and top-level repositories (curated by Docker Inc. and select vendors)


## Docker Image Commands

### Listing Docker Images
Lists all of the docker images
```bash
$> docker images
```

Lists the docker images matching the specified name
```bash
$> docker images fedora
```

### Pulling Docker Images from a Repository
Explicitly pulls a specific image from the repository
```bash
$> docker pull ubuntu:12.04
```

Implicitly pulls a specific image from the repository (if not existing) and runs it
```bash
$> docker run -i -t ubuntu:12.04 /bin/bash
```

### Pushing Docker Images to a Repository
```bash
$> docker push user1/example_server
```

### Deleting a Docker Image
```bash
$> docker rmi user1/example_server
```

### Searching for Docker Images

Searches for docker images by name
```bash
$> docker search nginx
```


## Building a Docker Image with the Commit Command
The docker commit command creates a new image by committing 
changes to a container's filesystem to a new image.  docker commit only commits the differences between the image
the container was created from and the current state of the container (making updates lightweight).
```bash

# run a docker container
$> docker run --name example_server -i -t ubuntu:12.04 /bin/bash

# install apache on the running container
root@example_server $> apt-get -y install apache2

# commit the changes to container to create a new docker image
$> docker commit -m "Adding apache2 to the server" 43e2ab99809ewp8 example_user/apache2 

```


## Building a Docker Image with a Dockerfile and the Build Command

### Docker Build Execution
* Docker runs the Dockerfile from a specified location.  The directory containing the Dockerfile
* Docker runs a container from the image
* An instruction runs and makes a change to the container
* Docker runs the equivalent of docker commit to commit a new layer
* Docker runs a new container from this new image.
* Then next instruction in the file is executed and the process repeats until all instructions have been executed
* If an instruction fails, the build produces an image containing filesystem layers up until the failed instruction.  
This makes it easy to debug and manually test instructions that fail.  
* Docker caches layers from successfully built instruction so they dont need to be rebuilt.  Builds can bypass build cache if necessary.

### Hello Dockerfile
An example Dockerfile that builds a container running ubuntu and nginx:
```
# Version: 0.0.1
FROM ubuntu:14.04
MAINTAINER Joe Developer "joe_developer@example.com"
RUN apt-get update
RUN apt-get install -y nginx
RUN echo 'Hello Docker Container' > /usr/share/nginx/html/index.html
EXPOSE 80
```

### Building a Dockerfile
Executes the build file in the current directory and tags the new image.  It is a best practice to always use the tag command.
```bash

# building a Dockerfile in the current working directory
$> docker build -t="user1/example_server:v1" .

# building a Dockerfile from a git repository (assuming a dockerfile is present in the root directory)
$> docker build -t="user1/example_server:v1" git@github:user1/example_server

# building a Dockerfile, bypassing build cache
$> docker build --no-cache -t="user1/example_server:v1" .

```

### Displaying Docker Image History
Displays a list of instructions that have been applied to the docker image
```bash
$> docker history example_server
```

### Dockerfile Instruction Reference
[Dockerfile Reference](http://docs.docker.com/reference/builder/)

RUN - executes the command inside a shell using the command wrapper /bin/sh -c 
```                                                                                                                                                             do
# RUN instruction using standard syntax
RUN apt-get update

# RUN instruction using array syntax
RUN ["apt-get", "install", "-y", "nginx"]
```

EXPOSE - exposes 1 or more ports that are used by the containerized application.  
The run command must bind the host port to the container port (ie. -p 80:80) because it does not happen by default.  
```
EXPOSE 80

```

CMD - specifies the command to run when a container is launched.  Only one command can be run per container.
NOTE: The CMD will be overridden by a command specified on the command line.  
```
# runs the bash shell at startup
CMD /bin/bash

# runs a startup script using array syntax
CMD ["/usr/bin/startapp", "--debug"]
```

ENTRYPOINT - specifies a command (non-overridable) that can be passed arguments from the command line.
```
# runs a startup command using array syntax - cannot be overridden
ENTRYPOINT ["/usr/sbin/nginx"]

# runs the ENTRYPOINT with a default flag - flags can be overridden by flags specified at the command line
CMD ["-h"]
```

WORKDIR - specifies the current working directory in the container for RUN, CMD, ENTRYPOINT commands to execute within.
```
# change directory
WORKDIR /opt/webapp

# run npm install command
RUN npm install

# execute the node command 
ENTRYPOINT ["/usr/bin/node", "server.js"]

```

ENV - sets environment variables during the image build process.  The environment variable will be used for all subsequent RUN commands.
```
# basic environment variable
ENV RVM_PATH /home/rvm/

# referencing an environment variable from another instruction
ENV BUILD_DIR /tmp/build
WORKDIR $BUILD_DIR

```

Passing environment variables from the command line:
```bash
$> docker run -it -e "WEB_PORT=8080" ubuntu env HOME=/ HOSTNAME=example.com 
```

USER - specifies a user that the image should run as
```
USER nginx
```

VOLUME - mounts a data volume at one or more specified paths
```
VOLUME ["/data", "/opt/project"]
```

ADD - adds files and directories from the build environment to the image.  Directories that don't exist
will be created with a mode of 0755 and a UID and GID of 0.
```
# add a single file
ADD software.lic /opt/application/software.lic

# add a file from a url
ADD http://wordpress.org/latest.zip /root/wordpress.zip

# automatic unpacking for valid archive types (gzip, bzip2, xz)
ADD latest.tar.gz /var/www/wordpress/

```

COPY - copies files from the build context to the image, does not have any extraction or decompression capabilities.
```
COPY conf.d/ /etc/apache2/
```

ONBUILD - adds triggers to images.  A trigger is executed when the image is used as the basis of another image.
```
ONBUILD ADD . /app/src
ONBUILD RUN cd /app/src && make
```


## Tips and Tricks
```
# Version: 0.0.1
FROM ubuntu:14.04
MAINTAINER Joe Developer "joe_developer@example.com"

# change the environment variable to trigger a rebuild of subsequent layers (by bypassing build cache)
ENV REFRESHED_AT 2014-07-01
RUN apt-get update
RUN apt-get install -y nginx
RUN echo 'Hello Docker Container' > /usr/share/nginx/html/index.html
EXPOSE 80
```

