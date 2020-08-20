| [INICIO](./README.md)|[ANTERIOR](./01_CONFIG_MV.md)          | [SEGUINTE](./03_CLIENTE.md)  |
| ------------- |:-------------:| -----:|


# FLUXO DE TRABALLO
## CHULETA DE PARAMETROS
- Ruta paxina inicio cliente :  /var/www/qgis-server/public
- Ruta proxecto : /var/www/qgis-server/proxecto/
- Ruta configuracion vitualhost : /etc/apache2/sites-avaliable/qgis_asv.conf
- Ruta configuracion cliente: /home/qgis/qwc/qwc2-demo-app/

## 1 Creacion modificacion proxecto qgis
- Gardar na carpeta compartida (ollo coas capas e configrar as rutas relativas no proxecto )
- executar o script /home/qgis/actualizar_xxxx.sh
- De cambiar o nome recomendase editar o arquivo de configuración de virtualhost e modificar a variable do proxecto

## Creacion e modificacion do cliente
Seguir o pasos de XXX , todo o que poña localhost, cambialo por 127.0.0.1
- yarn run prod
- fumar varios pitillos
- o que acabe executar o script /home/qgis/actualizar_xxxx.sh

## Test 
No windows , nun navegador :
- Capabilities:
http://localhost:90/cgi-bin/qgis_mapserv.fcgi?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetCapabilities
- Cliente: http://localhost:90/

