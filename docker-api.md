Docker APIs
===========

## Remote API
* Remote API is provided by the Docker daemon.
* Docker daemon binds to a socket, unix:///var/run/docker.sock, on the host on which it is running
* Docker daemon runs with root priviledges
* Remote access to the API requires that the docker daemon be bound to a network interface using the -H flag on the daemon command
* Remote API is exposed on the port 2375 by default
* There are many client libraries available in Ruby, Python, Node.js, Go, Java, etc.


## Consuming the Remote API
Virtually all commands that can be performed with the Docker CLI can be performed via the Docker Remote REST API.  Here are some examples:
```bash

# show docker daemon info
$> curl 192.168.59.103:2375/info

# list images
$> curl 192.168.59.103:2375/images/json

# list running containers
$> curl -s 192.168.59.103:2375/containers/json

# list all containers
$> curl -s 192.168.59.103:2375/containers/json?all=1

# create a container
$> curl -X POST -H "Content-Type: application/json" \
    > 192.168.59.103:2375/containers/create \
    > -d '{ "Image": "user1/web-server" }'

```

## Enabling the Remote API for boot2docker
Backs up default configuration, disables TLS and restarts the docker deamon on the boot2docker VM
```bash
$> boot2docker ssh
$> cp /etc/init.d/docker ~/docker.bak
$> sudo sed -i 's/DOCKER_TLS:=auto/DOCKER_TLS:=no/1' /etc/init.d/docker
$> sudo /etc/init.d/docker stop
$> sudo /etc/init.d/docker start
```

## Securing the Remote API
* Docker's Remote API is exposed to everyone and is considered a security risk.  It should be secured!
* Authentication uses TLS/SSL certificates to secure your connection to the API
* Check the [Docker Remote API Docs](https://docs.docker.com/reference/api/docker_remote_api/) for more information

