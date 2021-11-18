# **Manual de WordPress**
## **Vagrant**

Lo primero que debemos hacer es la siguiente orden:
```
vagrant init ubuntu/focal64
```
Esto nos generara un archivo de vagrant que nos hara falta para poder ejecutar las maquinas virtuales. En este archivo, debemos entrar con vi, e cambiar un parametro que necesitaremos más adelante. Esto es sencillo, solo hay que poner la siguiente orden, y modificar como la imagen adjunta el archivo:
```
vi Vagrantfile
```
Una vez hecho esto, debemos poner las siguientes 2 ordenes, y tras eso iniciamos la maquina.
```
vagrant up --provider=virtualbox
```
## **Dentro de la maquina**
Lo primero de todo y mas esencial, es cambiar el hostname de la maquina. Para hacerlo, lo que se debe hacer es el siguiente comando en la siguiente ruta:
```
vi /etc/hostname
```
Una vez dentro del fichero, le damos a la `I` para editar, y cambiamos el nombre. una vez cambiado haces `Ctrl+C` y para salir guardando debes poner `:wq`

Tras eso lo que debemos hacer es instalar en la maquina el software de `Apache2` y el de `Mysql`. Como añadido podemos instalar las bibliotecas extra de apache 2.

Los comandos para instalarlo son los siguientes:
```
apt update
apt upgrade
apt install -y apache2
apt install -y mysql-server
apt install -Y php libapache2-mod-php
apt install -Y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
apt install -Y unzip
apt install -Y firefox
```
Una vez todo este descargado, procedemos a reiniciar el servicio de Apache2, hacerlo es tan simple como poner la siguiente orden:
```
systemctl restart apache2
```
Ahora debemos comprobar si el servidor apache2 funciona, si es asi lo siguiente que hay que hacer pasar a la siguiente categoria:

#### **Acceso al MySQL**
```
mysql -u root
```

Una vez dentro, crearemos nuestra base de datos y usuario en MySQL de la siguiente forma:

#### **Creación de la base de datos:**
```
CREATE DATABASE [NombreBaseDatos]];
```

#### **Creación de un usuario**

```
CREATE USER '[NombreUsuario]'@'localhost' IDENTIFIED WITH mysql_native_password BY '[NombreContraseña]';
```

Tras tener creado el usuario, le tendremos que dar permisos de root, para que este pueda administrar a los demas usuarios. Esto se debe hacer con la siguiente orden:

#### **Dar privilegios al usuario:**
```
GRANT ALL ON [NombreBaseDatos]].* to '[NombreUsuario]'@'localhost';
```

#### **Salida de la base de datos**
```
exit
```

Una vez todo creado, debemos comprobar si lo hemos hecho bien, por ello haremos lo siguiente:

#### **Conectarse a la base de datos**

```
mysql -u [NombreUsuario] -p
```

Con la comprobaración hecha, solo nos quedaria reiniciar el servidor, esto se hace de la siguiente forma:

```
systemctl restart mysql
```
## **Descargar WordPress**

Cuando ya tenemos todo el mysql, lo que debemos hacer es decargar el WordPress en un directorio a nuestra elección. En nuestro caso sera en `/var/www/[NombreDominio].local`.
**IMPORTANTE:** Siempre debe acabar en `.local`

Una vez creado el directorio entramos en el mismo, y cuando ya estamos dentro debemos poner la siguiente comanda para descagar el paquete, en nuestro caso es WordPress
```
wget https://wordpress.org/latest.zip
```
Una descargado, lo descomprimimos y le cambiamos el propietario con el siguiente comando:
```
sudo chown -R $USER:www-data /var/www/[NombreDominio].local
```
Y tras eso se cambia los permisos globales del directorio
```
sudo chmod -R 775  /var/www/[NombreDominio].local
```

## **Configuración de VirtualHost**

Tras dar los permisos, lo que vamos a hacer es configurar  los VirtualHost. Esto realmente es bastante simple.

Lo que debemos hacer es ir al directorio `/etc/apache2/sites-available/` y en ese directorio, usar la siguiente comadnda:
```
vi [NombreDominio].local.conf
```
Y ya dentro de ese fichero, debes poner lo siguiente:
```
# [NombreDominio].local.conf
<VirtualHost *:80>
    ServerAdmin admin@[NombreDominio].local
    ServerName www.[NombreDominio].local
    ServerAlias [NombreDominio].local
    DocumentRoot /var/www/[NombreDominio].local
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Ahora lo que debemos hacer es habilitar este dominio con la siguiente comanda:
```
sudo a2ensite [NombreDominio].local.conf
```
**IMPORTANTE:** Para que esto funcione debes estar en el directorio `/etc/apache2/sites-available/`.

Una vez terminado debes reiniciar apache2, si no recuerdas como se hacia, es con el comando `sudo service apache2 restart`

## **Incluirnos a nuestros hosts**

Ahora añadiremos nuestro dominio en el fichero de host para añadir nuestro dominio como localhost. Para hacer eso solo hay que seguir las siguientes ordenes:

```
Vi /etc/hosts
```
y dentro de este fichero hay que añadir
```
127.0.0.1 [Tabulador] www.[NombreDominio].local
```
salimos guardando como he explicado anteriormente, reiniciamos apache y comprovamos si funciona entrando en la ip que se acaba de añadir en el fichero de hosts.

## **Comprobación**
