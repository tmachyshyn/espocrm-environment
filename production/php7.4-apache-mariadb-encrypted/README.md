# Encrypted database for EspoCRM

This is an example of encrypted MariDB for EspoCRM

## Generate keys

### 1. keyfile

```
(echo -n "1;" ; openssl rand -hex 32 ) | sudo tee -a  ./keyfile
```

### 2. keyfile.key

```
sudo openssl rand -hex 128 > ./keyfile.key
```

### 3. keyfile.enc

```
sudo openssl enc -aes-256-cbc -md sha1 \
   -pass file:./keyfile.key \
   -in ./keyfile \
   -out ./keyfile.enc
```

### 4. Moving the `keyfile` to a safe place.

```
sudo mkdir -p /root/espocrm/mariadb
sudo mv ./keyfile /root/espocrm/mariadb/keyfile
```

## Modify `docker-compose.yaml`

After creating the keys, you have to modify their path in `docker-compose.yaml`.

## MariaDB hostname

The `mariadb-encrypted` database host can be used in the EspoCRM installation.

