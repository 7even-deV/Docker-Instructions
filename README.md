# Instrucciones de Docker


## Instalación

1. Descargamos y ejecutamos como administrador __Docker Desktop__. (En este caso para versión de escritorio para de Windows). _Necesitará reinicar el PC_.
> https://www.docker.com/get-started

2. Descargamos y ejecutamos como administrador __WSL__ (_Windows Subsystem for Linux_), ya que necesita correr sobre el Kernel de Linux.
> https://docs.microsoft.com/en-us/windows/wsl/install

3. Dentro de Windows. Abrimos un __PowerShell__ como administrador y seteamos por defecto el __WSL__ previamente instalado.
> `wsl --set-default-version 2`

4. Descargamos e instalamos el __Linux__ desde _Windows Store_, en el que trabajará a modo de __VM__ (Virtual Machine) dentro de Windows. (En este caso Ubuntu).

5. Dentro de Windows. Abrimos un terminal del __Ubuntu__ previamente instalado y creamos el _username_ y _password_.

6. Antes de continuar, haremos una actualización de paquetes del __Ubuntu__. Ya que suelen contener nuevos parches de seguridad o corrección de errores. Al finalizar las actualizaciones, después habilitaremos los permisos como administrador.
- `sudo apt update && sudo apt upgrade`
- `sudo chmod 666 varrundocker.sock`

7. Dentro de Windows. Abrimos un __PowerShell__ como administrador y seteamos por defecto la versión del sistema operativo virtualizado __Ubuntu__ instalado anteriormente al __WSL__. Así le indicaremos al __Docker Desktop__ que __VM__ usará para correr los contenedores.
> `wsl --set-default ubuntu`

8. Una vez completados todos los pasos anteriores. Abrimos __Docker Desktop__. Y copiamos la instrucción que nos aparece a la __Shell__ de __Ubuntu__.
> `docker run -d -p 80:80 docker/getting-started`

9. Observamos que con el comando _run_ le indicamos que corra el contenedor de la imagen, pero si no la encuentra la buscará y descargará automáticamente de manera local, después la lanzará.

10. Ahora para comprobar que todo funciona correctamente, desde la __Shell__ de __Ubuntu__ descargaremos la imagen de un contenedor de prueba llamado _hello-world_.
> `docker pull hello-world`

11. Observamos que aparece el siguiente mensaje _Using default tag: latest_. Esto es debido a  que si no le indicamos ningún _tag_ (versión de la imagen), cogerá por defecto la versión más reciente de dicha imagen, también llamada _latest_ como instrucción.

12. A continuación, desde el __Docker Desktop__. Verificamos que se creó nuestra imagen _hello-world_, y lo lanzamos con _run_. Esto creará un nuevo contenedor. Importante a tener en cuenta, que los _containers_ son creados de la _image_, y no al contrario.


## Persistencia de datos

***__Bind Mounts__*** :

Cada vez que creamos un contenedor, toda su información será volátil, perdiendo los datos cuando este sea eliminado. Una forma para mantener esa persistencia en los datos es utilizaremos __Bind Mounts__. Este conecta una carpeta de tu equipo anfitrión con una carpeta de tu contenedor. De tal manera, que cada vez que se refleje un cambio en el contenedor, lo escriba en la carpeta de tu equipo anfitrión. Así cada vez que se crea un contenedor, lo lea de la carpeta de tu equipo anfitrión.

1. Primero, abrimos un __Shell__ de __Ubuntu__. Para crear una ruta donde guardaremos de forma local, todo el registro de la imagen que usaremos para cargar los contenedores que creemos.
- `mkdir docker`
- `cd docker`
- `mkdir mongo_db`
- `cd mongo_db`

2. Necesitaremos copiar la ruta absoluta de la carpeta.
> `pwd`

3. Para crear nuestro __Bind Mounts__, en este caso usaremos la siguiente instrucción.
> `docker run -d --name db -v /home/sevenz01/docker/mongo_db:/data/db mongo`

4. En caso que no pueda crearse por que ya se esta utilizando y queramos eliminar el mismo. Después volveríamos a lanzar la instrucción anterior.
> `docker rm -f db`

5. Una vez creado, accedamos al bash del contenedor. Con _exec_ ejecutaremos un comando dentro de un contenedor que ya esta corriendo.
> `docker exec -it db bash`

6. En este caso, __Mongo__ tiene un binario llamado así, accederemos a el para abrir nuestra base de datos. _(Para salir de mongo o del contenedor escribimos `exit`)_.
> `mongo`

7. __Notas__. El comando __-v__  es para especificar a __Docker__ el volumen que usaremos. Este recibe 2 parametros; La ruta local _(donde se guardarán los datos)_ y la ruta especifica donde la imagen escribe los datos e intercambia la información de estos.
Para saber la ruta donde se escriben esos datos entraremos en la página oficial del contenedor de _hub.docker_, y buscaremos el __-v__ de la imagen que necesitemos.
> https://hub.docker.com/


***__Volumenes__*** :

Una de las principales desventajas de los __Bind Mounts__ es que los datos quedan expuestos en nuestro servidor (equipo anfitrión), resultando una grave falla de seguridad. Otra alternativa para persitir los datos es a través de __Volumenes__.

1. Listamos los volumenes que tenemos en nuestro __Docker__.
> `docker volume ls`

2. Creamos nuestro propio volumen.
> `docker volume create "volume_name"`

3. Para eliminar un volumen en especifico.
> `docker volume rm "volume_name"`

4. Para crear nuestro __Container__, en este caso usaremos la siguiente instrucción.
> `docker run -d --name db_name -v /home/sevenz01/docker/mongo_volume:/data/db mongo`

5. __Notas__. Cada vez que queramos usar volúmenes, necesitamos crear un volumen si no existe, y asignarselo a un contenedor, para que la información no quede guardada en una carpeta del sistema de archivos, sino que se guarde en algún lugar del equipo y podamos acceder a la información únicamente a través del contenedor. Disminuyendo así los riesgos de seguridad que nos deriva __Bind Mounts__.

- mount: Indica que la persistencia de datos se hará con volumenes.
- src: Indica el volumen que será usado como base de datos.
- dst: Indica la ruta del contenedor la cual almacena los datos.

- docker ps -a: Muestra toda la información.
- docker inspect "volume": Inspecciona el volume.

## Instrucciones para montar un contenedor __MySQL__.

> `docker volume create mysql_vol`

> `docker run -d --name some-mysql -v /home/sevenz01/docker/mysql_vol:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=7410 -d mysql`

> `docker exec -it some-mysql bash`

> `mysql -u root -p`

***Creación de Base de datos dentro del contenedor MySQL***

> create database dbTest

> use dbTest

> db.users.insert({"name":"Sergio"})

> db.users.find()

> exit
