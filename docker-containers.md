Docker Containers
=================

## Container Basics
* Containers are the runtimes for images.
* Containers are stored in the /var/lib/docker/containers directory.

## Docker Container Commands

### Run Containers

Creates and runs a docker container, keeping STDIN open from the container (-i) and assigning a pseudo-tty to the container (-t).
It runs the bash shell.  This is the basic command necessary for running an interactive container.  The container will stop upon
existing the bash shell.
```bash
$> docker run -i -t ubuntu /bin/bash

```

Container naming - It is a best practice to name your containers.  If you do not name a container explicitly, a random name will be
generated for you.
```bash
$> docker run --name example_server -i -t ubuntu /bin/bash

```

Creates a daemonized container which is ideal for running applications and services.  Most of the containers you are likely to run 
will probably be daemonized.  The -d flag tells docker to detach the container to the background.  The /bin/sh -c commands tells the container
to run a command on the shell.  The container will stop once the command completes.  Since the loop doesn't complete, the container does not stop.  
```bash
$> docker run --name example_server -d ubuntu /bin/sh -c "while true; do echo running; sleep 10; done"

```

Creates a daemonized container with restart behavior.  restart=always will tell docker to restart the container no matter what exit
code is returned.  Other restart options include on-failure.
```bash
$> docker run --name example_server --restart=always -d ubuntu /bin/bash

```

Create a container with (almost) all of the same capabilities of the host machine, including kernel features and device access.  
NOTE: Running docker in --privileged mode is a security risk.  
```bash
$> docker run -p 8080:8080 --name example_server --privileged -d ubuntu

```

Capturing the container id by using the --cidfile flag stores the containers ID in the specified file.
```bash
$> docker run --cidfile=/tmp/containerid.txt ubuntu

```

### Docker Container Ports
Exposes port 80 on the container and binds it to a random port on the host.
```bash
$> docker run --name example_server -p 80 -d ubuntu /bin/bash

```

Exposes port 80 on the container and binds it to port 80 on the host (ie. -p <host_port>:<container_port>)
```bash
$> docker run --name example_server -p 80:80 -d ubuntu /bin/bash

```

### Mounting a Host Directory to a Container
A directory from the host system can be mounted to a container using the -v flag. (ie. -v <host_path>:<container_path>) 
For Mac OSX users, the host path must be located within the /Users directory.  NOTE: Volumes bypass the union filesystem mount and changes 
are not included in the image.
```bash
$> docker run --name example_server -d -v /Users/user1/data:/home/user1/data ubuntu /bin/bash

```


### Start/Stop/Restart Containers

Start a container
```bash
# start a container by name
$> docker start example_server

# start a container by hash
$> docker start aa3f365

```

Stop a container (sends a SIGTERM signal)
```bash
$> docker stop example_server

```

Restart a container
```bash
$> docker restart example_server

```

Kill a container (sends a SIGKILL signal)
```bash
$> docker kill example_server

```

### Attaching to a Container
When starting a stopped interactive (-it) container, you can attach to the terminal session with the attach command.  Upon exiting the terminal
session, the container will stop running.
```bash
$> docker attach example_server

```

### Running Processes within a Container
Processes running within a container can be in the foreground (ie. interactive shell) or in the background.  Use the -d flag to execute a background
process.  Use the -i and -t flags for interactivity like the docker run command.

```bash

# Run a non-interactive command that creates a new file (background)
$> docker exec -d example_server touch /tmp/new_file.txt

# Open an interactive shell within the running container (foreground)
$> docker exec -i -t example_server /bin/bash

```

### Deleting a Container
Deletes the specified container.  It must be stopped first.
```bash
$> docker rm example_server

```

A work around to delete all containers.
```bash

# inner command lists all containers, returning only the IDs - outer command deletes the containers
$> docker rm `docker ps -a -q`

```

### Viewing Container Logs
Tails the logs from specified container.  The logs command is similar to the linux tail command
```bash
$> docker logs -ft example_server

```

### Viewing Container Processes
Inspects the processes running inside a container
```bash
$> docker top example_server

```

### Viewing Container Ports
Inspects the container's port bindings
```bash
$> docker port example_server

```


### Inspecting Container Configuration
Inspects the container configuration
```bash
$> docker inspect example_server

```

### Monitoring Containers
Lists running containers by default.  Use the -a flag to show all containers (running and stopped).
```bash
$> docker ps -a

```

### Monitoring Container Usage Statistics
Inspects the resource usage statistics of all running docker containers
```bash
$> docker stats

```

