# NextCloud
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud">Home</a>
</p>  
  
### Create docker networks (*if not already created*)
```
sudo docker network create nc
sudo docker network create db
```
### Clone this git repository
```
echo -n "Enter directory name: "; read NAME; mkdir -p "$NAME"; cd "$NAME" \
&& git clone https://github.com/vdarkobar/NextCloud.git .
```
### Add passwords and change premissions (bash)
```
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret
echo | openssl rand -base64 20 > secrets/nc_mysql_password.secret
echo "admin" > secrets/nc_admin_user.secret
echo | openssl rand -base64 20 > secrets/nc_admin_password.secret
TOKEN=$(openssl rand -base64 20); sed -i "s|CHANGE_PASS|${TOKEN}|" .env
sudo chown -R root:root secrets/
```
### Adjust if necessary, *if multiple instances are planed.*
```
sudo nano docker-compose.yml
```
```
sudo nano .env
```
  
### Dynamic config
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
      rule: "Host(`cloud.domain`)"  # adjust domain name

    # Collabora service router
    collabora-router:
      service: collabora-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`code.domain`)" # adjust domain name


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
