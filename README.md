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
## 'detach' tells docker run in the backgroud. We get a unique container ID. Go to local host to check if the container and server are communication
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

## How to remove containers ('rm -f' forces a running container to be stopeed and removed)
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



## Container VS. VM: It's Just a Process. Container is not virtual machine!
## 'docker ps' sees process, this shows us this is a process on the host, not virtual machine
## 'ps aux' sees all process list
```
Koitaro@MacBook-Pro-3 ~ % docker run --name mongo -d mongo
df277f731b2db36e4e7c96d6df32ebf04079958cdd370b74df9a2aceac403575

Koitaro@MacBook-Pro-3 ~ % docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS               NAMES
df277f731b2d        mongo               "docker-entrypoint.s…"   About a minute ago   Up About a minute   27017/tcp           mongo

Koitaro@MacBook-Pro-3 ~ % docker top mongo
PID                 USER                TIME                COMMAND
3003                999                 0:08                mongod --bind_ip_all

Koitaro@MacBook-Pro-3 ~ % docker stop mongo
mongo

Koitaro@MacBook-Pro-3 ~ % docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

```
## Manage Multiple Containers, MySQL, Nginx, HTTPD (apache)
![manage_multiple_containers](https://github.com/NoriKaneshige/Docker_Creating_Using_Containers/blob/master/manage_multiple_containers.png)
```
Koitaro@MacBook-Pro-3 ~ % docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes MYSQL_RANDOM_ROOT_PASSWORD
docker: invalid reference format: repository name must be lowercase.
See 'docker run --help'.

Koitaro@MacBook-Pro-3 ~ % docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
5b54d594fba7: Pull complete
07e7d6a8a868: Pull complete
abd946892310: Pull complete
dd8f4d07efa5: Pull complete
076d396a6205: Pull complete
cf6b2b93048f: Pull complete
530904b4a8b7: Pull complete
fb1e55059a95: Pull complete
4bd29a0dcde8: Pull complete
b94a001c6ec7: Pull complete
cb77cbeb422b: Pull complete
2a35cdbd42cc: Pull complete
Digest: sha256:dc255ca50a42b3589197000b1f9bab2b4e010158d1a9f56c3db6ee145506f625
Status: Downloaded newer image for mysql:latest
c8cf761aae4f1109c7580794d3048c9c22b84fc1b5504bb354e0664ea853f843

Koitaro@MacBook-Pro-3 ~ % docker container logs db
2020-05-14 23:00:36+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.20-1debian10 started.

Koitaro@MacBook-Pro-3 ~ % docker container run -d --name webserver -p 8080:80 httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
5b54d594fba7: Already exists
4b53bced9ee8: Pull complete
33abd7401e3d: Pull complete
8fb831f2b4d7: Pull complete
f26db3b1c783: Pull complete
Digest: sha256:f1d23356c95762858854958be00b66c92f7bf35a88b902575a2afd808e1ab29e
Status: Downloaded newer image for httpd:latest
db6fe75cb5fbdb211638e29c9452475e2458a026ad08bf8e861e68c30dd7f866

Koitaro@MacBook-Pro-3 ~ % docker container run -d --name proxy -p 80:80 nginx
12b3a1de970bf1c7bc902fef672f4b1bfb0dcc46abdbe89688e36da13d048681

Koitaro@MacBook-Pro-3 ~ % docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
12b3a1de970b        nginx               "nginx -g 'daemon of…"   21 seconds ago      Up 21 seconds       0.0.0.0:80->80/tcp                  proxy
db6fe75cb5fb        httpd               "httpd-foreground"       48 seconds ago      Up 47 seconds       0.0.0.0:8080->80/tcp                webserver
c8cf761aae4f        mysql               "docker-entrypoint.s…"   3 minutes ago       Up 3 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   db

Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                               NAMES
12b3a1de970b        nginx               "nginx -g 'daemon of…"   38 seconds ago       Up 38 seconds       0.0.0.0:80->80/tcp                  proxy
db6fe75cb5fb        httpd               "httpd-foreground"       About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp                webserver
c8cf761aae4f        mysql               "docker-entrypoint.s…"   3 minutes ago        Up 3 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   db

Koitaro@MacBook-Pro-3 ~ % docker container stop 12b db6 c8c
12b
db6
c8c

Koitaro@MacBook-Pro-3 ~ % docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                      PORTS               NAMES
12b3a1de970b        nginx               "nginx -g 'daemon of…"   About a minute ago   Exited (0) 18 seconds ago                       proxy
db6fe75cb5fb        httpd               "httpd-foreground"       2 minutes ago        Exited (0) 17 seconds ago                       webserver
c8cf761aae4f        mysql               "docker-entrypoint.s…"   4 minutes ago        Exited (0) 17 seconds ago                       db
3054ecd6b5be        nginx               "nginx -g 'daemon of…"   2 hours ago          Exited (0) 2 hours ago                          mystifying_matsumoto

Koitaro@MacBook-Pro-3 ~ % docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
12b3a1de970b        nginx               "nginx -g 'daemon of…"   2 minutes ago       Exited (0) 38 seconds ago                       proxy
db6fe75cb5fb        httpd               "httpd-foreground"       2 minutes ago       Exited (0) 37 seconds ago                       webserver
c8cf761aae4f        mysql               "docker-entrypoint.s…"   5 minutes ago       Exited (0) 37 seconds ago                       db
3054ecd6b5be        nginx               "nginx -g 'daemon of…"   2 hours ago         Exited (0) 2 hours ago                          mystifying_matsumoto

Koitaro@MacBook-Pro-3 ~ % docker container rm 12b db6 c8c 305
12b
db6
c8c
305

Koitaro@MacBook-Pro-3 ~ % docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

Koitaro@MacBook-Pro-3 ~ % docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              a0d4d95e478f        19 hours ago        541MB
httpd               latest              a8a9cbaadb0c        24 hours ago
```
