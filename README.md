Docker RosarioSIS
=================

# Este es un fork del repositorio original, puedes alternativamente observar la guia oficial del repositorio original

## Instalacion

Requisitos: Instancia de AWS (Familia Debian, como Ubuntu o la misma Debian) Git y algun editor de texto como VIM o NANO

## Uso

RosarioSIS usa PostgreSQL como Base de datos

El repositorio cuenta con un docker-compose.yml

```bash
docker-compose up -d
```
Una vez levantado el archivo compose, ambos contenedores estaran arriba para su uso, si te aparece un problema con sql, es porque se debe crear la base de datos

Para ello debes ingresar en el navegador:  [IP_PUBLICA_DE_LA_MAQUINA]/InstallDatabase.php

En el sitio, se te creara la base de datos para ingresar al sitio

# Credenciales

* Administrador =     Usuario: admin   Pass: admin
* Profesor =  Usuario: teacherf Pass: teacher
* Estudiante = Usuario: student Pass: student

# Con lo anterior ya tendras el sistema montado (Recordar tener el puerto 80 tcp abierto)

# Si necesitas hacerlo sin compose sigue los pasos que vienen a continuacion

```bash
git clone https://gitlab.com/francoisjacquet/docker-rosariosis.git
cd docker-rosariosis
docker build -t rosariosis .
```

# Ejecutar la base de datos

```bash
docker run --name rosariosisdb -e "POSTGRES_PASSWORD=postgrespwd" -d postgres
```

# Crear BD:
```bash
docker exec -it rosariosisdb bash
psql -h localhost -p 5432 -U postgres
postgres=# CREATE USER rosario WITH PASSWORD 'rosariopwd';
postgres=# CREATE DATABASE rosariosis WITH ENCODING 'UTF8' OWNER rosario;
postgres=# \q
exit
```

# Ejecutar RosarioSIS (DockerHub image) y conectarlo al contenedor de POSTGRESQL:
```bash
docker run -e "ROSARIOSIS_ADMIN_EMAIL=admin@example.com" -e "PGHOST=rosariosisdb" -h `hostname -f` -d -p 80:80 --name rosariosis --link rosariosisdb:rosariosisdb rosariosis/rosariosis:master
```

## Variables de Entorno para modificar la APP

### PGHOST

Host of the postgres database.

### PGUSER

This optional environment variable is used in conjunction with PGPASSWORD to set a user and its password for the database.

### PGPASSWORD

This optional environment variable is used in conjunction with PGUSER to set a user and its password for the database.

### PGDATABASE

This optional environment variable can be used to define a different name for the database.

### PGPORT

This optional environment variable can be used to define a different port for the database.

### ROSARIOSIS_YEAR

This optional environment variable can be used to define the default school year in RosarioSIS settings.

### ROSARIOSIS_ADMIN_EMAIL

This optional environment variable can be used to define an email address where to send error and new administrator notifications.

### ROSARIOSIS_LANG

This optional environment variable is for RosarioSIS to show another language.

Values are `fr_FR` for French and `es_ES` for Spanish.

You must also generate the `fr_FR.utf8` (for example) locale. To do so run these commands:
```bash
sudo docker exec -it rosariosis bash
dpkg-reconfigure locales
```

### ROSARIOSIS_VERSION

This optional environment variable is used to set the required version of RosarioSIS.

## SMTP

RosarioSIS will attempt to send mail via the host's port 25. In order for this to work you must set the hostname of the rosariosis container to that of `host` (or some other hostname that can appear on a legal `FROM` line) and configure the host to accept SMTP from the container. For postfix this means adding the container IP addresses to `/etc/postfix/main.cf` as in:

```
mynetworks = 192.168.0.0/16 172.16.0.0/12 10.0.0.0/8 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
```

Note: alternatively, you can use the [Email SMTP](https://www.rosariosis.org/plugins/email-smtp/) plugin for RosarioSIS.


## Configuracion adicional oficial

[Quick Setup Guide](https://www.rosariosis.org/quick-setup-guide/)

[Secure RosarioSIS](https://gitlab.com/francoisjacquet/rosariosis/-/wikis/Secure-RosarioSIS)
