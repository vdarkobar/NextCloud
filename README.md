# NextCloud
## deploy NextCloud behind Traefik

##### Create docker network
```
sudo docker network create proxy
```

### Clone this git repository:
```
git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/NextCloud.git
```

##### Change domain name
```
sudo nano NextCloud/docker-compose.yml
```
##### Start
```
sudo docker-compose -f NextCloud/docker-compose.yml up -d
```
##### Log
```
sudo docker logs -tf --tail="50" NextCloud
```
