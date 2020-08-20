| [INICIO](./README.md)|[ANTERIOR](./01_CONFIG_MV.md)          | [SEGUINTE](./03_CLIENTE.md)  |
| ------------- |:-------------:| -----:|
# APACHE E QGIS SERVER

## INDICE
 - [Instalacion apache debian](##-Instalacion-de-apache)
 - [Instalacion QGIS Server](##-Instalación-QgisServer)
 - [Configuracion apache](##-Configuracion-apache)

## Instalación de apache
Solo a parte de instalación de apache, é interesante o apartado de configuración de virtualHost para entender logo a configuración de QgisServer
- [Enlace](https://www.tecmint.com/install-apache-with-virtual-hosts-on-debian-10/)
~~~
$ sudo apt update -y
$ sudo apt install -y apache2 libapache2-mod-fcgid
~~~
Para a xestion do servicio de apache :
~~~
$ sudo systemctl status apache2

$ sudo systemctl enable apache2

$ sudo systemctl start apache2

$ sudo systemctl restart apache2
~~~

## Instalación QgisServer
- [Referencia](https://qgis.org/es/site/forusers/alldownloads.html#debian-ubuntu)

Na terminal ssh como root :

~~~
$ apt install gnupg software-properties-common

$ wget -qO - https://qgis.org/downloads/qgis-2020.gpg.key | gpg --no-default-keyring --keyring gnupg-ring:/etc/apt/trusted.gpg.d/qgis-archive.gpg --import

$ chmod a+r /etc/apt/trusted.gpg.d/qgis-archive.gpg

$ add-apt-repository "deb https://qgis.org/debian buster main"

$ apt update -y && apt install -y qgis qgis-plugin-grass qgis-server
~~~
## Configuracion apache
- [Referencia configuracion VHost Qgis Server](https://docs.qgis.org/3.10/en/docs/training_manual/qgis_server/install.html)
- [Documentación cfg qgis server ](https://docs.qgis.org/3.10/en/docs/user_manual/working_with_ogc/server/index.html)
- [Habilitar CORS en Apache](https://riptutorial.com/es/apache/example/19826/habilitar-cors)
- [Apuntes apache San Clemente](./arquivos/apache_s_clemente.pdf)
- [Exemplo Windows](https://opengislab.com/blog/2018/7/7/updated-installing-apache-qgis-server-and-lizmap-on-windows-os)
- Prieiro instalase o modulo headers de apache , para que as peticions do cliente se acepten:
~~~
$ a2enmod headers
$ service apache2 reload
~~~
Configuracion dos portos
~~~
/etc/apache2/ports.conf

Listen X
~~~

Configurase o virtual host :
~~~
$ nano /etc/apache2/sites-available/qgis_asv
~~~
~~~
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  ServerName qgis.demo

  DocumentRoot /var/www/html

  # Apache logs (different than QGIS Server log)
  ErrorLog ${APACHE_LOG_DIR}/qgis.demo.error.log
  CustomLog ${APACHE_LOG_DIR}/qgis.demo.access.log combined

  # Longer timeout for WPS... default = 40
  FcgidIOTimeout 120

  FcgidInitialEnv LC_ALL "en_US.UTF-8"
  FcgidInitialEnv PYTHONIOENCODING UTF-8
  FcgidInitialEnv LANG "en_US.UTF-8"

  # QGIS log (different from apache logs) see https://docs.qgis.org/testing/en/docs/user_manual/working_with_ogc/ogc_server_support.html#qgis-server-logging
  FcgidInitialEnv QGIS_SERVER_LOG_FILE /var/log/qgis/qgisserver.log
  FcgidInitialEnv QGIS_SERVER_LOG_LEVEL 0

  FcgidInitialEnv QGIS_DEBUG 1

  # default QGIS project
  SetEnv QGIS_PROJECT_FILE /home/qgis/projects/world.qgs

  # QGIS_AUTH_DB_DIR_PATH must lead to a directory writeable by the Server's FCGI process user
  FcgidInitialEnv QGIS_AUTH_DB_DIR_PATH "/home/qgis/qgisserverdb/"
  FcgidInitialEnv QGIS_AUTH_PASSWORD_FILE "/home/qgis/qgisserverdb/qgis-auth.db"

  # See https://docs.qgis.org/testing/en/docs/user_manual/working_with_vector/supported_data.html#pg-service-file
  SetEnv PGSERVICEFILE /home/qgis/.pg_service.conf
  FcgidInitialEnv PGPASSFILE "/home/qgis/.pgpass"

  # Tell QGIS Server instances to use a specific display number
  FcgidInitialEnv DISPLAY ":99"

  # if qgis-server is installed from packages in debian based distros this is usually /usr/lib/cgi-bin/
  # run "locate qgis_mapserv.fcgi" if you don't know where qgis_mapserv.fcgi is
  ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
  <Directory "/usr/lib/cgi-bin/">
    AllowOverride None
    Options +ExecCGI -MultiViews -SymLinksIfOwnerMatch
    Order allow,deny
    Allow from all
    Require all granted
  </Directory>

 <IfModule mod_fcgid.c>
 FcgidMaxRequestLen 26214400
 FcgidConnectTimeout 60
 </IfModule>

</VirtualHost>
~~~
Hai que crear todalas carpetas que se referencian no arquivo e darlle permisos o usuario de apache, exemplo :
~~~
$ chmod -R www-data:www-data /ruta
~~~
Por ultimo activase os virtual host ( a primeira liña so a primeira vez que se vai isto )

~~~
$ a2enmod fcgid
$ a2ensite qgis_asv
$ apachectl configtest
$ systemctl restart apache2
~~~
### Url Servizo 

- Dende host : 
http://localhost:8050/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetCapabilities
- Dende a mv : 
curl http://localhost:90/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetCapabilities