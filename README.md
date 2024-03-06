# Sample of production/development environments for EspoCRM

This repository contains examples of production and development environments for EspoCRM.

## How to start

1. Download the needed environment directory, ex. `php8.0-nginx-mysql`.
2. Inside the downloaded directory run the command:

```bash
docker-compose up -d
```

3. Wait some time for deploying the containers.
4. Download and extract EspoCRM files to the `html` directory.
5. Visit `http://localhost:8080` and install EspoCRM.

## Usage

### Start

```bash
docker-compose up -d
```

### Start with a build

```bash
docker-compose up -d --build "$@"
```

### Restart

```bash
docker-compose restart
```

### Stop & remove

```bash
docker-compose down
```

### Status

```bash
docker-compose ps
```

### See logs

```bash
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

### PostgreSQL

Default connection information:

- Host Name: `espocrm-postgres`
- Database Name: `espocrm`
- User: `espocrm`
- Password: `espo_password`

### PhpMyAdmin

#### 1. Open a port for the container

Edit the `docker-compose.yml`:

```yaml
espocrm-mysql:
    .....
    ports:
      - 8033:3306
```

#### 2. Modify phpMyAdmin configuration

Edit the file `config.inc.php` under the phpMyAdmin directory:

```php
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

```bash
* * * * * /usr/bin/docker exec --user www-data -i espocrm-php /bin/bash -c "cd /var/www/html; php cron.php" > /dev/null 2>&1
```

## WebSocket

#### 1. Add Websocket to your docker-compose file

Edit the `docker-compose.yml`:

```yaml
espocrm-websocket:
    image: espocrm/espocrm
    container_name: espocrm-websocket
    volumes:
      - ./html:/var/www/html
    restart: always
    entrypoint: php websocket.php
    ports:
      - 8081:8080
```

#### 2. Add Websocket settings to your instance

In your `data/config.php` add:

```php
'useWebSocket' => true,
'webSocketUrl' => 'ws://localhost:8081',
'webSocketZeroMQSubscriberDsn' => 'tcp://*:7777',
'webSocketZeroMQSubmissionDsn' => 'tcp://espocrm-websocket:7777',
```

#### 3. Restart your container

Inside your directory with `docker-composer.yml` file run the command:

```bash
sudo docker-compose down -v
```

Then:

```bash
sudo docker-compose up -d --build "$@"
```

## Run tests

### Unit tests

```bash
/usr/bin/docker exec --user www-data phpunit --bootstrap vendor/autoload.php tests/unit
```

### Integration tests

```bash
/usr/bin/docker exec --user www-data phpunit --bootstrap vendor/autoload.php tests/integration
```
