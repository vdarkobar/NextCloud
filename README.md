# NextCloud
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#small-home-cloud">Home</a> |
  <a href="https://github.com/vdarkobar/Bitwarden#bitwarden">Bitwarden</a> |
  <a href="https://github.com/vdarkobar/WordPress#wordpress">WordPress</a> |
  <a href="https://github.com/vdarkobar/Ghost-blog#ghost-blog">Ghost</a> |
  <a href="https://github.com/vdarkobar/Portainer">Joomla</a>  
  <br><br>
</p>  
  
Login to <a href="https://dash.cloudflare.com/">CloudFlare</a> and set Domain name, or Domain name and Subdomain for your WordPress.
```
    A | example.com | YOUR WAN IP
```
or:
```
    A | example.com | YOUR WAN IP
```
```
    CNAME | subdomain | @ (or example.com)
```
Add subdomain *code* for Collabora Office:
```
    CNAME | code | @ (or example.com)
```
---

### Create Docker networks:
```
sudo docker network create nc
sudo docker network create db
```
### Clone NextCloud Git Repository:
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/NextCloud.git .
```
  
#### *Decide what you will use for*:
```
Time Zone,
Domain name,
Subdomain (if planned),
Local IP Address,
NextCloud Admin username,
Collabora username.
```
  
### Select and run all at once. Enter required data:
*Only works once, on wrong data input delete folder and clone again*.

```
clear
RED='\033[0;31m'
echo -ne "${RED}Enter Time Zone: "; read TZONE; \
echo -ne "${RED}Enter Domain name: "; read DNAME; \
echo -ne "${RED}Enter Subdomain with . (dot) at the end, or just press Enter to default to Domain name: "; read SDNAME; \
echo -ne "${RED}Enter Server(VM) Local IP Address: "; read LIP; \
echo -ne "${RED}Enter NextCloud Admin username: "; read NCUNAME; \
echo -ne "${RED}Enter Collabora username: "; read CUNAME; \
sed -i "s|01|${TZONE}|" .env && \
sed -i "s|02|${DNAME}|" .env && \
sed -i "s|03|${SDNAME}|" .env && \
sed -i "s|04|${LIP}|" .env && \
echo ${NCUNAME} > secrets/nc_admin_user.secret && \
sed -i "s|05|${CUNAME}|" .env && \
echo | openssl rand -base64 20 > secrets/nc_admin_password.secret && \
TOKEN=$(openssl rand -base64 20); sed -i "s|CHANGE_PASS|${TOKEN}|" .env && \
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret && \
echo | openssl rand -base64 20 > secrets/nc_mysql_password.secret && \
sudo chown -R root:root secrets/ && \
rm README.md && \
sudo chmod -R 600 secrets/
```
  
### Start:
*Change Container names (docker-compose.yml), if multiple instances are planed.*
```
sudo docker-compose up -d
```
### Log:
```
sudo docker-compose logs nextcloud-db
sudo docker-compose logs nextcloud
sudo docker-compose logs code
sudo docker logs -tf --tail="50" nextcloud-db
sudo docker logs -tf --tail="50" nextcloud
sudo docker logs -tf --tail="50" code
```
  
### Dynamic config *(Traefik VM)*:
*create file: service_name.yml in Traefik: /data/configurations/ folder for routing and to get free SSL certificates.*

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
      rule: "Host(`subdomain.example.com`)" # comment out if using domain name
#      rule: "Host(`example.com`) || Host(`www.example.com`)" # comment out if using subdomain, used for non-www to www redirect

    # Collabora service router
    collabora-router:
      service: collabora-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`code.example.com`)" # adjust domain, subdomain already set in docker-compose.yml file


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
  
### Middlewares *(Traefik VM)*:
Add to: *middlewares.yml* in Traefik: */data/configurations/* for non-www to www redirect  
  
* *If using Domain name for NextCloud*
```
http:

  # All middlewares
  middlewares:
  
    # WWW-Redirect
    example-com-www-redirect: # any name
      redirectRegex:
        regex: "^https://example.com/(.*)"
        replacement: "https://www.example.com/${1}"
```  
  
### Follow <i><a href="https://github.com/vdarkobar/shared/blob/main/NextCloud.md#edit-configphp-file">this link</a></i> for additional NextCloud settings.  
  

<a href="https://github.com/vdarkobar/NextCloud#nextcloud">top of the page</a>
