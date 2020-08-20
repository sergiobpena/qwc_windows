| [INICIO](./README.md)|[ANTERIOR](./03_CLIENTE.md)          | [SEGUINTE](./05_FLUXO_TRABALLO.md)  |
| ------------- |:-------------:| -----:|
# SERVIZO CLIENTE

Servizos para o cliente en servidores independientes (desarrollo)

- [Documentacion servizos ](https://github.com/sourcepole/qwc2-server)

### Test

Descargar os servidores flask e configurar o entorno virtual:
- [Documentacion](https://realpython.com/python-virtual-environments-a-primer/)

~~~
$ cd /home/qgis/qwc

$ git clone https://github.com/sourcepole/qwc2-server.git

$ cd qwc2-server
# Creacion do entorno virtual para os servidores
$ apt install -y python3-pip python3-venv
$ python3 -m venv ./qwc_env

$ source ./qwc_env/bin/activate
## De precisarse volver o python global..... $ deactivate

$ pip install -r requirements.txt

$ pip install requests
~~~

Executar como servizos os servidores:

- [Documentantacion](https://medium.com/@manivannan_data/how-to-deploy-the-flask-app-as-ubuntu-service-399c0adf3606)

Ruta do venv:
~~~
$ which python
> /home/qgis/qwc/qwc2-server/qwc_venv/bin/python
~~~
Anotar esta ruta


Cambiar na primeira liña do servidor (exemplo proxy.py).
Aqui empregar o editor de texto en liña de debian é mais comodo , accedendo a terminal dende ssh


~~~
#!/usr/bin/python
~~~
pola salida do anterior
~~~
#!/home/qgis/qwc/qwc2-server/qwc_venv/bin/python
~~~

systemctl daemon-reload
Crear arquivo de inicio

/home/qgis/qwc/qwc2-server


/etc/init/

/lib/systemd/system/
permalink_flask.service
mapInfo_flask.service
featureReport.service
proxy_flask.service
~~~
[Unit]
Description=Flask proxy server
After=network.target
[Install]
WantedBy=multi-user.target
[Service]
User=root
Group=root
PermissionsStartOnly=true
WorkingDirectory=/home/qgis/qwc/qwc2-server
ExecStart= /home/qgis/qwc/qwc2-server/qwc_venv/bin/python proxy.py
TimeoutSec=600
Restart=on-failure
RuntimeDirectoryMode=755
~~~