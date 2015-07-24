Docker Registries
=================

## Docker Registry Basics
* Images live in repositories (like github repositories) within registries
* Repositories can contain multiple images (ie. ubuntu - 12.04, 12.10, 13.04, etc.)
* Default public registry:  https://registry.hub.docker.com
* More Info https://docs.docker.com/registry/deploying/


## Running a Private Registry

Run the open-source docker registry image.
```bash
docker run -p 5000:5000 registry:2.0
```

## Pushing Images to a Private Registry

Tag the image using the repository hostname and port as a prefix
```bash
docker tag 223df09r84 registry.example.com:5000/user1/example_server
```

Push the image using the repository hostname and port as a prefix
```bash
docker push registry.example.com:5000/user1/example_server
```


## Running Images from a Private Registry
Run the image using the fully qualified name (<host>:<port>/<user>/<image>)
```bash
docker run -it --name example_server registry.example.com:5000/user1/example_server /bin/bash
```
