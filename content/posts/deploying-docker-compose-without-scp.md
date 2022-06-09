+++
title = "Deploying docker-compose with docker remote host"
date = "2021-03-28T16:31:03+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["docker", "devops"]
keywords = ["docker", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
summary = "Ever wondered how you can deploy your locally running docker-compose project to a remote server? Don't want to use a registry or scp?"
+++
Ever wondered how you can deploy your locally running docker-compose project to a remote server?
There are several options:
1. Pushing the images to a container registry like dockerhub, github container registry and pulling them on your server
2. Saving the images to a .tar archive and copying it over to your server and loading them there [explained here](https://medium.com/@sanketmeghani/docker-transferring-docker-images-without-registry-2ed50726495f)

There is a easier way using [docker remote host](https://www.docker.com/blog/how-to-deploy-on-remote-docker-hosts-with-docker-compose/):

The only thing you have to make sure is to install docker, docker-compose onto your server and have a valid ssh key


### docker-compose.yml

```yaml
version: '3.9'
services:
  backend:
    build: spring-backend
    container_name: spring-backend
    image: spring-backend:0.0.1
    expose:
      - "8088"
  frontend:
    build: angular-frontend
    image: angular-frontend:0.0.1
    container_name: angular-frontend
    ports:
      - 80:80
    depends_on:
      - backend
    command: [nginx-debug, '-g', 'daemon off;']

```

### deploy.sh
```shell
#!/bin/bash

docker-compose build

for img in $(docker-compose config | awk '{if ($1 == "image:") print $2;}'); do
  images="$images $img"
done

echo $images


docker image save $images | docker -H "ssh://user@serverIp" image load
docker-compose -H "ssh://user@serverIp" up --force-recreate -d
docker-compose -H "ssh://user@serverIp" logs -f
read -p "Press any key to continue... " -n1 -s

```


With a simple deploy.sh you can build your current state, load them onto your server, run them and attach to the output.
