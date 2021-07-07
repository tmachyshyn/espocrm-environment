# Sample of production/development environments for EspoCRM

This repository contains examples of production and development environments for EspoCRM.

## How to start

1. Download the needed environment directory, ex. `php8.0-nginx-mysql`.
2. Inside the downloaded directory run the command:

```
docker-compose up -d
```

3. Wait some time for deploying the containers.
4. Download and extract EspoCRM files at `html` directory.
5. Visit `http://localhost:8080` and install EspoCRM.

## Usage

### Start

```
docker-compose up -d
```

### Start with a build

```
docker-compose up -d --build "$@"
```

### Restart

```
docker-compose restart
```

### Stop & remove

```
docker-compose down
```

### Status

```
docker-compose ps
```

### See logs

```
docker-compose logs
```

## MySQL

Default connection information:

- Host: `espocrm-mysql`
- User: `root`
- Password: `1`

## MariaDB

Default connection information:

- Host: `espocrm-mariadb`
- User: `root`
- Password: `1`

## Crontab

### Method 1: setup from the host

```
* * * * * /usr/bin/docker exec --user www-data espocrm-php php cron.php
```

## Run tests

### Unit tests

```
/usr/bin/docker exec --user www-data phpunit --bootstrap vendor/autoload.php tests/unit
```

### Integration tests

```
/usr/bin/docker exec --user www-data phpunit --bootstrap vendor/autoload.php tests/integration
```
