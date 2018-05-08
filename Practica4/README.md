# Práctica 4

## Asegurar la granja web

Cuestiones a resolver:

 - Crear e instalar en las máquinas un certificado SSL autofirmado para configurar el acceso HTTPS a los servidores. Configurar también el balanceador de carga para aceptar tanto el tráfico HTTP como el HTTPS
 - Configurar las reglas del cortafuegos con IPTABLES para asegurar el acceso a uno de los servidores web. Esta configuración se hará en una de las máquinas servidoras finales



###### Certificado SSL

En todas las máquinas finales he seguido los mismos pasos:

Ejecuto: `a2enmod ssl`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/1.png)

Y reinicio el servicio con `service apache2 restart`

Ahora, hay que crear la clave y certificado con:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/2.png)

Edito el archivo de configuración del sitio default-ssl, añadiendo:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/3.png)

Y activo el sitio default-ssl:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/4.png)

Una vez hecho esto, tengo acceso por https (puerto 443) directamente a las máquinas finales

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/5.png)

Ahora necesito configurar el balanceador para que acepte tanto el tráfico HTTP como HTTPS. Para ello creo una clave y certificado en el balanceador, y las voy a guardar en /etc/ssl:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/6.png)

Una vez hecho, me dirijo al archivo de configuración de nginx, para permitir que escuche por el puerto 443 y activar ssl:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/7.png)

Y ahora el balanceador está configurado correctamente, y acepta tráfico HTTPS y HTTP

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/8.png)

###### IPTABLES

La configuración se ha realizado en la máquina final 1. He creado un script, guardado en /etc/iptables_script.sh, con la siguiente estructura:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/10.png)

Con ello, se está permitiendo el tráfico al puerto 80 y al puerto 443 únicamente, borrando toda la configuración anterior y poniéndola por defecto.

Antes de ejecutar el script, comprobé mediante nmap que puertos estaban a la escucha, cuyo resultado:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/12.png)

Por lo tanto, si ejecuto el script con:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/11.png)

La configuración debería ser distinta ahora. Compruebo de nuevo mediante nmap, y veo que es correcto:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/13.png)

Para acabar, faltaría configurar este script para ejecutarlo cuando se encienda la máquina, para ello he añadido en /etc/rc.local el comando a ejecutar:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica4/Capturas/reboot.png)

Por lo que ejecutará dicho script (que configura IPTABLES correctamente) y la configuración será la deseada.
