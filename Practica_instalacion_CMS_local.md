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
$ sudo apt install mariadb-server mariadb-client
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
vagrant@servidor:~$ sudo mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 45
Server version: 10.3.17-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database mysqldrupal
    -> ;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> create user drupal;
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]> grant all on mysqldrupal.* to drupal identified by 'drupal'
    -> ;
Query OK, 0 rows affected (0.002 sec)

~~~


**3. Descarga la versión que te parezca más oportuna de Drupal (7 o 8) y realiza la instalación.**

Se descarga drupal:
~~~
vagrant@servidor:~$ cd /var/www/
vagrant@servidor:~$ sudo wget https://www.drupal.org/download-latest/zip
~~~

Y se descomprime:
~~~
vagrant@servidor:~$ sudo apt install unzip
vagrant@servidor:~$ sudo unzip zip
~~~

Se configura Apache2:
~~~
vagrant@servidor:/var/www$ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/drupal.conf
~~~

En el nuevo fichero drupal.conf:
~~~
<VirtualHost *:80>
        ServerAdmin admin@example.com
        DocumentRoot /var/www/drupal
        ServerName www.paloma-drupal.org
        <Directory "/var/www/drupal/">
                Options FollowSymLinks
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>
                ErrorLog /var/log/apache2/drupal-error_log
                CustomLog /var/log/apache2/drupal-access_log common
</VirtualHost>
~~~

Y se reinicia el servicio:
~~~
vagrant@servidor:/var/www$ sudo systemctl restart apache2.service 
vagrant@servidor:/var/www$ sudo a2ensite drupal
Site drupal already enabled
~~~

Desde el navegador, continúa la instalación en 6 pasos, corrigiendo los errores que aparecerán.


**4. Realiza una configuración mínima de la aplicación (Cambia la plantilla, crea algún contenido, …)**

Todos estos cambios se realizan desde el navegador de forma muy sencilla y amigable. 


**5. Instala un módulo para añadir alguna funcionalidad a drupal.**
Se ha añadido el módulo foro.



### Tarea 3: Configuración multinodo

**1. Realiza un copia de seguridad de la base de datos**

Se hace la copia de la base de datos:
~~~
vagrant@servidor:~$ mysqldump -v --opt --events --routines --triggers --default-character-set=utf8 -u drupal -p mysqldrupal > copiadrupal_`date +%Y%m%d_%H%M%S`.sql
~~~

**2. Crea otra máquina con vagrant, conectada con una red interna a la anterior y configura un servidor de base de datos.**
Se modifica Vagrantfile para crear un nuevo nodo con una red interna conectada con el nodo anteriormente creado. 


**3. Crea un usuario en la base de datos para trabajar con la nueva base de datos.**

Como en la primera máquina, se instala mariadb (Punto 2 de la Tarea 1 y punto 2 de la Tarea 2).


**4. Restaura la copia de seguridad en el nuevo servidor de base datos.**
Se restaura la base de datos:
~~~
vagrant@nodo2:~$ mysql -u drupal --password=drupal mysqldrupal < copiadrupal_20191020_100152.sql
~~~

Y se configura la base de datos para que se pueda acceder a ella de forma remota modificando el fichero /etc/mysql/mariadb.conf.d/50-server.cnf:
~~~
bind-address            = 0.0.0.0
~~~



**5. Desinstala el servidor de base de datos en el servidor principal.**

~~~
vagrant@servidor:/var/www$ sudo apt purge mariadb-*
~~~

**6. Realiza los cambios de configuración necesario en drupal para que la página funcione.**

En el fichero setting.php:
~~~
$databases['default']['default'] = array (
  'database' => 'mysqldrupal',
  'username' => 'drupal',
  'password' => 'drupal',
  'prefix' => '',
  'host' => '192.168.100.2',
  'port' => '3306',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
);
~~~



### Tarea 4: Instalación de otro CMS PHP

**1. Elige otro CMS realizado en PHP y realiza la instalación en tu infraestructura.**
~~~
wget https://downloads.joomla.org/cms/joomla3/3-9-12/Joomla_3-9-12-Stable-Full_Package.zip
unzip Joomla_3-9-12-Stable-Full_Package.zip 
vagrant@servidor:~$ sudo mv joomla/ /var/www/joomla
vagrant@servidor:/var/www$ sudo chown -R www-data:www-data joomla/
~~~
~~~
vagrant@servidor:~$ sudo cp /etc/apache2/sites-available/drupal.conf /etc/apache2/sites-available/joomla.conf
~~~
~~~
<VirtualHost *:80>
        ServerAdmin admin@example.com
        DocumentRoot /var/www/joomla
        ServerName www.paloma-joomla.org
        <Directory "/var/www/joomla/">
                Options FollowSymLinks
                AllowOverride All
                Order allow,deny
                allow from all
        </Directory>
                ErrorLog /var/log/apache2/drupal-error_log
                CustomLog /var/log/apache2/drupal-access_log common
</VirtualHost>
~~~
~~~
vagrant@servidor:/var/www$ sudo a2ensite joomla
Enabling site joomla.
To activate the new configuration, you need to run:
  systemctl reload apache2
vagrant@servidor:/var/www$ sudo systemctl reload apache2.service 
~~~

Se crea la base de datos para joomña:
~~~
vagrant@servidor:~$ sudo mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 45
Server version: 10.3.17-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database mysqljoomla
    -> ;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> create user joomla;
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]> grant all on mysqjoomla.* to joomla identified by 'joomla'
    -> ;
Query OK, 0 rows affected (0.002 sec)
~~~

Por último, el isntalador pedirá que se elimine el cirectorio "installation".



**2. Configura otro virtualhost y elige otro nombre en el mismo dominio.**
~~~
192.168.43.127  www.paloma-drupal.org
192.168.43.127  www.paloma-joomla.org
~~~




### Tarea 5: Necesidad de otros servicios

> La mayoría de los CMS tienen la posibilidad de mandar correos electrónicos (por ejemplo para notificar una nueva versión, notificar un comentario,…) 

**1. Instala un servidor de correo electrónico en tu servidor. debes configurar un servidor relay de correo, para ello en el fichero /etc/postfix/main.cf, debes poner la siguiente línea:**

      relayhost = babuino-smtp.gonzalonazareno.org

~~~
vagrant@servidor:/var/www/joomla$ sudo apt update
vagrant@servidor:/var/www/joomla$ sudo apt install postfix postfix-doc
~~~

Se configura el fichero /etc/postfix/main.cf:
~~~
smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = servidor.gonzalonazareno.org.
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination = $myhostname, gonzalonazareno.org, localhost.localdomain, localhost
relayhost = babuino-smtp.gonzalonazareno.org
mynetworks = 172.0.0.0/8, 0.0.0.0
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
inet_protocols = ipv4
html_directory = /usr/share/doc/postfix/html

~~~

Y se inicia:
~~~
vagrant@servidor:/var/www/joomla$ sudo systemctl start postfix
Failed to start postfix.service: Unit postfix.service is masked.
vagrant@servidor:/var/www/joomla$ sudo systemctl unmask postfix
Removed /etc/systemd/system/postfix.service.
vagrant@servidor:/var/www/joomla$ sudo systemctl start postfix
~~~




**2. Configura alguno de los CMS para utilizar tu servidor de correo y realiza una prueba de funcionamiento.**

Muestra al profesor algún correo enviado por tu CMS.

