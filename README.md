# Docker-shef (shef)

Docker-shef is a POC system for managing docker containers across multiple hosts via REST API. It provides basic mechanisms for docker service deployment and scaling. Because this is a POC system you can only decide docker service name, docker image and replica count. Container networking, volume and port bindings aren't covered by this POC system.

Docker-shef build with nodejs v14 and uses docker API on hosts to manage containers.

This system has inspired from Kubernetes architecture and works with Master-Slave design to manage docker hosts. Docker-shef consist of 4 components Bouter, Conducktor, shefRunner and Redis.

### [Bouter](https://github.com/docker-shef/bouter)

Bouter is booter of the system(I like word games). Bouter designed for easier deployment of docker-shef master and slaves. Bouter manages lifecycle of all other system components. It boots them with necessary variables, volume and port bindings.

### [Conducktor](https://github.com/docker-shef/conducktor)

Conducktor is API server and conduc(🦆)tor of the system. It handles requests from users and checks availabilities of shefRunners. Conducktor decides where to create containers and balances them between nodes properly. Conducktor triggers shefRunners via http calls to create or delete containers on docker hosts. Default 8044 port used for REST API endpoints. You can find specific endpoint documentation at here.

### [ShefRunner](https://github.com/docker-shef/shefRunner)

ShefRunner is docker host agent of the system. It is responsible for creating and deleting containers ordered by Conducktor.

### Redis

Redis used as metadata KeyValue database of system. Detailed information of runners, services and containers stored in Redis with stats of system.


## Installation

Docker-shef supports amd64 and arm64 architectures. Tested on Docker Engine >v19, supports Docker Desktop Mac/Windows.
### Requirements
- Docker Engine >v19
- Root privileged user to start Bouter service.

### Easy Installation

To start Docker-shef master node, fill the \<MASTER HOST IPv4\> with IP of docker host. This will star bouter, conducktor, shefRunner and redis containers with proper configurations.
```
docker run -d --name bouter -e HOST_IP=<MASTER HOST IPv4> \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -l shef-bouter=true seljuke/bouter:latest
```

To add slave nodes to master set `SLAVE` environment variable to true and fill \<SLAVE HOST IPv4\> with docker host IP and \<MASTER HOST IPv4\> with master host IP. This will start bouter and shefRunner containers with proper configurations.
```
docker run -d --name bouter -e HOST_IP=<SLAVE HOST IPv4> \
  -e SLAVE=true \
  -e MASTER_HOST=<MASTER HOST IPv4> \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -l shef-bouter=true seljuke/bouter:latest
```

After containers up and running you can start interact with Conducktor REST API through `http://<MASTER HOST IPv4>:8044`.
