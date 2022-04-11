# VALLE MÁGICO - DESPLIEGUE EN EL I3LAP

## Contenido
  * [Descripción](#descripcion)
  * [Prerrequisitos](#prerrequisitos)
  * [Despliegue en Docker utilizando el docker-compose](#despliegue)

<a name="descripcion"></a>
## Descripción

Este proyecto contiene la información necesaria para desplegar Valle Mágico en el I3LAP utilizando Docker. 

### Software utilizado:

* Sistema operativo del I3LAP: Ubuntu 16.04.5 LTS (Xenial Xerus)
* Docker: Versión 20.10.7
* Docker compose: Version 1.18.0
* MySQL: Versión 14.14 Distribución 5.7.33

<a name="prerrequisitos"></a>
## Prerrequisitos

### Credenciales de acceso
Se deben solicitar las siguientes credenciales:

* Credenciales de acceso por SSH al I3LAP.
* Credenciales de acceso a MySQL.
* Contraseña de un usuario con privilegios de root.

### Conexión al I3LAP mediante SSH
Para establecer la conexión puede utilizar el software PuTTY que puede descargar dando clic [aquí.][putty]

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

### Apertura de puertos en el I3LAP
* Los puertos 8070 (Videojuego), 9092 (Sistema de información) y 9091 (Backend) deben estar abiertos en el I3LAP, para ello ejecutar los siguientes comandos en la shell del dispositivo.    

Para entrar al modo super usuario ejecutar:  

```bash
    sudo su
```

Para la apertura de puertos ejecutar:  

```bash
    sudo ufw allow 8070
    sudo ufw allow 9091
    sudo ufw allow 9092
```

### Permitir las conexiones externas a la base de datos
En el archivo  /etc/mysql/mysql.conf.d/mysqld.cnf  ubicar la línea bind-address = 127.0.0.1 y cambiarla por bind-address = 0.0.0.0  

Una vez hecho el cambio anterior debe reiniciar el servicio de mysql, para ello debe ejecutar el siguiente comando:

```bash
    sudo service mysql restart
```

### Conecta el I3LAP a Internet
Conectar un cable de red con acceso a Internet en el purto LAN que se encuentra más cercano al puerto donde se conecta el adaptador de corriente.

<a name="despliegue"></a>
## Despliegue en Docker utilizando el docker-compose

### Clonar el proyecto:

Para clonar el proyecto ejecutar los siguientes comandos:

```bash
    cd /
    mkdir repositories
    cd repositories
    git clone https://github.com/jrgranada/valle-magico-i3lap.git
    cd valle-magico-i3lap/
```

### Creación de la base de datos y del usuario mysql:

Para crear las bases de datos y un usuario para accederla, se deben ejecutar los siguientes comandos. En este punto es requerido el usuario de MySQL.  


* Acceso a mysql:

```bash
    mysql -u root -p
```

* Creación del usuario:

```bash
    CREATE USER 'valle-user'@'%' IDENTIFIED BY 'Inclusionvalle2022*';
```

* Base de datos talentumehs_valle_magico:

```bash
    CREATE DATABASE talentumehs_valle_magico;
    GRANT ALL PRIVILEGES ON talentumehs_valle_magico.* TO 'valle-user'@'%' IDENTIFIED BY 'Inclusionvalle2022*';
    FLUSH PRIVILEGES;
```

* Base de datos talentumehs_valle_open_location:

```bash
    CREATE DATABASE talentumehs_valle_open_location;
    GRANT ALL PRIVILEGES ON talentumehs_valle_open_location.* TO 'valle-user'@'%' IDENTIFIED BY 'Inclusionvalle2022*';
    FLUSH PRIVILEGES;
    Exit;
```

### Cargue de la base de datos

Para cargar la información en las bases de datos creadas en el paso anterior, se deben ejecutar los siguientes comandos:

* Base de datos talentumehs_valle_magico:

```bash
    mysql -u root -p talentumehs_valle_magico < docker/db/talentumehs_valle_magico.sql
```

* Base de datos talentumehs_valle_open_location:

```bash
    mysql -u root -p talentumehs_valle_open_location < docker/db/talentumehs_valle_open_location.sql
```

### Despliegue de la solución

Para el despliegue de la solución se deben ejecutar los siguientes comandos:

```bash
    docker kill $(docker ps -q)
    docker rm $(docker ps -a -q)
    iptables -L
    service docker restart
    docker-compose -p "inclusion_valle" up
```




