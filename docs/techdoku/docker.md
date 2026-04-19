# docker

**Snippets**

```
docker run -it ubuntu
docker run -it --rm ubuntu # löscht container sobald er beendet wird
docker run -it --rm --network Extern busybox
docker run -it --rm --net bridge-l1 -p 50111:50111 nicolaka/netshoot
docker ps -a
docker image ls
docker build . -t dockers/server # mit tag, sonst gibt´s hash
docker run -p 8080:3000 -d dockers/server
docker inspect
docker inspect 32 # alle Prozesse die mit "32" im Hash beginnen
docker cp srcfile containerid:pfad # file vom Docker-Host in Container beginnend mit "containerid" kopieren
docker attach <containerid>
docker-compose up # orchestriert mittels docker-compose.yaml aus lokalem Verzeichnis
docker-compose up -d # detached

docker-compose up -d --force-recreate --build nginx # force rebuild

docker network ls

docker exec -it influxdb /bin/bash

# veth (virtual ethernet pair) zu Container herausfinden
docker exec -it <container_name> cat /sys/class/net/eth0/iflink # Nummer (z.B. "83" herausfinden)
ip a | grep ^<nummer>
```

<dl><dt><strong>❗ IMPORTANT</strong></dt><dd>

* GUI "portainer"
</dd></dl>

## docker networking

* **None**\
nix
* **Host**\
direkter Dienst auf Host-IP, keine Isolation
* **Bridge**\
IP + MAC des Hosts, Dienste via Portforwarding (Default-Bridge ohne Namensauflösung -> eigene "custom bridge" erstellen!)
* **macvlan (bridged)**\
Im bridged Modus mit mehreren MACs auf gleichem Rechner (muss Switch + HostOS unterstützen)
* **macvlan (802.1q)**\
VLAN
ipvlan: wie macvlan, aber mit selber MAC wie Host - manche Switches bekommen Probleme mit mehreren MACs auf einem Host. Damit Traffic auf den Host gelangt, benötigt der Rest des Netzwerkes Routen!

**Snippets**

```
docker network ls

docker network create -d macvlan \
--subnet 192.168.10.0/24 \
--gateway 192.168.10.1 \
-o parent=eth0.20 \
mynetworkname

docker run -it --rm --network mynetworkname --ip 192.168.10.3 busybox
```

**Portainer-Update**

```
docker stop portainer
docker rm portainer

docker pull portainer/portainer-ce:2.21.4 # Version s. https://docs.portainer.io/start/upgrade/docker[Website]

docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ee:2.21.4 --sslcert /path/to/cert/portainer.crt --sslkey /path/to/cert/portainer.key
```

## docker-compose

**Update**

```
docker-compose pull
docker-compose up -d --remove-orphans
docker image prune
```

**Networking (Bsp. mit bridge + macvlan)**

```
  adminer:
    image: adminer
    container_name: adminer
    ports:
      - 8082:8080
    restart: unless-stopped
    networks:
      - frontend

  docker-ubuntu:
    image: ubuntu:latest
    container_name: docker-ubuntu
    restart: unless-stopped
    command: ["sleep", "infinity"]
    networks:
      extern:
        ipv4_address: 10.10.192.2

networks:
  frontend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.29.0.0/24

  backend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.29.64.0/24
```
