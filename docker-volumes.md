Docker Volumes
==============

## Volume Basics
* A volume is a specially designed directory within one or more containers that bypasses the Union File System
to provide several useful feature for persistent or shared data.
* Volumes can be shared and reused between containers
* A container doesn't have to be running to share its volumes
* Changes to a volume are made directly.
* Changes to a volume will not be included when you update an image
* Volumes persist until no containers use them
* Volumes live in the /var/lib/docker/volumes directory
* Warning: deleting the last container that uses a volume will also delete that volume

## Defining a Volume in an Image
Mounts a data volume at one or more specified paths
```
VOLUME ["/data", "/opt/project"]
```

## Using Another Container's Volume
Use the --volumes-from flag to connect to a container's volumes.  The container has to exist but does not have to be running
for it's volume to be used.
```bash
$> docker run -d --volumes-from example-data
```


## How to Backup a Volume
Uses the -v to mount our current directory, using the $(pwd) command at /backup, then the container runs the command.  
The --rm flag, which is useful for single-use or throwaway containers, removes the container after the process exits.
```bash
$> docker run --rm --volumes-from jekyll-blog \
   > -v $(pwd):/backup ubuntu \
   > tar cvf /backup/blog-backup.tar /var/www/html
```

