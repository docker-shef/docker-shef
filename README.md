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
