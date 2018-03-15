# Práctica 2.

## Clonar la información de un sitio web

### Cuestiones a resolver

Hay que llevar a cabo las siguientes tareas:

 - Probar el funcionamiento de la copia de archivos por ssh
 - Clonado de una carpeta entre las dos máquinas
 - Configuración de ssh para acceder sin que solicite la contraseña
 - Establecer una tarea en cron que se ejecute cada hora para manterner actualizado el contenido del directorio /var/www entre las dos máquinas

*************************

### Funcionamiento de la copia de archivos por ssh

Voy a probar la transmisión de un fichero tar.tgz por ssh, desde la máquina swap2018(máquina 1 en adelante) a la máquina swap2018-2(máquina 2 en adelante):

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/tarssh.png)

Instalo la herramienta rsync mediante el comando:

`sudo apt-get install rysnc`

Para no tener que estar trabajando como root o teniéndo que introducir constantemente la contraseña, hago dueño al usuario de la carpeta donde residen los archivos que hay en el espacio web en ambas máquinas con:

`sudo chown tehribbon:tehribbon -R /var/www`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/chown.png)

### Clonar contenido entre dos máquinas con rsync

Para probar el correcto funcionamiento de rsync, voy a crear un archivo básico en /var/www/html/

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/rsync1.png)

Vemos como en la máquina 2 el fichero no existe, ahora para clonar la carpeta de la máquina 1 en la maquina 2:

`rsync -avz -e ssh 192.168.56.101:/var/www/ /var/www/`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/rsync2.png)

Podría especificar que directorios copiar y cuales ignorar con la orden:

`rsync -avz --delete --exclude=**/directorioUno --exclude=**/directorioDos -e ssh 192.168.56.101/var/www/ /var/www/`

Los directorios especificados con --exclude=**/loquesea indica los directorios que no deben copiarse y con --delete, los archivos que se hayan borrado en la máquina origen se borrarán también en la que vamos a clonar.

### Configurar el ssh para acceder sin contraseña

Con el fin de poder realizar actualizaciones automáticas con rsync sin tener que depender del administrador, voy a configurar ssh para poder acceder sin contraseña. Para ello usaré autenticación con un par de claves pública-privada.

Genero la clave en la máquina 2(máquina secundaria) mediante el comando:

`ssh-keygen -b 4096 -t rsa`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/clavesecundaria.png)

Con este comando se ha creado la clave privada en ~/ssh/id_rsa y el fichero ~/.ssh/id_rsa.pub para la clave pública

Ahora tengo que copiar la clave pública de la máquina secundaria en la máquina principal, con el comando(ejecutado desde la máquina secundaria):

`ssh-copy-id 192.168.56.101`

Ahora ya podemos hacer ssh a la máquina principal sin contraseña:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/sshlogin.png)

Y en la máquina principal se ha añadido a la máquina secundaria en el archivo ~/.ssh/authorized_keys

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/authorized_keys.png)

Añadiendo tras el ssh un comando, se ejecuta en la máquina destino, en nuestro caso la principal, sin necesidad de contraseña:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/comandossh.png)

### Establecer tareas en cron

Como ultimo apartado, se va a automatizar el proceso de clonado de la información mediante cron.

Para automatizar dicho proceso, voy a editar el archivo /etc/crontab.
Vemos en este fichero las tareas ejecutadas cada x tiempo (horas, dias, semanas, meses) por el usuario root. Como lo que quiero realizar es clonar la carpeta /var/ww/ de la máquina principal en la máquina secundaria cada hora, añadiré en el fichero /etc/crontab de la máquina secundaria:

`0 * * * * tehribbon rsync -avz --delete -e ssh 192.168.56.101:/var/www/ /var/www/ `

Lo cual clonará la carpeta /var/www/ de 192.168.56.101, borrandose también aquellos elementos que se borrasen en la máquina principal, cada hora **:00, es decir, a las 00:00, 01:00, ...,23:00 todos los días del mes, todos los meses, todos los días de la semana llevado a cabo por el usuario tehribbon (el cual tiene permiso para realizar el rsync) 

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/cron.png)

Para comprobarlo, he introducido el siguiente comando que realiza el clonado cada minuto:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/cronttabminutos.png)

Sin actualizar:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/sinactualizar.png)

Y compruebo que realmente se ha producido las actualizaciones tras ir creando ficheros en la máquina principal:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica2/Capturas/actualizaciones.png)

