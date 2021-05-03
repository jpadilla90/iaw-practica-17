# iaw-practica-17
Balanceo de carga con HAProxy

> IES Celia Viñas (Almería) - Curso 2020/2021
Módulo: IAW - Implantación de Aplicaciones Web
Ciclo: CFGS Administración de Sistemas Informáticos en Red

**Introducción**
------------
En esta práctica vamos a modificar los archivos docker-compose.yml que hemos creado en las prácticas 15 y 16, y vamos a incluir un nuevo contenedor Docker con HAProxy para balancear la carga de los contenedores que ejecutan la aplicación web.

Posteriormente deberá realizar la implantación de ambos sitios web en Amazon Web Services (AWS) haciendo uso de contenedores Docker y de la herramienta Docker Compose.

**1.1 Como escalar los servicios definidos en un archivo docker-compose.yml**
------------
El comando sería el siguiente:

`docker-compose up --scale SERVICE=NUM`
Donde
`SERVICE` es el nombre del servicio.
`NUM` es el número de instancias que queremos tener de ese servicio.

Ejemplo:

En el siguiente ejemplo estaríamos iniciando todos los servicios que están definidos en el archivo docker-compose.yml y para el servicio de wordpress estaríamos creando 4 instancias.

`docker-compose up --scale wordpress=4`

**1.2 Ejemplo de un archivo docker-compose.yml con un balanceador de carga**
------------
A continuación se muestra un fragmento de un archivo ``docker-compose.yml`` que incluye un servicio de balanceo de carga con HAProxy que nos puede servir de ejemplo:

```yaml
...
services:
  lb:
    image: dockercloud/haproxy <1>
    ports:
      - 80:80 <2>
      - 1936:1936 <3>
    links:
      - apache <4>
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock <5>

  apache:
    ...
...
```

<1> Utilizaremos la imagen dockercloud/haproxy que está disponible en Docker Hub.

<2> El puerto 80 será el puerto del servicio que queremos balancear.

<3> El puerto 1936 nos permite acceder a una página web con información estadística del balanceador.

<4> Creamos un enlace con el servicio que queremos balancear. Los enlaces permiten que los contenedores se descubran entre sí y transfieran de manera segura información sobre un contenedor a otro contenedor.

<5> Es necesario montar el socket UNIX del Docker daemon (/var/run/docker.sock) para que el contenedor lb pueda comunicarse con el Docker daemon y obtener información del resto de contenedores.


`Importante:` Tenga en cuenta que la imagen oficial de **MySQL** está preparada para permitirnos importar un script SQL con la base de datos inicial de nuestra aplicación. Para importar la base de datos de la aplicación web puede crear un volumen de tipo `bind mount` entre el directorio de su máquina local donde está el script SQL y el directorio `/docker-entrypoint-initdb.d` de la imagen oficial de MySQL. Esto hará que la primera vez que se instancie la base de datos leerá todos los archivos con extensión .sql que estén en este directorio y se ejecutarán.

A continuación se muestra un ejemplo de cómo tendría que hacerlo en el archivo `docker-compose.yml:`

```yaml
...
    volumes:
      - mysql_data:/var/lib/mysql
      - ./sql:/docker-entrypoint-initdb.d 
...
```

**1.3 Archivo de configuración ``haproxy.cfg``**
------------
El archivo de configuración que utiliza HAProxy es ``haproxy.cfg.``

Localice el archivo haproxy.cfg dentro del contenedor que ejecuta el balanceo de carga y estudie los parámetros de configuración que está utilizando.

**Archivos en el repositorio**
------------
1. **README.md** Documentación.
2. **** 


**Referencias**
------------
- Guía original para la práctica.
https://josejuansanchez.org/iaw/practica-17/index.html
- Practica 15: Instalación de WordPress usando contenedores Docker y Docker Compose.
https://github.com/japsasir/iaw-practica-15
- Practica 16: 'Dockerizar' aplicación LAMP
https://github.com/josejuansanchez/iaw-practica-lamp
-Introdución a HAProxy y balanceo de carga
https://www.digitalocean.com/community/tutorials/an-introduction-to-haproxy-and-load-balancing-concepts
-HAProxy: Manual de configuración.
http://cbonte.github.io/haproxy-dconv/2.2/configuration.html


**Editor Markdown**
------------
- Markdown editor. Alternativamente, investigar atajos de teclado como Ctrl+B= bold (negrita) 
https://markdown-editor.github.io/

