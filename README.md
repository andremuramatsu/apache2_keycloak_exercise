# Apache mod_auth_openidc Exercise
Apache + Keycloak integration exercise

1. Docker as container technology
1. Apache for webserver + mod_auth_openidc (https://github.com/zmartzone/mod_auth_openidc)
1. MySQL as DBMS
1. PHP 8.1.x 
1. Keycloak 19.x as IAM solution

# Configuration

## Database configuration

1. Create an user for the IAM solution.
```
    mysql> CREATE DATABASE keycloak;
    Query OK, 1 row affected (0.00 sec)
    
    mysql> CREATE USER IF NOT EXISTS iam@'172.28.0.%' IDENTIFIED BY 'ASDQWE123';
    Query OK, 0 rows affected (0.00 sec)

    mysql> GRANT ALL PRIVILEGES ON keycloak.* TO iam@'172.28.0.%';
    Query OK, 0 rows affected (0.00 sec)

    mysql> FLUSH PRIVILEGES;
    Query OK, 0 rows affected (0.00 sec)
```

## Apache self-signed certificate

For development purpose, this project will use self-signed CA and Certificates.

```
$ openssl genrsa -out ./ssl/herlios-ca.key 2048 
$ openssl req -new -x509 -nodes -days 365 -key ./ssl/herlios-ca.key -out ./ssl/herlios-ca.crt \
-subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=HERLIOS/CN=herlios.local"

$ openssl req -newkey rsa:2048 -nodes -keyout ./ssl/herlios-app.key -out ./ssl/herlios-app.csr \
-subj "/C=BR/ST=SAO PAULO/L=SAO PAULO/O=HERLIOS/CN=herlios.local"

$ openssl x509 -req -days 365 -in ./ssl/herlios-app.csr -out ./ssl/herlios-app.crt -CA ./ssl/herlios-ca.crt -CAkey ./ssl/herlios-ca.key -CAcreateserial

$ sudo cp ./ssl/herlios-ca.crt \
  ./ssl/herlios-app.crt \
  /usr/local/share/ca-certificates

$ sudo update-ca-certificates
```

## Apache + mod_auth_openidc configuration

1. Create a separated realm in Keycloak
  1. Into this new Realm, create an application using OpenID authentication method
  1. Create a new user

Take a look at `application/apache/*.conf` files to match the Keycloak informations needed to configure the Apache module.