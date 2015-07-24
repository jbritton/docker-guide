Docker Orchestration
====================

## Docker Compose

### Installing Docker Compose
See the [Docker Compose Installation](https://docs.docker.com/compose/install/)

### Configuring Docker Compose
Specify docker-compose configuration in a docker-compose.yml file.  These options are like the options specified in a docker run command.
A full reference can be found here: [Docker Compose YAML Reference](https://docs.docker.com/compose/yml/)
```yaml

web:
  image: user1/webapp
  command: python app.py
  ports:
   - "5000:5000"
  volumes:
   - .:/webapp
  links:
   - redis
redis:
  image: redis

```

### Running Docker Compose

Run docker compose interactively:
```bash
$> cd example-webapp
$> docker-compose up
```

Run docker compose as a daemon:
```bash
$> docker-compose up -d
```

### Docker Compose Commands
See the [Docker Compose CLI Reference](https://docs.docker.com/compose/cli/)



## Resources
* [Docker-Compose](https://docs.docker.com/compose/) - simple container orchestration
* [Docker Compose YAML Reference](https://docs.docker.com/compose/yml/)
* [Consul](https://www.consul.io/) - distributed service discovery
* [Swarm](http://docs.docker.com/swarm/) - orchestration and clustering
 
 
