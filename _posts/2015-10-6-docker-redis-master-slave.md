---
layout: default
---

A few days ago, my colleague asked me how to configure [redis](http://redis.io/) master-slave on docker. I tell him redis has a [replication](http://redis.io/topics/replication)
feature, to investigate the details will be better before to configure it on docker. However, after a day, my colleague asked me again. So, I write this here.

## Run redis master
We use the docker hub official redis image to build the master/standalone redis.

	sudo docker run --name="docker_redis_master" --restart=always -p 127.0.0.1:6379:6379 -d redis

## Building the redis slave image
We base on official latest redis image to build the redis salve image, so, first we need to create a new Dockerfile

	vim /home/rigofunc/doc/Dockerfile
The Dockerfile as following:

	FROM redis:latest
	
	EXPOSE 6379
	
	VOLUME ["/data"]
	
	COPY start-redis-slave.sh /start-redis-slave.sh
	
	RUN chmod +x /start-redis-slave.sh
	
	ENTRYPOINT ["/start-redis-slave.sh", "--dir", "/data"]	
The start-redis-salve.sh file as following:

	#!/bin/bash
	#
	if [ -z "$DOCKER_REDIS_MASTER_PORT_6379_ADDR" ]; then
		echo "DOCKER_REDIS_MASTER_PORT_6379_ADDR not defined. Did you run with -link?";
		exit 7;
	fi
	
	# exec allows docker-redis-slave to receive signals for clean shutdown
	#
	
	echo "DOCKER_REDIS_MASTER_PORT_6379_ADDR -> " $DOCKER_REDIS_MASTER_PORT_6379_ADDR
	echo "DOCKER_REDIS_MASTER_PORT_6379_PORT -> " $DOCKER_REDIS_MASTER_PORT_6379_PORT

	exec /usr/local/bin/redis-server --slaveof $DOCKER_REDIS_MASTER_PORT_6379_ADDR $DOCKER_REDIS_MASTER_PORT_6379_PORT
Using docker build command to build the Dockerfile

	sudo docker build -t redis-slave .

## Verify whether have the image
To verify the new image, typing *sudo docker images*, if succeed, will as following:

	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	redis-slave         latest              d85a8de17772        5 days ago          109.2 MB
	redis               latest              2f2578ff984f        3 weeks ago         109.2 MB

## Run the redis slave
Because we had have the redis slave redis image, so we can use docker --link to link the redis master

	sudo docker run --name="docker_redis_slave" --restart=always --link=docker_redis_master:docker_redis_master -p 127.0.0.1:6380:6379 -d redis-slave
	
## Resource
The Dockerfile and the shell script can be download from my [github repo](https://github.com/xyting/docker-redis-master-slave)
