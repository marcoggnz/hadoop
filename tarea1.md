# Tarea 1: Despliegue del Cluster y Realización de Consultas

Como aparece mencionado en el [README.md](README.md) esta tarea se ha realizado sobre una máquina virtual configurada en Cloudera.

## Ingestión de datos en Hadoop

Para empezar, se abre la línea de comandos y se crea un directorio nuevo con el nombre <code>dataset-hadoop</code> en la ruta <code>/home/cloudera/dh-course/dataset-hadoop</code>. A continuación, se copian los tres archivos que se van a utilizar a lo largo de la tarea desde el repositorio https://github.com/dgarciaesc/sample_dataset:

* movies.dat: contiene información sobre
* users.dat:
* ratings.dat: contiene información sobre el ID de usuario, ID de la película, la calificación y la variable timestamp.
> **Note:** La información detallada sobre la estructura y las variables de los 3 ficheros se encuentra explicada en [README.txt](https://github.com/marcoggnz/hadoop/blob/main/data/README.txt).

![image](https://github.com/user-attachments/assets/e89e96fd-6210-4970-aa5a-47dc18cd5ba4)

Una vez los achivos se encuentran en el directorio creado, se debe garantizar que no haya problemas de permisos durante el desarrollo de la tarea. Para ello se garantizan permisos de lectura, escritura y ejecución a todos estos ficheros mediante los comandos que aparecen a continuación:

```
chmod 777 movies.dat
chmod 777 ratings.dat
chmod 777 users.dat
```
## Creación de la Base de Datos

Una vez se ha preparado el entorno, se debe crear primero la base de datos en Hive. Para ello se accede primero al entorno Hive:

```
hive
```

Una vez dentro de Hive, se crea la base de datos mediante los siguientes comandos:
```
CREATE DATABASE ex1;
SHOW DATABASES;
USE ex1;
```

Esta base de datos se almacena en la ruta <code>/usr/hive/warehouse</code>. A continuación, se crean las tablas con los datos que se van a utilizar.

### Tabla Movies

Para la creación de la tabla de películas, se utilizan los siguientes comandos:

```
CREATE TABLE movies (
    MovieID INT,
    Title STRING,
    Genres STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

Los tres archivos de datos tienen dos puntos (::) como delimitadores de columnas, por tanto, hay que tener esto en cuenta a la hora de crear las tablas.

A continuación, se debe mover el fichero <code> movies.dat</code> del sistema de ficheros local a Hadoop mediante el comando:

```
!hadoop fs -put /home/cloudera/dh-course/dataset-hadoop/movies.dat /user/hive/warehouse/ex1.db/movies;
```

Se puede asegurar que la copia se ha realizado correctamente:

```
!dfs -ls /user/hive/warehouse/ex1.db/movies;
```

o realizando una simple consulta a esta tabla:

```
SELECT * FROM movies LIMIT 10;
```
### Tabla Users



### Tabla Ratings



### Ejercicio 1

...

### Ejercicio 2

...
