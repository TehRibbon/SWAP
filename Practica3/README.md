# Práctica 3. 

## Balanceo de carga

###### Cuestiones a resolver:

 - Configurar una máquina e instalar el nginx como balanceador de carga.
 - Configurar una máquina e instalar el haproxy como balanceador de carga.
 - Someter a la granja web a una alta carga, generada con la herramienta Apache Benchmark, teniendo primero nginx y después haproxy.

Creo una máquina nueva que actuará de balanceador de la granja web, sin ningún software que se apropie del puerto 80, "SWAP balancer".

Las direcciones IP de las máquinas durante la práctica será:

|         MÁQUINA         |       IP       |
| :---------------------: | :------------: |
|    SWAP (máquina 1)     | 192.168.56.101 |
| SWAP clonar (máquina 2) | 192.168.56.102 |
|       SWAP nginx        | 192.168.56.103 |
|      SWAP haproxy       | 192.168.56.104 |

##### NGINX

###### INSTALACIÓN

En la máquina balanceadora, instalo nginx mediante los comandos:

`sudo apt-get update && sudo apt-get dist-upgrade && sudo apt-get autoremove`

`sudo apt-get install nginx`

`sudo systemctl start nginx`

###### CONFIGURACIÓN

Hay que modificar el fichero /etc/nginx/conf.d/default.conf para crear la configuración que necesitamos. En este fichero hay dos bloques principales:

	- upstream: indicaremos las IP  de todos los servidores finales de nuestra granja web
- server: indicamos a nginx las máquinas a las cuales se les debe repartir el tráfico (se encuentran en upstream), además de especificarles otros detalles de la configuración(que la conexión sea HTTP 1.1 así como eliminar la cabecera Connection)

El fichero final de configuración quedará como:

~~FOTO config~~

Además he tenido que comentar la línea en /etc/nginx/nginx.conf para que actuase como balanceador y no como servidor web:

`#include /etc/nginx/sites-enabled/*;`

Iniciamos el servicio de nginx:

`sudo systemctl start nginx`

Compruebo que está escuchando en el puerto 80 nginx, y no otro software.

~~puertoescucha.png~~

Para comprobar que está funcionando voy a realizar peticiones a esta máquina balanceadora.

~~FOTO comp_nginx~~

Y muestra de forma alternativa la página de inicio de cada una de las máquinas

Podemos modificar el valor de "weight" de cada máquina para que atribuyamos mas carga a la máquina mas potente o menos sobrecargarda. Por ejemplo, con (en /etc/nginx/conf.d/default.conf:

`server 192.168.56.101 weigth=1`

`server 192.168.56.102 weight=2`

De cada 3 peticiones, 1 será para la máquina 1, y 2 para la máquina 2, entendiéndose así la máquinas mas potente o menos sobrecargada a la máquina 2.

~~foto pesos~~

También se puede realizar un balanceo añadiendo en el default.conf, dentro de upstream apaches:

`ip_hash;`

Para que la conexión se realice de forma persistente (KeepAlive) añadimos al final de upstream apaches:

`keepalive 3`

##### HAPROXY

Creo otra máquina distinta para configurar el balanceador de carga con haproxy

###### INSTALACIÓN

Instalo haproxy en la máquina mediante:

`sudo apt-get install haproxy`

###### CONFIGURACIÓN

Tengo que editar el archivo /etc/haproxy/haproxy.cfg, quedando de la forma:

~~confighaproxy~~

Lanzo el servidio haproxy con:

`sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg`

Y compruebo si está funcionando correctamente realizando peticiones desde el anfitrión:

~~comphaproxy~~

##### APACHE BENCHMARK

Voy a realizar el benchmark desde mi máquina anfitrión, para ello el comando que voy a usar, tanto para nginx como haproxy:

`ab -n 1000 -c 10 http://192.168.56.103/index.html`

`ab -n 1000 -c 10 http://192.168.56.104/index.html`

Y los resultados son, para nginx:

~~abnginx~~

Y para haproxy:

~~haproxy~~

Y aumentando el numero de peticiones y concurrencia, con el comando:

`ab -n 50000 -c 15 http://192.168.56.103/index.html`

`ab -n 50000 -c 15 http://192.168.56.104/index.html`

Los resultados son, para nginx:

~~abnginxlargo~~

Y para haproxy:

~~abhaproxylargo~~

En ambos tests nginx ha sido mas rápido, pero debido a la sencillez de la granja web y de las peticiones realizadas, no puedo determinar como conclusión que balanceador es mejor bajo estos términos.