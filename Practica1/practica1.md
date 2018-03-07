# Práctica 1

## Instalación de dos máquinas virtuales (usando VirtualBox) mostrándo su correcto funcionamiento.

Instalo ambas máquinas virtuales en virtualbox, así como LAMP y SSH en cada una de ellas.
LAMP: (durante la instalación de UbuntuServer)
![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/1.png)



Instalo SSH mediante el comando:

`sudo apt-get install ssh`

Podría también haber instalado ssh directamente durante la instalación de ubuntuserver

Activo en ambas máquinas la cuenta root para no tener que escribir "sudo" al principio de cada comando:

`sudo passwd root`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/2.png)

Compruebo que se ha instalado correctamente LAMP, mirando la versión de apache por ejemplo:
![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/3.png)

Ha quedado comprobado que funciona, creo un fichero html en /var/www/hola.html para después:
`<HTML>
  <BODY>Fichero de prueba. Este servidor funciona! :D
  </BODY>
 </HTML>`
Y algo similiar en la otra máquina.
Una vez creado, reinicio apache para que se guarden los cambios:

`service apache2 reload`

Para que ambas máquinas virtuales tengan conexión entre sí, así como cada máquina conexión con el anfitrión tengo que modificar la configuración de Red de VirtualBox, añadiendo: 
![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/4.png)
![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/5.png)

Además, hay que añadir un par de líneas de el archivo /etc/network/interfaces:
![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/6.png)
Es importante tener en cuenta que la dirección debe ser distinta en ambas máquinas, y que estén dentro del rango establecido en las Preferencias de VirtualBox.

Con esto, ya debería estar establecidas las conexiones entre las máquinas correctamente. Voy a comprobarlo:
Curl entre las máquinas: ![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/7.png)
Y desde el anfitrión a ambas máquinas: ![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/8.png)

Para permitir establecer la conexión de ssh de una máquina a otra, tengo que editar el ficher /etc/ssh/sshd_config, habilitándo el acceso de root mediante contraseña:

`PermitRootLogin yes`

Reinicio el servicio, y ya funciona:

`systemctl restart sshd.service`

![imagen](https://github.com/TehRibbon/SWAP/blob/master/Practica1/Capturas/9.png)

