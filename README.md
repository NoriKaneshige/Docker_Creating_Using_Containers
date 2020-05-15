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

## Would the following two commands create a port conflict error with each other?
docker container run -p 80:80 -d nginx
<br>
docker container run -p 8080:80 -d nginx

### No. Just because the containers are both listening on port 80 inside (the right number), there is no conflict because on the host they are published on 80, and 8080 separately (the left number).

## process list in one container
docker container top
## details of one container config
## show metadata of container (startup config, volumes, networking etc) 
docker container inspect
![docker_container_stats](https://github.com/NoriKaneshige/Docker_Creating_Using_Containers/blob/master/docker_container_stats.png)
```
## show all performance stats for all containers
docker container stats
```
Koitaro@MacBook-Pro-3 ~ % docker container run -d --name nginx nginx
aeb875584ea96e595081d662bb0f84b2374124996c18f8ddd0a6ec119520d32b
Koitaro@MacBook-Pro-3 ~ % docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3
Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
b9db46c0e512        mysql               "docker-entrypoint.s…"   6 seconds ago       Up 6 seconds        3306/tcp, 33060/tcp   mysql
aeb875584ea9        nginx               "nginx -g 'daemon of…"   13 seconds ago      Up 13 seconds       80/tcp                nginx
Koitaro@MacBook-Pro-3 ~ % docker container inspect mysql
[
    {
        "Id": "b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3",
        "Created": "2020-05-15T00:17:56.2630442Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "mysqld"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4601,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-05-15T00:17:56.6204422Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:a0d4d95e478ff2962ede50c50b7dc2fc699382bcb94ad301e9c6805609f0939a",
        "ResolvConfPath": "/var/lib/docker/containers/b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3/hostname",
        "HostsPath": "/var/lib/docker/containers/b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3/hosts",
        "LogPath": "/var/lib/docker/containers/b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3/b9db46c0e5128c0bb4a2212803e08b38f4b584b0dbbf5e936f9ec8634f803de3-json.log",
        "Name": "/mysql",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/282af0585967ff7e001d147f00eb3ee6d97d873dd69033d723419e979fdbae0f-init/diff:/var/lib/docker/overlay2/0938d569e895909855bcc2b0d13ea6fc1acf4dce212e84c01a94971b0196ded5/diff:/var/lib/docker/overlay2/47c2a9099e50d637e99266d346d145e90eb0fb873ff0d6ec4575b39a06e51b43/diff:/var/lib/docker/overlay2/cdb8ca30067487b46394a3afad83e202d2a9f4f9155c68bc2fe992b321d5af7d/diff:/var/lib/docker/overlay2/12267a529eb34be624dcd94a30201779023331e579bf3daa7c4fc437fb5c01bb/diff:/var/lib/docker/overlay2/3163d82be7eb95191766c31b9255e868b22d21aae53add44bfab81a457e4af92/diff:/var/lib/docker/overlay2/ec989779521c59725288fdd70ed4d099397b84ad6d71edb747b8a8ee00aba4bd/diff:/var/lib/docker/overlay2/a9e9990af21a4aac92eac4a3f9336f1aec4344b0363273a4810a747a7d2ff5d7/diff:/var/lib/docker/overlay2/6f3b6a4a952c15f2dc5e5bb88ead48033d427a11cf21f30d297992f44eb864f6/diff:/var/lib/docker/overlay2/d3a4a04ef3b980dcc19b1c1d60c459bf884f035e1bf3a80578aec98996166fcf/diff:/var/lib/docker/overlay2/f16ea2b5ff133b058367bf557f88140beb6bedd07b6f471ecae6ce9acee758ca/diff:/var/lib/docker/overlay2/3041a3a78e0019f6b5b6d327b138bb16e293012a71e91fe191ecadc89246a02d/diff:/var/lib/docker/overlay2/5c9d6348f43b116f96351ae240ff9342815e2940336af7a2e5c2ca041b1c0e00/diff",
                "MergedDir": "/var/lib/docker/overlay2/282af0585967ff7e001d147f00eb3ee6d97d873dd69033d723419e979fdbae0f/merged",
                "UpperDir": "/var/lib/docker/overlay2/282af0585967ff7e001d147f00eb3ee6d97d873dd69033d723419e979fdbae0f/diff",
                "WorkDir": "/var/lib/docker/overlay2/282af0585967ff7e001d147f00eb3ee6d97d873dd69033d723419e979fdbae0f/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [
            {
                "Type": "volume",
                "Name": "61030e8a25717802318e9608b2a73fefb5a3828905e1068e51209ccaf332c806",
                "Source": "/var/lib/docker/volumes/61030e8a25717802318e9608b2a73fefb5a3828905e1068e51209ccaf332c806/_data",
                "Destination": "/var/lib/mysql",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        "Config": {
            "Hostname": "b9db46c0e512",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "3306/tcp": {},
                "33060/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "MYSQL_RANDOM_ROOT_PASSWORD=true",
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "MYSQL_MAJOR=8.0",
                "MYSQL_VERSION=8.0.20-1debian10"
            ],
            "Cmd": [
                "mysqld"
            ],
            "Image": "mysql",
            "Volumes": {
                "/var/lib/mysql": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "b78bbfef929bd3b5f250251fe1368c96481f3ef0748ba7ef982b78bcf5ab6601",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "3306/tcp": null,
                "33060/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/b78bbfef929b",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "a4f525c2691122aa15a49011afb4a4d5efbf3eb8a6fec79e1f835e93b83f1fea",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.3",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:03",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "a1d418d9a2a81f5a77f4cd74eb9e434aa1fb0201c07f0d3939373ece636b168e",
                    "EndpointID": "a4f525c2691122aa15a49011afb4a4d5efbf3eb8a6fec79e1f835e93b83f1fea",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                }
            }
        }
    }
]
```
# Getting a Shell Inside Containers: No Need for SSH
## start new container interactively
1. '-t' is peudo-tty, simulates a real terminal, like what SSH does
2. '-i' is interactive, Keeps session open to receive terminal input
3. this gives us an equivalent of interactive shell.
docker container run -it
> bash shell
> if run with -it, it will give you a terminal inside the running container
> 'root' is user that the container started as. This doesn't mean i'm root in the host.
> acting root on container, then followed by container ID
## run additional command in existing container
docker container exec -it
docker container run -help
```
Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
Koitaro@MacBook-Pro-3 ~ % docker container run -it --name proxy nginx bash
root@2b6b55cef748:/#
```
## to see root of file systems inside a container
## looking at all files inside of a container which is based on nginx image
```
root@2b6b55cef748:/# ls -al
total 72
drwxr-xr-x   1 root root 4096 May 15 01:22 .
drwxr-xr-x   1 root root 4096 May 15 01:22 ..
-rwxr-xr-x   1 root root    0 May 15 01:22 .dockerenv
drwxr-xr-x   2 root root 4096 Apr 22 00:00 bin
drwxr-xr-x   2 root root 4096 Feb  1 17:09 boot
drwxr-xr-x   5 root root  360 May 15 01:22 dev
drwxr-xr-x   1 root root 4096 May 15 01:22 etc
drwxr-xr-x   2 root root 4096 Feb  1 17:09 home
drwxr-xr-x   1 root root 4096 Apr 23 13:02 lib
drwxr-xr-x   2 root root 4096 Apr 22 00:00 lib64
drwxr-xr-x   2 root root 4096 Apr 22 00:00 media
drwxr-xr-x   2 root root 4096 Apr 22 00:00 mnt
drwxr-xr-x   2 root root 4096 Apr 22 00:00 opt
dr-xr-xr-x 145 root root    0 May 15 01:22 proc
drwx------   2 root root 4096 Apr 22 00:00 root
drwxr-xr-x   3 root root 4096 Apr 22 00:00 run
drwxr-xr-x   2 root root 4096 Apr 22 00:00 sbin
drwxr-xr-x   2 root root 4096 Apr 22 00:00 srv
dr-xr-xr-x  12 root root    0 May 15 01:22 sys
drwxrwxrwt   1 root root 4096 Apr 23 13:02 tmp
drwxr-xr-x   1 root root 4096 Apr 22 00:00 usr
drwxr-xr-x   1 root root 4096 Apr 22 00:00 var
root@2b6b55cef748:/#
```
## to get out of shell
## Now, the container stopped
```
root@2b6b55cef748:/# exit
exit
Koitaro@MacBook-Pro-3 ~ %
```
## Notice there is no container named proxy because it stopped
```
Koitaro@MacBook-Pro-3 ~ % docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
## Notice there is the container named proxy here
## when we typed 'bash' above, we changed the default program to bash, which gave us a shell.
## when we exited a shell, the container stopped. 
```
Koitaro@MacBook-Pro-3 ~ % docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                          PORTS               NAMES
2b6b55cef748        nginx               "bash"              7 minutes ago       Exited (0) About a minute ago                       proxy
```
## its default CMD is bash, so we don't have to spedify it
## this gonna download ubutu image, and place me a prompt of new container
docker container run -it --name ubuntu ubuntu

## we can use ubutu's apt package manage like below
apt-get update

## then we can actually install something such as below
atp-get install -y curl

## Now, we have curl in the container and we can use it
curl google.com

## By 'exit', the shell stops container 
exit

## check if we don't see the container
docker container ls

## we can see ubutu imange here
## when we start that container up again, but we create a new container from ubutu image
## this different container doesn't have curl command line installed
docker container ls -a
<br>
docker container start --help

## if we want to re-run that container, and git bush right back in it, we can use 'docker container start'
docker container start -ai ubuntu

## 'curl' can work, because we re-run the container in which curl has been installed
curl google.com

## 'exit' stops again
exit
<br>
docker container exec --help

## when we want to see the shell inside of running container that is already running like nginx
## we can use 'exec' command
## -it [name of the container] [program I want to run]
## Now, in the container with mysql
## 'exec -it' does exactly the same thing as run but it does it on an existing container that's runnig, not starting new container
docker container exec -it mysql bash

## List processes, we see mysql demon running in the background as well as bash process and ps command I just ran
ps aux

## exit
exit

## check if mysql is still running, becaue 'docker container exec' runs an additional process on an existing running container
## it's not going to affect the root process of mysql demon
docker container ls

## pull down the latest alpine image
## alpine comes with its own package managerd
docker pull alpine
<br>
docker image ls

## let's do a bash shell inside of alpine, why error? bash is not in the container?
## alpine doesn't have bash
docker container run -it alpine bash
<br>
docker container run -it alpine sh

# Docker Networks: Concepts for Private and Public Comms in Containers

## '-p' command exposes the port on your machine
## Below, exposing port 80 of nginx
## The left side is host port forwrd traffic from the port into the port 80 of the container
```
Koitaro@MacBook-Pro-3 ~ % docker container run -p 80:80 --name webhost -d nginx
79d9f7d0e3ac977c56e31542fb13f3e26c2f450e5ea2caeddba3ab3151ba6d47
```
## Docker network by default
![docker_network_by_default](https://github.com/NoriKaneshige/Docker_Creating_Using_Containers/blob/master/docker_network_by_default.png)

## 'container port' command shows what ports are open for the container on your network
when you start a container, you are connecting to a particular docker network, which by default is 'bridge' network. Each network routes out though net firewall. All containers o a virtual network can talk eath other without -p.
## docker container port [conntainer]
## Which ports are forwarding traffic to that container from the host into the container itself
```
Koitaro@MacBook-Pro-3 ~ % docker container port webhost
80/tcp -> 0.0.0.0:80
```
## check IP address of the container (this is different from IP of host by default)
```
Koitaro@MacBook-Pro-3 ~ % docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
172.17.0.2
```
## Docker container network diaglam
![docker_network_diaglam](https://github.com/NoriKaneshige/Docker_Creating_Using_Containers/blob/master/docker_network_diaglam.png)


