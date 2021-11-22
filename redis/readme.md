# Setting Redis up on top of Kubernetes. 

Foobar is a Python library for dealing with word pluralization.

## Requirement


1.	Docker installation:
```bash
sudo apt update 
sudo apt install docker.io
sudo systemctl start docker 
sudo systemctl enable docker
```

2.	Disable the firewall

$ sudo ufw disable

3.	Kubernetes installation: 


```bash
pip install foobar
```



# Redis 

## Docker 

Docker image over [here](https://hub.docker.com/_/redis)

## Running redis

```
docker network create redis
docker run -it --rm --name redis --net redis -p 6379:6379 redis:6.0-alpine
```

## Configuration

Redis configuration documentation [here](https://redis.io/topics/config)

Starting Redis with a custom config

```
cd .\storage\redis\
docker run -it --rm --name redis --net redis -v ${PWD}/config:/etc/redis/ redis:6.0-alpine redis-server /etc/redis/redis.conf

```

## Security

Redis should not be exposed to public.
Always use a strong password in `redis.conf`

```
requirepass SuperSecretSecureStrongPass
```


## Persistence

Redis Persistence Documentation [here](https://redis.io/topics/persistence)

```
docker volume create redis
cd .\storage\redis\
docker run -it --rm --name redis --net redis -v ${PWD}/config:/etc/redis/ -v redis:/data/  redis:6.0-alpine redis-server /etc/redis/redis.conf

```


## Redis client application

An example application that reads a key from Redis, increments it and writes it back to Redis.

```
cd .\storage\redis\applications\client\

# start go dev environment
docker run -it -v ${PWD}:/go/src -w /go/src --net redis -p 80:80 golang:1.14-alpine

go build client.go
# start the app
./client

# build the container
docker build . -t aimvector/redis-client:v1.0.0

```

Run our application

```
cd .\storage\redis\applications\client\
docker build . -t aimvector/redis-client:v1.0.0

docker run -it --net redis `
-e REDIS_HOST=redis `
-e REDIS_PORT=6379 `
-e REDIS_PASSWORD="SuperSecretSecureStrongPass" `
-p 80:80 `
aimvector/redis-client:v1.0.0

```

## Redis Replication and High Availability

Lets move on to the [clustering](./clustering/readme.md) secion.
