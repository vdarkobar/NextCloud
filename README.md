# NextCloud
## deploy NextCloud

##### Create docker networks (*if not already created*)
```
sudo docker network create nc
sudo docker network create db
```
### Clone this git repository
```
echo -n "Enter directory name: "; read NAME; mkdir -p "$NAME"; cd "$NAME" \
&& git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/NextCloud.git .
```
##### Add passwords and change premissions
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
##### Start
```
sudo docker-compose up -d
```
##### Log
```
sudo docker-compose logs nextcloud-db
sudo docker-compose logs nextcloud
sudo docker logs -tf --tail="50" nextcloud-db
sudo docker logs -tf --tail="50" nextcloud
```
