# mondedie/flarum

![logo](https://i.imgur.com/Bjrtbsc.png "logo")

### What is this ?

Flarum is the next-generation forum software that makes online discussion fun. It's simple, fast, and free. http://flarum.org/

### Features

- Lightweight & secure image
- Based on Alpine Linux with **nginx** and **PHP 7**
- Latest Flarum Beta (v0.1.0-beta.6)
- MySQL/Mariadb driver
- OPCache extension configured

### Build-time variables

- **VERSION** = Version of flarum (default: *v0.1.0-beta.6*)

### Ports

- **8888**

### Volume

- **/flarum/app/assets** : Flarum assets directory

### Environment variables

| Variable | Description | Type | Default value |
| -------- | ----------- | ---- | ------------- |
| **GID** | Flarum user id | *optional* | 991
| **UID** | Flarum group id | *optional* | 991
| **DEBUG** | Flarum debug mode | *optional* | false
| **FORUM_URL** | Forum URL | **required** | none
| **DB_HOST** | MariaDB instance ip/hostname | *optional* | mariadb
| **DB_USER** | MariaDB database username | *optional* | flarum
| **DB_NAME** | MariaDB database name | *optional* | flarum
| **DB_PASS** | MariaDB database password | **required** | none
| **DB_PREF** | Flarum tables prefix | *optional* | none

## Installation

#### 1 - Pull flarum image

```
# Pull from hub.docker.com :
docker pull mondedie/flarum

# or build it manually :
docker build -t mondedie/flarum https://github.com/mondediefr/flarum.git#master
```

#### 2 - Docker-compose.yml

Adapt to your needs :

```
flarum:
  image: mondedie/flarum
  container_name: flarum
  links:
    - mariadb:mariadb
  environment:
    - FORUM_URL=https://forum.domain.tld
    - DB_PASS=xxxxxxxx
  volumes:
    - /mnt/docker/flarum:/flarum/app/assets

mariadb:
  image: mariadb:10.1
  container_name: mariadb
  volumes:
    - /mnt/docker/mysql/db:/var/lib/mysql
  environment:
    - MYSQL_ROOT_PASSWORD=xxxxxxxx
    - MYSQL_DATABASE=flarum
    - MYSQL_USER=flarum
    - MYSQL_PASSWORD=xxxxxxxx
```

#### 4 - Reverse proxy setup

See : https://github.com/mondediefr/flarum/wiki/Reverse-proxy-example

#### 5 - Done, congratulation ! :tada:

You can now run Flarum :

```
docker-compose up -d
```

### Upgrade from beta 5

:warning: Disable 3rd party extensions prior to upgrading.

```
docker pull mondedie/flarum && docker-compose up -d
```

Navigate to `yourforum.com/admin`, enter your database password and update.

![flarum-update](https://images.mondedie.fr/udl8j4Ue/PueJSigV.png)

Remove and restart your container :

```
docker-compose stop flarum
docker-compose rm flarum
docker-compose up -d
```

### Install custom extensions

**Flarum extensions list :** https://packagist.org/search/?q=flarum-ext

#### Install an extension

```
docker exec -ti flarum extension require some/extension
```

#### Remove an extension

```
docker exec -ti flarum extension remove some/extension
```

#### List all extensions

```
docker exec -ti flarum extension list
```

### Custom error pages

To use custom error pages, add your .html files in `/mnt/docker/flarum/errors` folder :

```
mkdir -p /mnt/docker/flarum/errors
touch 403.html 404.html 500.html 503.html
chown -R 991:991 /mnt/docker/flarum
```

### Custom composer.json (experienced users)

Customize your own composer.json file in `/mnt/docker/flarum/composer.custom.json`, for example add some privates repositories like this :

```json
# /mnt/docker/flarum/composer.custom.json
{
  "name": "flarum/flarum",
  "description": "Delightfully simple forum software.",
  [...]

  "repositories": [
    {
      "type": "package",
      "package": {
        "name": "xxxxx/flarum-ext-my-private-extension",
        "version": "dev-master",
        "type": "flarum-extension",
        "authors": [
          {
            "name": "Me",
            "email": "me@domain.tld"
          }
        ],
        "dist": {
          "url": "/flarum/app/assets/extensions/flarum-ext-my-private-extension",
          "type": "path",
          "reference": "master"
        },
        "require": {
          "flarum/core": "^0.1.0-beta.6"
        },
        "autoload": {
          "psr-4": {
            "xxx\\xxx\\": "src/"
          }
        },
        "extra": {
          "flarum-extension": {
            "title": "xxxxxx",
            "icon": {
              "name": "picture-o",
              "backgroundColor": "#2196f3",
              "color": "#fff"
            }
          }
        }
      }
    }
  ],
}
```

### Screenshot

#### Installation

![flarum-installation](http://i.imgur.com/e3Hscp4.png)

#### Home page

![flarum-home](http://i.imgur.com/6kH9iTV.png)
