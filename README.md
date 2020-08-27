## Requerimentos
- [XAMMP](https://www.apachefriends.org/es/index.html)
- [osgeo installer]()
- [Node](https://nodejs.org/es/) 
- [Yarn](https://classic.yarnpkg.com/es-ES/docs/install#windows-stable)
- [Git](https://git-scm.com/downloads)
- IDE (Recomendable, pero podese amañar co notepad++) [Visual Studio Code](https://code.visualstudio.com/)
## Configuracion apache 
- Descarga apache [Enlace](https://httpd.apache.org/docs/2.4/platform/windows.html)
- Descarga modulo fcgid [Enlace](http://www.apachelounge.com/download/VC15/modules/mod_fcgid-2.3.9-win64-VC15.zip)

1. Descomprimir o zip de apache en c:\\
2. Modificar c:\Apache24\conf\httpd.conf , atopando e modificando o seguinte:
~~~
ServerName 127.0.0.1:88

Listen 88
~~~
2. Descomentar ou engadir o que corresponda : 
~~~
LoadModule actions_module modules/mod_actions.so

LoadModule deflate_module modules/mod_deflate.so

LoadModule expires_module modules/mod_expires.so

LoadModule ext_filter_module modules/mod_ext_filter.so

LoadModule fcgid_module modules/mod_fcgid.so

LoadModule headers_module modules/mod_headers.so

LoadModule ident_module modules/mod_ident.so

LoadModule rewrite_module modules/mod_rewrite.so

LoadModule ssl_module modules/mod_ssl.so

LoadModule cache_module modules/mod_cache.so

LoadModule cache_disk_module modules/mod_cache_disk.so

LoadModule proxy_module modules/mod_proxy.so

LoadModule proxy_connect_module modules/mod_proxy_connect.so

LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so

LoadModule proxy_http_module modules/mod_proxy_http.so
~~~
4. Engadir o final do arquivo :

~~~
<IfModule mod_deflate.c>
SetOutputFilter DEFLATE
BrowserMatch ^Mozilla/4 gzip-only-text/html
BrowserMatch ^Mozilla/4\\.0[678] no-gzip
BrowserMatch \\bMSIE !no-gzip !gzip-only-text/html
SetEnvIfNoCase Reuqest_URI \\.(?:gif|jpe?g|png|rar|zip)$ no-gzip dont-vary
Header append Vary User-Agent env=!dont-vary
</IfModule>
~~~

AddHandler cgi-script .cgi .pl .asp .exe

5. Editar o virtualHost para o acceso a qgisserver
Descomentar linea

Include conf/extra/httpd-vhosts.conf



\Apache\Conf\extra\httpd-vhosts.conf 
~~~
<VirtualHost *:80>
  ServerName 127.0.0.1
Alias /qgis/ "C:/OSGeo4W64/apps/qgis-ltr/bin/"
<Directory "C:/OSGeo4W64/apps/qgis-ltr/bin/">
    SetHandler fcgid-script
    Options +ExecCGI
    AllowOverride All
    Require all granted
</Directory>
</VirtualHost>
~~~


httpd.exe -k install

 httpd.exe -k restart, httpd.exe -k shutdown

Documentacion

https://opengislab.com/blog/2018/7/7/updated-installing-apache-qgis-server-and-lizmap-on-windows-os


https://docs.qgis.org/3.10/en/docs/user_manual/working_with_ogc/server/getting_started.html#installation-on-windows


## Posta en marcha do cliente en modo desarrollo

Creo que para definir , o mellor e partir do exemplo de https://github.com/qgis/qwc2-demo-app para iso , unha vez instalado o anterior , seria 
1. Definir o espazo de traballo do visual studio
    1.  Abrir o visual studio e en arquivo ir a abrir carpeta  , dende ahi , pois creas unha carpeta en calquera sitio que che sexa comodo , e unha vez dentro daslle a seleccionar carpeta.
    2. Co editor aberto , no menu de terminal , seleccionas novo terminal , quedando o ide asi :
    ![Imaxe ide listo](./arquivos/01_ide.PNG)

2. Descarga do exemplo e proba:
    1. No terminarl de VStudio escribes:
    ~~~
    $ git clone --recursive https://github.com/qgis/qwc2-demo-app.git
    ~~~
    Esto copian o proxecto e exemplo a carpeta onde tes aberto o VStudio
    
    2. Proba do proxecto:
    Primeiro hai que entrar dende a consola do VStudio a carpeta que se descargou con git
    ~~~
    $ cd qwc2-demo-app
    ~~~
    *Co tabulador autocompletas as rutas*
    
    Logo :
    ~~~
    $ yarn install
    ~~~
    Descarga paquetes e dependencias.
    Logo arrancas o servidor do cliente con:
    ~~~
    $ yarn start
    ~~~

    A consola quedaria algo como esto :

    ![Imaxe execucion](./arquivos/02_execucion.PNG) 

    Ahora , nun navegador escribirias :
    ~~~
    localhost:8081
    ~~~
    E abririase o proxecto de exemplo : 

    ![Exemplo cliente](./arquivos/04_proba.PNG)

    Para parar o servidor , na consola do visual studios , premese control + c , e aceptas:
    ![Parada](./arquivos/03_parada.PNG)

## Trasteando e enlazado do proxecto QGIS

1. Engadindo a traduccion o español:

No arquivo /js/AppConfig.js : 
![Idioma](./arquivos/05_configuracion_idioma.PNG)
Engades no seguinte punto :
~~~
        "es":{
            code : "es-ES",
            description:"Espanol",
            localeData: require('react-intl/locale-data/es')
        }
~~~
![Idioma fin](./arquivos/06_configuracion_idioma.PNG)

2. ESPG ------ NON FUNCIONA!!!!!
Engadir o ETRS 89 no arquivo config.json engadindo: 
~~~
    {
      "code":"ESPG:25829",
      "proj":"+proj=utm +zone=29 +ellps=GRS80 +units=m +no_defs ",
      "label":"ETRS89 / UTM 29N"  
    },
~~~
![Proxeccions](./arquivos/07_proxeccions.PNG)

3. Enlazar o proxecto qgis

No arquivo themesConfig.json.
Ten tres puntos de configuracion , items , que son os proxectos que carga por defecto , e themes que serian outros proxectos a cargar e backgroudlayer(capas base).
![themes](./arquivos/08_themes.PNG)
Aqui xa son cousas que tes que decidir segun sexa o a definición do proxecto , pero a priori , eu para probar , cargueime os grupos, configurei un item (proxecto ) , e engadin unha capa base do wms do IGN mapa base (co MTN e as ortos do PNOA)
- Enlazando o proxecto Qgis

Primeiro asegurate de ter o proxecto qgis definido nos seguintes puntos https://github.com/qgis/qwc2-demo-app/blob/master/doc/QWC2_Documentation.md#theme-configuration-qgis-projects-and-the-themesconfigjson-file

Logo :
~~~
    "items": [
      {
        "url": "http://localhost/cgi-bin/qgis_mapserv.fcgi.exe?MAP=C:%5CQGServer%5Cproxecto%5Cexemplo.qgs",
        "attribution": "Sergio",
        "attributionUrl": "xxxx.xx",
        "default":"true",
        "printScales": [80000, 40000, 20000, 10000, 8000, 6000, 4000, 2000, 1000, 500, 250, 100],
        "printResolutions": [150, 300, 600],
        "backgroundLayers": [
          {
            "name": "base_ign"
          },
          {
            "name": "mapnik",
            "visibility": true
          }
        ],
        "searchProviders": ["coordinates", "nominatim"],
        "mapCrs": "EPSG:3857",
        "additionalMouseCrs": ["EPSG:25829"]
      }
    ],
~~~
![Item](./arquivos/09_Items.PNG)

*A imaxe e para ver onde vai encastrada a configuracion, fiate do codigo non da imaxe*

Onde tes que modificar a url como segue:

http://localhost/cgi-bin/qgis_mapserv.fcgi.exe?MAP=

E a continuación a ruta do proxecto qgis , cambiando as barras \ por %5C

- Engadino capa base wms

Os parametros saqueinos do getcapabilities do servizo : 

~~~
      {
        "name":"base_ign",
        "type":"wms",
        "url":"https://www.ign.es/wms-inspire/ign-base",
        "params":{
          "LAYERS":"IGNBaseOrto",
          "STYLES":"default"
        },
        "title":"Base IGN",
        "srs": "EPSG:3857"
      },
~~~

- Compilando as configuracions
Logo hai que executar na consola de visual estudio na carpeta da demo como antes (ollo, ten que estar o servizo de apache activado e correndo no XAMPP) :
~~~
$ yarn run themesconfig
~~~
e logo
~~~
$ yarn start
~~~

no navegador localhost:8081 : 
![Funcionamento](./arquivos/11_funionmento.PNG)
