# Sample of production/development environments for EspoCRM

This repository contains examples of production and development environments for EspoCRM.

## How to start

1. Download the needed environment directory, ex. `php8.0-nginx-mysql`.
2. Inside the downloaded directory run the command:

```
docker-compose up -d
```

3. Wait some time for deploying the containers.
4. Download and extract EspoCRM files to the `html` directory.
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

## Database

### MySQL

Default connection information:

- Host Name: `espocrm-mysql`
- Database Name: `ANY`
- User: `root`
- Password: `1`

### MariaDB

Default connection information:

- Host Name: `espocrm-mariadb`
- Database Name: `ANY`
- User: `root`
- Password: `1`

### PhpMyAdmin

#### 1. Open a port for the container

Edit the `docker-compose.yml`:

```
espocrm-mysql:
    .....
    ports:
      - 8033:3306
```

#### 2. Modify phpMyAdmin configuration

Edit the file `config.inc.php` under the phpMyAdmin directory:

```
$i++;
$cfg['Servers'][$i]['verbose'] = 'Docker: espocrm-mysql';
$cfg['Servers'][$i]['host'] = '127.0.0.1';
$cfg['Servers'][$i]['port'] = 8033;
$cfg['Servers'][$i]['socket'] = '';
$cfg['Servers'][$i]['auth_type'] = 'cookie';
$cfg['Servers'][$i]['user'] = 'root';
$cfg['Servers'][$i]['password'] = '';
```

## Crontab

### Method 1: setup from the host

```
* * * * * /usr/bin/docker exec --user www-data -i espocrm-php /bin/bash -c "cd /var/www/html; php cron.php" > /dev/null 2>&1
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
