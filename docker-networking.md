Docker Networking
=================

## Docker Networking Basics
* Every docker container is assigned an IP address
* Docker has it's own network interface called docker0
* Firewall rules and NAT configuration allow Docker to route between containers and the host network
* Docker containers are dynamically assigned IP addresses (they change between start/stop/restarts).  Therefore, 
hardcoding IP configuration in applications is a bad idea.
* [Advanced Docker Networking](https://docs.docker.com/articles/networking/)


## Docker Linking
* The --link flag creates a parent-child link between two containers.  (ie. --link <container_name>:<alias>)  The
alias allows us to consistently access the exposed information without needing to be concerned about the container name.
* The link gives the parent container the ability to communicate with the child container and shares some connection details with it.
* The port does not need to be exposed on the child container (ie. -p flag).  By linking the containers together, the parent container
can communicate with any open ports on the child container.
* There are also security benefits as the containers ports are not directly exposed to the local host.
* Docker populates information about container links in two places: /etc/hosts file and environment variables


## Tips and Tricks
* Use the -h or --hostname flag with the docker run command to set a specific hostname for a container
* Use the --add-host flag with the docker run command to add additional entries to the /etc/hosts
* Use the env command to print the environment variables - some will appear for the linked container.
* Configure the DNS of your individual containers using the --dns and --dns-search flag on the docker run command.
