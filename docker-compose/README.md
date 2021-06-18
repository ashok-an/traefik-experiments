### Source: https://doc.traefik.io/traefik/getting-started/quick-start/

### Install traefik
```
❯ docker-compose up -d reverse-proxy
Docker Compose is now in the Docker CLI, try `docker compose up`

Creating network "docker-compose_default" with the default driver
Starting docker-compose_reverse-proxy_1 ...
Starting docker-compose_reverse-proxy_1 ... done
❯

❯ docker-compose ps
             Name                           Command               State                     Ports
--------------------------------------------------------------------------------------------------------------------
docker-compose_reverse-proxy_1   /entrypoint.sh --api.insec ...   Up      0.0.0.0:80->80/tcp, 0.0.0.0:8080->8080/tcp
❯ docker-compose ps
             Name                           Command               State                     Ports
--------------------------------------------------------------------------------------------------------------------
docker-compose_reverse-proxy_1   /entrypoint.sh --api.insec ...   Up      0.0.0.0:80->80/tcp, 0.0.0.0:8080->8080/tcp
```

open http://0.0.0.0:8080/dashboard/#/

---

### Install service for routing
```
❯ docker-compose up -d whoami
Docker Compose is now in the Docker CLI, try `docker compose up`

Pulling whoami (traefik/whoami:)...
latest: Pulling from traefik/whoami
f089da391c25: Pull complete
65aa504e5268: Pull complete
69d0b1dd9140: Pull complete
Digest: sha256:2c52bb2c848038a33e40415c300b655d7976bafaf033ecf4a6679cb9e1715917
Status: Downloaded newer image for traefik/whoami:latest
Creating docker-compose_whoami_1 ... done
❯ docker-compose ps
             Name                           Command               State                     Ports
--------------------------------------------------------------------------------------------------------------------
docker-compose_reverse-proxy_1   /entrypoint.sh --api.insec ...   Up      0.0.0.0:80->80/tcp, 0.0.0.0:8080->8080/tcp
docker-compose_whoami_1          /whoami                          Up      80/tcp

❯ curl -H Host:whoami.docker.localhost http://127.0.0.1
Hostname: c831c19244de
IP: 127.0.0.1
IP: 172.26.0.3
RemoteAddr: 172.26.0.2:42568
GET / HTTP/1.1
Host: whoami.docker.localhost
User-Agent: curl/7.64.1
Accept: */*
Accept-Encoding: gzip
X-Forwarded-For: 172.26.0.1
X-Forwarded-Host: whoami.docker.localhost
X-Forwarded-Port: 80
X-Forwarded-Proto: http
X-Forwarded-Server: 7a5ca25a6ed7
X-Real-Ip: 172.26.0.1
```
---

### Scale for load balancing
```
❯ docker-compose up -d --scale whoami=3
Docker Compose is now in the Docker CLI, try `docker compose up`

docker-compose_reverse-proxy_1 is up-to-date
Creating docker-compose_whoami_2 ... done
Creating docker-compose_whoami_3 ... done
❯ curl -H Host:whoami.docker.localhost http://127.0.0.1 | grep IP
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   367  100   367    0     0  33363      0 --:--:-- --:--:-- --:--:-- 33363
IP: 127.0.0.1
IP: 172.26.0.4
❯ curl -H Host:whoami.docker.localhost http://127.0.0.1 | grep IP
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   367  100   367    0     0  52428      0 --:--:-- --:--:-- --:--:-- 52428
IP: 127.0.0.1
IP: 172.26.0.3
❯ curl -H Host:whoami.docker.localhost http://127.0.0.1 | grep IP
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   367  100   367    0     0  40777      0 --:--:-- --:--:-- --:--:-- 40777
IP: 127.0.0.1
IP: 172.26.0.5
❯ curl -H Host:whoami.docker.localhost http://127.0.0.1 | grep IP
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   367  100   367    0     0  30583      0 --:--:-- --:--:-- --:--:-- 30583
IP: 127.0.0.1
IP: 172.26.0.4
```
