# Docker_Creating_Using_Containers

## Check Our Docker Install and Config
```
Koitaro@MacBook-Pro-3 ~ % docker version

Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
  
Koitaro@MacBook-Pro-3 ~ % docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
```
## Starting a Nginx Web Server
## Nginx is an open source web server. In the backgroud, docker engine looks for an image called Nginx and pulls down the latest image for Nginx from Dockerhub.
## detach tells docker run in the backgroud. We get a unique container ID. Go to local host to check if the container and server are communication
![nginx_running](https://github.com/NoriKaneshige/Docker_Creating_Using_Containers/blob/master/nginx_running.png)
## list containers running (ls), stop containers, list all containers (ls -a)
```
Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
3054ecd6b5be        nginx               "nginx -g 'daemon of…"   2 minutes ago       Up 2 minutes        0.0.0.0:80->80/tcp   mystifying_matsumoto

Koitaro@MacBook-Pro-3 ~ % docker container stop 305
305

Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

Koitaro@MacBook-Pro-3 ~ % docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
3054ecd6b5be        nginx               "nginx -g 'daemon of…"   4 minutes ago       Exited (0) 27 seconds ago                       mystifying_matsumoto
```
## when we run each time, it starts a new container from Nginx image
## names will be automatically generated unless we name



## How to see logs
```
Koitaro@MacBook-Pro-3 ~ % docker container run --publish 80:80 --detach --name webhost nginx
9d92fcdb56b37c5571a4e517e8152696bbff4b046d94430500e52bb319615a64

Koitaro@MacBook-Pro-3 ~ % docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                NAMES
9d92fcdb56b3        nginx               "nginx -g 'daemon of…"   19 seconds ago      Up 18 seconds              0.0.0.0:80->80/tcp   webhost
3054ecd6b5be        nginx               "nginx -g 'daemon of…"   7 minutes ago       Exited (0) 3 minutes ago                        mystifying_matsumoto

Koitaro@MacBook-Pro-3 ~ % docker container logs webhost
172.17.0.1 - - [14/May/2020:21:34:05 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36" "-"
172.17.0.1 - - [14/May/2020:21:34:05 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36" "-"
172.17.0.1 - - [14/May/2020:21:34:06 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36" "-"

Koitaro@MacBook-Pro-3 ~ % docker container top webhost
PID                 USER                TIME                COMMAND
2895                root                0:00                nginx: master process nginx -g daemon off;
2928                101                 0:00                nginx: worker process
```

## How to remove containers (rm -f forces a running container to be stopeed and removed)
```
Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
9d92fcdb56b3        nginx               "nginx -g 'daemon of…"   3 minutes ago       Up 3 minutes        0.0.0.0:80->80/tcp   webhost

Koitaro@MacBook-Pro-3 ~ % docker container rm 9d9
Error response from daemon: You cannot remove a running container 9d92fcdb56b37c5571a4e517e8152696bbff4b046d94430500e52bb319615a64. Stop the container before attempting removal or force remove

Koitaro@MacBook-Pro-3 ~ % docker container rm -f 9d9
9d9

Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## what happends in 'docker container run'
1. Looks for that image locally in image cache, doesn't find anything
2. Then looks in remote image repository (defaults to Docker Hub)
3. Downloads the latest version (nginx: latest by default)
4. Creates new container based on that image and prepares to start
5. Gives it a virtual IP on a private network inside docker engine
6. Opens up port 80 on host and forwards to port 80 in container
7. Starts container by using the CMD in the image Dockerfile
