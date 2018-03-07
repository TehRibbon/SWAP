# Práctica 1

##### Instalación de dos máquinas virtuales (usando VirtualBox) mostrándo su correcto funcionamiento.

Instalo ambas máquinas virtuales en virtualbox, así como LAMP y SSH en cada una de ellas. 



Instalo SSH mediante el comando:

`sudo apt-get install ssh`

Podría también haber instalado ssh directamente durante la instalación de ubuntuserver

Activo en ambas máquinas la cuenta root para no tener que escribir "sudo" al principio de cada comando:

`sudo passwd root`

Compruebo que se ha instalado correctamente LAMP:

Ha quedado comprobado que funciona, por lo que creo un fichero html en /var/www/hola.html

Una vez creado, reinicio apache para que se guarden los cambios:

`service apache2 reload`

Para que ambas máquinas virtuales tengan conexión entre sí, así como cada máquina conexión con el anfitrión tengo que modificar la configuración de Red de VirtualBox, añadiendo: 



Además, hay que añadir un par de líneas de el archivo /etc/network/interfaces:

Con esto, ya debería estar establecidas las conexiones entre las máquinas correctamente.

Para permitir establecer la conexión de ssh de una máquina a otra, tengo que editar el ficher /etc/ssh/sshd_config, habilitándo el acceso de root mediante contraseña:

`PermitRootLogin yes`

Reinicio el servicio, y ya funciona:

`systemctl restart sshd.service`

