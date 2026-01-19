# DockerCompose
M0375B4A6-dockerComposeFinal
# Prácticas: DockerComposeFinal 
**Alumno:** [Mossab Ech Chantoufy]
**Asignatura:** Serveis de xarxa i Internet



## 1. Lo que he aprendido sobre Docker 

En esta práctica me he dado cuenta de que los contenedores son básicamente como unas cajas ligeras donde metemos nuestra aplicación con todo lo que necesita para funcionar. A diferencia de las máquinas virtuales antiguas, que son muy pesadas porque tenían un sistema operativo completo dentro, los contenedores son mucho más rápidos porque comparten el núcleo del sistema de mi ordenador, pero a la vez están aislados.

Investigando un poco, vi que antes existían los LXC, que eran como máquinas virtuales ligeras. La diferencia es que Docker se centra más en la aplicación en sí (un contenedor para la web, otro para la base de datos), mientras que LXC intentaba imitar un sistema operativo entero. Además, Docker me ha parecido mucho más fácil de transportar de un sitio a otro.

Una duda que tenía al principio era distinguir entre **imagen** y **contenedor**. La mejor forma de explicarlo es con una receta de cocina: la **imagen** es la receta (el papel con las instrucciones, que no se puede comer), y el **contenedor** es el pastel que cocino siguiendo esa receta. La imagen está quieta, y el contenedor es lo que está vivo y funcionando.

También he aprendido una lección importante sobre los **datos**: si borro un contenedor, todo lo que hay dentro desaparece para siempre. Por eso es fundamental usar lo que llamamos Volúmenes, que es como conectar una carpeta de mi ordenador real al contenedor para que los datos (como las fotos de las zapatillas o los usuarios) se guarden a salvo en mi disco duro.

Las **ventajas** que le veo a esto son claras:
1. Si funciona en mi ordenador, va a funcionar en el de la profesora.
2. Gasta mucha menos memoria que una máquina virtual.
3. Puedo tener la web, la base de datos y el gestor separados sin que se peleen entre ellos.

Por lo que he visto, con Docker podemos montar de todo: páginas web, bases de datos, aplicaciones de móvil, inteligencia artificial... Aunque Docker es el más famoso, he leído que hay otros como **Podman** o **Kubernetes**, que sirve para controlar muchos contenedores a la vez.



## 2. Cómo he desplegado la aplicación Footlocker 

Para levantar el proyectod del año pasado de footlocker con el quim, he seguido estos pasos:

Primero, lo más importante fue llevarme los archivos de mi Windows a la máquina Ubuntu. Usé el comando `scp` para pasarlos por la red, porque es mucho más rápido que crearlos uno a uno. Una vez allí, organicé todo en una carpeta llamada `footlocker` y le di permisos a todo para no tener problemas de acceso.

Después, tuve que configurar **Nginx** (el servidor web). Creé un archivo de configuración para decirle dónde estaba la carpeta de mi web y, muy importante, decirle que si alguien pide un archivo `.php`, se lo envíe al contenedor de PHP para que lo procese.

La pieza clave de todo fue el archivo **`docker-compose.yml`**. Es como el plano del arquitecto. Ahí definí los cuatro servicios que necesitaba:
* Un servidor web (Nginx) en el puerto 90.
* El motor de PHP para que funcione el código.
* La base de datos MySQL en el puerto 3307.
* Y el PhpMyAdmin en el puerto 8090 para poder ver la base de datos de forma visual.
<img width="944" height="639" alt="image" src="https://github.com/user-attachments/assets/9b5ac356-fee3-4f41-8ec9-3cad3233d8f8" />


Al final, solo tuve que lanzar un comando (`sudo docker-compose up -d`) y todo se puso en marcha. Tuve que entrar un momento a la base de datos para crear la tabla de productos y conectarla con mi código PHP.



## 3. Problemas que me encontré y cómo los solucioné (Incidencias)

No todo salió a la primera, tuve varios problemas que tuve que investigar y arreglar:

**El problema del "driver" fantasma**
Al principio, la web me daba un error fatal diciendo que no encontraba la función `mysqli_connect`. Resulta que el contenedor de PHP viene sin nada y sin extras.
* **Solución:** Tuve que entrar dentro del contenedor con la terminal y instalar manualmente la extensión `mysqli` para que pudiera hablar con la base de datos.
<img width="932" height="234" alt="image" src="https://github.com/user-attachments/assets/731e2d22-4488-4b7e-8cb4-5cd1b8aab197" />

**El error de "localhost"**
Mi código PHP intentaba conectarse a "localhost", pero no funcionaba. Me di cuenta de que en Docker, "localhost" significa "yo mismo".
* **Solución:** Cambié el archivo de conexión y puse el nombre del contenedor (`footlocker_db`). Aprendí que Docker tiene un DNS interno que resuelve esos nombres mágicamente.
<img width="620" height="352" alt="image" src="https://github.com/user-attachments/assets/7fa0dd27-c577-4603-bb1d-28de6586ff88" />

**Nginx no encontraba mis archivos (Error 403)**
Me salía un error de "Forbidden". Al revisar, vi que tenía una carpeta dentro de otra carpeta (`footlocker/footlocker`) y Nginx estaba buscando en la primera, que estaba vacía.
* **Solución:** Tuve que arreglar las rutas en el archivo `docker-compose.yml` para que apuntaran a la subcarpeta correcta donde realmente estaba el `index.php`.
<img width="512" height="186" alt="image" src="https://github.com/user-attachments/assets/2af407e0-327a-4b0e-b42e-9f3266fc45fd" />

**El fallo al reiniciar Docker**
Cuando intenté arreglar las carpetas y reiniciar, Docker se quedó bloqueado con un error raro (`KeyError`). Era un fallo de la versión antigua de Docker Compose.
* **Solución:** Tuve que borrar los contenedores a la fuerza usando sus IDs y volver a levantarlos de cero. Ahí aprendí que a veces es mejor "limpiar y empezar de nuevo" que intentar arreglar algo que está atascado.


## 4. Detalles Técnicos de los archivos

Para terminar, explico brevemente qué hace cada archivo importante de mi práctica:

* **Dockerfile:** Aunque he usado imágenes ya hechas, sé que este archivo es como la receta para crear una imagen desde cero, diciendo qué sistema operativo usa y qué programas instala.
<img width="477" height="288" alt="image" src="https://github.com/user-attachments/assets/923253a8-99b1-4dba-b6a6-11f8d93b33ff" />

* **Docker-compose.yaml:** Es mi archivo favorito. Aquí es donde digo "quiero un Nginx, un PHP y un MySQL" y cómo se conectan entre ellos. Es mucho mejor que lanzar comandos sueltos por terminal.
<img width="713" height="637" alt="image" src="https://github.com/user-attachments/assets/b8ce23c8-2ef5-4f66-95f4-bd2ea7166a9e" />
<img width="594" height="329" alt="image" src="https://github.com/user-attachments/assets/da1a4936-d0f8-4393-8fc0-e806d86c441d" />

* **Default.conf:** Aquí está la gran diferencia con una instalación normal. En lugar de conectar a una IP fija, conecto al nombre del servicio (`fastcgi_pass footlocker_php:9000`). Esto hace que mi proyecto sea súper flexible.
<img width="774" height="437" alt="image" src="https://github.com/user-attachments/assets/1ac1bebb-cedc-4e71-9c75-a0ed5d530caa" />
