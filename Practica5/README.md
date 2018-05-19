# Práctica 5

## Replicación de bases de datos MySQL

### Cuestiones a resolver:

 - Crear una BD con al menos una tabla y algunos datos
 - Relizar la copia de seguridad de la BD completa usando mysqldump en la máquina principal y copiar el archivo de copia de seguridad al máquina secundaria.
 - Restaurar dicha copia de seguridad en la segunda máquina (clonado manual de la BD), de forma que en ambas máquinas esté esa BD de forma idéntica.
 - Realizar la configuración maestro-esclavo de los servidores MySQL para que la replicación de datos se realice automáticamente

****

#### Crear una BD e insertar datos

Para crear una base de datos, entramos en mysql como root con el comando:

`mysql -uroot -p`

Nos solicitará la contraseña del usuario en cuestión. Ya estamos dentro de mysql, ahora solo hay que ejecutar la query para crear la base de datos:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/2.png)

Con este comando hemos creado la base de datos, ahora para crear una tabla e insertar datos en ella:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/3.png)

Podemos comprobar la estructura de la base de datos con el siguiente comando:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/4.png)

#### Replicar una BD MySQL con mysqldump

Una vez creada la BD, pasamos a exportarla mediante el comando:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/5.png)

La base de datos se ha exportado al directorio temporal /tmp:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/6.png)

Desde la máquina esclavo, para obtener el fichero .sql ejecuto:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/7.png)

Una vez copiado el fichero, entramos en mysql con el comando:

`mysql -uroot -p`

Y ejecuto:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/8.png)

Y la BD ha sido importada.

#### Replicación mediante configuración maestro-esclavo

Desde la máquina maestra, editamos el fichero `/etc/mysql/mysql.conf.d/mysqld.cnf`

Realizando los cambios citados en el guión y reiniciamos el servicio.

El maestro está configurado correctamente, ahora desde el esclavo edito el mismo fichero, teniendo en cuenta que server-id = 2 y reiniciamos el servicio

Desde la máquina servidora creo un usuario esclavo y le proporcion privilegios, además mostramos la siguiente información:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/10.png)

En la máquina esclava, teniendo en cuenta la información de la imagen anterior, ejecuto el comando (dentro de mysql):

`CHANGE MASTER TO MASTER_HOST='192.168.56.101', MASTER:USER='esclavo', MASTER_PASSWORD='esclavo', MASTER_LOG_FILE='bin.000001', MASTER_LOG_POS=154, MASTER_PORT=3306`

Y ejecuto el comando: `START SLAVE`

Compruebo que todo está funcionando correctamente:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/20.png)

Y ahora, todos los cambios que realiza en la BD de la máquina master, se replicaran en el esclavo:

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica5/Capturas/21.png)
