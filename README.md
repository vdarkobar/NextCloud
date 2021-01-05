# NextCloud
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#proxmox">Home</a>
</p>  
  
### Create docker networks
```
sudo docker network create nc
sudo docker network create db
```
### Clone this git repository
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/NextCloud.git .
```
  
#### *Decide what you will use for*:
```
Time Zone,
Domain name,
Local IP Address,
NextCloud Admin username,
Collabora username,
```
  
### Select and run all at once. Enter required data:
*Only works once, use bash*
```
RED='\033[0;31m'
echo -ne "${RED}Enter Time Zone: "; read TZONE; \
echo -ne "${RED}Enter Domain name: "; read DNAME; \
echo -ne "${RED}Enter Local IP Address: "; read LIP; \
echo -ne "${RED}Enter NextCloud Admin username: "; read NCUNAME; \
echo -ne "${RED}Enter Collabora username: "; read CUNAME; \
sed -i "s|01|${TZONE}|" .env && \
sed -i "s|02|${DNAME}|" .env && \
sed -i "s|03|${LIP}|" .env && \
echo ${NCUNAME} > secrets/nc_admin_user.secret && \
sed -i "s|04|${CUNAME}|" .env && \
echo | openssl rand -base64 20 > secrets/nc_admin_password.secret && \
TOKEN=$(openssl rand -base64 20); sed -i "s|CHANGE_PASS|${TOKEN}|" .env && \
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret && \
echo | openssl rand -base64 20 > secrets/nc_mysql_password.secret && \
sudo chown -R root:root secrets/ && \
sudo chmod -R 600 secrets/
```
### Adjust if necessary, *if multiple instances are planed.*
```
sudo nano docker-compose.yml
```
  
### Dynamic config *(Traefik VM)*
```
http:

  # All routers:
  routers:

    # NextCloud service router
    nextcloud-router:
      service: nextcloud-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`cloud.example.com`)"  # adjust domain name

    # Collabora service router
    collabora-router:
      service: collabora-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`cloud.example.com`)" # adjust domain name


  # All services:
  services:

    # Nextcloud service
    nextcloud-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:8585" # adjust ip and port nummber

    # Collabora service
    collabora-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:9980" # adjust ip and port nummber
          
```
  
### Start
```
sudo docker-compose up -d
```
### Log
```
sudo docker-compose logs nextcloud-db
sudo docker-compose logs nextcloud
sudo docker logs -tf --tail="50" nextcloud-db
sudo docker logs -tf --tail="50" nextcloud
```
### NextCloud - slow login, edit: *'overwrite.cli.url' => ...*
```
sudo nano /home/<USER>/NextCloud/files/config/config.php
# change to:
'overwrite.cli.url' => 'https://cloud.example.com', 'overwritehost' => 'cloud.example.com', 'overwriteprotocol' => 'https',
```
