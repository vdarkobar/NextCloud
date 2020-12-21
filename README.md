# NextCloud
## deploy NextCloud

##### Create docker networks (*if not already created*)
```
sudo docker network create nc
sudo docker network create db
```
### Clone this git repository
```
cd $(mktemp -d NC-XXX) && git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/NextCloud.git .
```
##### Add passwords and change premissions, *adjust folder name*
```
echo | openssl rand -base64 48 > secrets/mysql_root_password.secret
echo | openssl rand -base64 20 > secrets/nc_mysql_password.secret
echo "admin" > secrets/nc_admin_user.secret
echo | openssl rand -base64 20 > secrets/nc_admin_password.secret
sudo chown -R root:root secrets/
```
##### *Change container names, labels and volume name, inside docker-compose file, if multiple instances are planed.*
```
sudo nano docker-compose.yml
```

#### *Change temp folder name*
```
cd
mv NC-XXX/ FOLDER_NAME
```

##### Start
```
sudo docker-compose -f FOLDER_NAME/docker-compose.yml up -d
```
##### Log
```
sudo docker logs -tf --tail="50" nextcloud-db
sudo docker logs -tf --tail="50" nextcloud
```
