# Instalación local de un CMS PHP

>Investiga las funcionalidades y características de la última versión del CMS drupal. (https://www.drupal.org)

Esta tarea consiste en instalar un CMS de tecnología PHP (drupal 7 o drupal 8) en un servidor local. Los pasos que tendrás que dar los siguientes:

### **Tarea 1: Instalación de un servidor LAMP**

**1. Crea una instancia de vagrant basado en un box debian o ubuntu**

~~~
$ vagrant init
~~~

Configuración de Vagranfile:
[Máquina_lamp](https://github.com/PalomaR88/Instalaci-n-local-de-un-CMS-PHP/blob/master/Vagrantfile "Máquina Debian Buster con Vagrant")

~~~
$ vagrant up
~~~


**2. Instala en esa máquina virtual toda la pila LAMP**

Actualiza la nueva máquina. 
~~~
$ sudo apt update
$ sudo apt upgrade
~~~

Instalación de Apache2:
~~~
$ sudo apt install apache2
~~~

Instalación del servidor mariaDB:
~~~
$ sudo apt install mariadb-server
~~~

Instalación de las librerías PHP:
~~~
$ sudo apt install php7.3 php7.3-mysql libapache2-mod-php php-gd php-mysql php-xml php-mbstring
~~~



### Tarea 2: Instalación de drupal en mi servidor local

**1. Configura el servidor web con virtual hosting para que el CMS sea accesible desde la dirección: www.nombrealumno-drupal.org.**

Se añade en /etc/hosts del anfitrion la siguiente línea:
~~~
172.22.1.164    www.paloma-drupal.org
~~~


**2. Crea un usuario en la base de datos para trabajar con la base de datos donde se van a guardar los datos del CMS.**

Se configura el usuario root:
~~~
$ sudo mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
~~~

Se crea un nuevo usuario:
~~~
vagrant@servidor:/etc/apache2/sites-available$ sudo mariadb -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 59
Server version: 10.3.17-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> grant all privileges on *.* to 'paloma'@'localhost' identified by 'paloma' with grant option;
Query OK, 0 rows affected (0.001 sec)
~~~


**3. Descarga la versión que te parezca más oportuna de Drupal (7 o 8) y realiza la instalación.**



**4. Realiza una configuración mínima de la aplicación (Cambia la plantilla, crea algún contenido, …)**

**5. Instala un módulo para añadir alguna funcionalidad a drupal.**


En este momento, muestra al profesor la aplicación funcionando en local. Entrega un documentación resumida donde expliques los pasos fundamentales para realizar esta tarea.



### Tarea 3: Configuración multinodo

**1. Realiza un copia de seguridad de la base de datos**

**2. Crea otra máquina con vagrant, conectada con una red interna a la anterior y configura un servidor de base de datos.**

**3. Crea un usuario en la base de datos para trabajar con la nueva base de datos.**

**4. Restaura la copia de seguridad en el nuevo servidor de base datos.**

**5. Desinstala el servidor de base de datos en el servidor principal.**

**6. Realiza los cambios de configuración necesario en drupal para que la página funcione.**

Entrega un documentación resumida donde expliques los pasos fundamentales para realizar esta tarea. En este momento, muestra al profesor la aplicación funcionando en local.



### Tarea 4: Instalación de otro CMS PHP

**1. Elige otro CMS realizado en PHP y realiza la instalación en tu infraestructura.**

**2. Configura otro virtualhost y elige otro nombre en el mismo dominio.**

En este momento, muestra al profesor la aplicación funcionando en local. Y describe en redmine los pasos fundamentales para realizar la tarea.



### Tarea 5: Necesidad de otros servicios

**1. La mayoría de los CMS tienen la posibilidad de mandar correos electrónicos (por ejemplo para notificar una nueva versión, notificar un comentario,…)**

**2. Instala un servidor de correo electrónico en tu servidor. debes configurar un servidor relay de correo, para ello en el fichero /etc/postfix/main.cf, debes poner la siguiente línea:**

      relayhost = babuino-smtp.gonzalonazareno.org


**3. Configura alguno de los CMS para utilizar tu servidor de correo y realiza una prueba de funcionamiento.**

Muestra al profesor algún correo enviado por tu CMS.

