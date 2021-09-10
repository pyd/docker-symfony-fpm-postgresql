## Docker setup for symfony **development** in **Linux** with php-fpm, nginx and postgresql.
<br>

## Prerequisites
Configure and enable docker user namespace so root files in containers can be edited by current user in host and vice versa.<br>
See https://docs.docker.com/engine/security/userns-remap/<br>
Create the /etc/docker/daemon.json file to enable namespace remapping for the current user<br>
```yaml
{
  "userns-remap": "my-username"
}
```
In the /etc/subuid file create a mapping between the unprivileged user in host and root user in containers:
```yaml
  my-username:my-UID:1
```
In the /etc/subgid file create a mapping between the unprivileged group in host and root group in containers:
```yaml
  my-username:my-GID:1
```
<br>

## Install
### 1. clone the repo
    $ cd /path/to/project
    $ git clone --depth 1 https://github.com/pyd/docker-symfony-fpm-postgresql.git .
<br>

### 2. set GIT_USER_NAME and GIT_USER_EMAIL in the /path/to/project/docker/.env file
<br>

### 3. run the containers
    $ cd /path/to/project/docker
    $ docker-compose up -d

### 4. set permissions on /path/to/project/pgadmin-data directory (see pgadmin app section bellow)
    $ sudo chmod -R 777 /path/to/project/pgadmin-data
<br>

### 5. open a terminal inside the php container (connected as root in /var/www/html)
    $ docker-compose exec php sh

### 6. create the symfony project
    # composer create-project symfony/skeleton .
    # git init
    # git add .
    # git commit -m "create symfony project"
<br>

## symfony app URL: http://localhost:8080
<br>

## pgadmin app URL: http://localhost:8181
<br>

## permissions on /path/to/project/pgadmin-data
According to https://www.pgadmin.org/docs/pgadmin4/latest/container_deployment.html, this directory should be owned by 5050:5050
but this does not prevent an access denied on sessions/ subdirectory when trying to log in. Chmod 777 solved this.<br>
<br>

## DSN
Use variables defined in the /path/to/project/docker/.env file.<br>
By default "host=db dbname=postgres user=postgres password=superuser_password" as DSN.<br>
Note host=db. Internally, docker will replace 'db' with the IP of the php container.<br>
Same DSN can be used in the symfony app and in pgadmin.<br>
<br>

## Test db connection
Comment code in /path/to/project/app/public/index.php and paste the code bellow.
```php
if (!$connection = pg_connect ("host=db dbname=postgres user=postgres password=superuser_password")) {
  $error = error_get_last();
  echo "DB connection failed: ". $error['message']. "\n";
} else {
  echo "DB connection succeeded.\n";
}
```

## Force docker to rebuild containers after modifications to /path/to/project/docker/.env file
Remove pyd_symfony_* images
```bat
$ docker-compose down --rmi 'local'
```
or force docker to rebuild
```bat
$ docker-compose up -d --build
```


