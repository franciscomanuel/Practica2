Practica2
=========

Para la realización de la segunda práctica voy a utilizar la aplicación usada para la práctica1 y la voy a aislar en 
una jaula chroot.

Mi programa creado en la práctica1 era una aplicación web en php en la que aparecía una foto mia, seguida de mis datos
(nombre, correo y twitter) y dos tablas. La primera de ella es mi horario para el primer cuatrimestre y la segunda tabla 
contiene información sobre cada una de mis asignaturas(nombre, código, tipo ...).

He usado una licencia GPLV3. Esta licencia la he generado al crear el proyecto seleccionando como tipo de licencia 
"LICENCE GPLV3".

Dicho todo esto procedo a explicar los pasos para aislar en una jaula chroot mi aplicación:

En primer lugar si no se tiene instalado la herramienta debootstrap debemos instalarla con:

	  sudo apt-get install debootstrap
	
Esta herramienta es un conjunto de archivos del shell que localizan la descripción de la distro y la descargan.

Una vez instalada voy a crear un sistema mínimo para ubuntu con una arquitectura de 64 bits y la distribución Saucy Salamander. Lo voy a crear en el directorio /home/jaulas/saucy que previamente abré creado.

	sudo mkdir -p /home/jaulas/saucy
	sudo debootstrap --arch=amd64 saucy /home/jaulas/saucy/	http://archive.ubuntu.com/ubuntu
	
Podemos ver que nuestro sistema mínimo se ha creado correctamente en la siguiente captura de pantalla:

![imagen1](https://dl.dropbox.com/s/okmblqhjfg2co7h/p2_imagen1.png)

Una vez creada la máquina está usable pero no esta completa. Debemos de montar el filesystem virtual /proc, pero
antes debemos entrar en la jaula:

	sudo chroot /home/jaulas/saucy
	mount -t proc proc /proc
	
Estoy montando el filesystem tipo proc (el primero) en el subdirectorio proc (el segundo) y usando /proc de la máquina 
anfitriona.

Luego instalo el paquete español para evita que nos dé una serie de errores.

	apt-get install language-pack-es
	
Ahora el siguiente paso es instalar el servidor apache:

	apt-get install apache2
	
Instalado todo esto ya podemos enjaular nuestra aplicación, para ello copio mi aplicación dentro de mi jaula en el 
directorio /var/www

	cp -r php /home/jaulas/saucy/var/www
	
Con la opción -r hago un copiado recursivo de la carpeta php

El siguiente paso es reiniciar apache:

	etc/init.d/apache2 restart
	
Si nos metemos en localhost podemos ver nuestra aplicación funcionando "a medias" ya que no nos aparece ni el fondo 
ni las imagenes. Para solucionar esto debemos de darle permisos a la carpeta /home/jaulas/saucy/var/www.

	sudo chroot -R 755 /home/jaulas/saucy/var/www
	
Reiniciamos de nuevo apache:

	etc/init.d/apache2 restart
	
Y ya si que tenemos nuestra aplicación funcionando correctamente:

![imagen2](https://dl.dropbox.com/s/ov5i51h5bcalrx3/p2_imagen2.png)
