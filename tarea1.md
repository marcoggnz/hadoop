# Tarea 1: Despliegue del Cluster y Realización de Consultas

Como aparece mencionado en el [README.md](README.md) esta tarea se ha realizado sobre una máquina virtual configurada en Cloudera.

## Ingestión de datos en Hadoop

Para empezar, se abre la línea de comandos y se crea un directorio nuevo con el nombre <code>dataset-hadoop</code> en la ruta <code>/home/cloudera/dh-course/dataset-hadoop</code>. A continuación, se copian los tres archivos que se van a utilizar a lo largo de la tarea desde el repositorio https://github.com/dgarciaesc/sample_dataset:

* movies.dat: contiene información sobre
* users.dat:
* ratings.dat: contiene información sobre el ID de usuario, ID de la película, la calificación y la variable timestamp.
> **Note:** La información detallada sobre la estructura y las variables de los 3 ficheros se encuentra explicada en [README.txt](https://github.com/marcoggnz/hadoop/blob/main/data/README.txt).

![image](https://github.com/user-attachments/assets/e89e96fd-6210-4970-aa5a-47dc18cd5ba4)

Se puede comprobar que los datos se encuentran almacenados correctamente mostrando el contenido de las primeras filas de cada archivo mediante los comandos:

```
head -n 5 ratings.dat
head -n 5 users.dat
head -n 5 movies.dat
```

![image](https://github.com/user-attachments/assets/cf042dee-ada7-4601-8f9d-5bdc1eddaf7a)


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
CREATE DATABASE IF NOT EXIST ex1;
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

![image](https://github.com/user-attachments/assets/02a256f2-bfd0-4a74-99f0-a911269b1eec)

### Tabla Users

De forma similar a como se ha creado la tabla de películas se crea la tabla de usuarios:

```
CREATE TABLE users (
    user_id INT,
    gender STRING,
    age INT,
    occupation INT,
    zip_code STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

Esta tabla se encuentra en la ruta <code>/user/hive/warehouse/ex1.db</code>. A continuación, se copia el archivo que contiene los datosa del entorno Linux al entorno Hive:

```
!hadoop fs -put /home/cloudera/dh-course/dataset-exercise1/users.dat /user/hive/warehouse/exercise1.db/users;
```

Mediante una consulta se asegura que los datos están almacenados correctamente en esta tabla:

```
SELECT * FROM users LIMIT 10;
```

![image](https://github.com/user-attachments/assets/ad5000d7-bd11-445b-b3e1-11aa85635704)

### Tabla Ratings

Por último, se crea la tercera de las tablas (ratings) con el siguiente comando:

```
CREATE TABLE ratings (
    user_id INT,
    movie_id INT,
    rating INT,
    timestamp INT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

Esta tabla se almacena en la misma ruta que el resto de tablas <code>/user/hive/warehouse/ex1.db</code>. Una vez creada esta tabla, se cargan los datos del fichero que contiene las calificaciones desde el entorno local al entorno Hive:

```
!hadoop fs -put /home/cloudera/dh-course/dataset-exercise1/ratings.dat /user/hive/warehouse/exercise1.db/ratings;
```

Una simple consulta permite comprobar que los datos están guardados correctamente:

```
SELECT * FROM ratings LIMIT 10;
```

![image](https://github.com/user-attachments/assets/846b8c68-b854-4c38-9d8e-48e2e82e09c0)


### Ejercicio 1

<strong>De cara a definir por qué genero apostar, identificar los influencers que pueden potenciar el marketing de MovieBuster y definir una estrategia de publicidad, el CEO te pide averiguar los siguientes datos del momento de mercado actúal:</strong>

<strong>1. ¿Cuál es la película con más opiniones?</strong>

Consulta utilizada:

```
SELECT m.title, COUNT(*) AS num_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
ORDER BY num_ratings DESC
LIMIT 1;
```

![image](https://github.com/user-attachments/assets/aa41c802-b03c-45ae-9fe5-d652de82be58)

La película con mayor número de opiniones es American Beauty con un total de 3428 opiniones recibidas.

<strong>2. ¿Qué 10 usuarios son los más activos a la hora de puntuar películas?</strong>

Consulta utilizada:

```
SELECT user_id, COUNT(*) AS total_reviews
FROM ratings
GROUP BY user_id
ORDER BY total_reviews DESC
LIMIT 10;
```

![image](https://github.com/user-attachments/assets/e8d142b1-e2bd-44ed-97bb-75b2b75e5f1b)

Los 10 ID de usuario que han publicado mayor número de reviews son 4169, 1680, 4277, 1941, 1181, 889, 3618, 2063, 1150 y 1015. 

<strong>3. ¿Cuáles son las tres mejores películas según los scores? Y las tres peores?</strong>

Consulta utilizada para obtener las 3 mejores películas:

```
SELECT m.movie_id, m.title, m.genres, AVG(r.rating) AS avg_rating
FROM movies m
JOIN ratings r ON m.movie_id = r.movie_id
GROUP BY m.movie_id, m.title, m.genres
ORDER BY avg_rating DESC
LIMIT 3;
```



Las 3 mejores películas según las calificaciones son Follow the Bitch, Smashing Time y One Little Indian, todas ellas con una valoración de 5.0.

Consulta utilizada para obtener las 3 peores películas:

```
SELECT m.MovieID, m.Title, AVG(r.Rating) AS avg_rating
FROM movies m
JOIN ratings r ON m.MovieID = r.MovieID
GROUP BY m.MovieID, m.Title
ORDER BY avg_rating ASC
LIMIT 3;
```



Las 3 peores películas según las calificaciones son Hillbillys in a Haunted House, Elstree Calling y Little Indian, Big City, todas ellas con una valoración de 1.0.

<strong>4. ¿Hay alguna profesión en la que deberíamos enfocar nuestros esfuerzos en publicidad? ¿Por qué?</strong>

Consulta utilizada:

```
SELECT u.Occupation, COUNT(r.UserID) AS num_ratings
FROM users u
JOIN ratings r ON u.UserID = r.UserID
GROUP BY u.Occupation
ORDER BY num_ratings DESC;
```



<strong>5. Se te ocurre algún otro insight valioso que pudiéramos extraer de los datos procesados? Cómo?</strong>

Consulta utilizada:

```
...
```

### Ejercicio 2

<strong>El CEO está preocupado con la eficiencia de las queries usadas para extraer los datos de los ejercicios prácticos #1 y #2 y exige poder ver estos resultados desde una web.</strong>

...

<strong>1. Implementa, a través de Sqoop, una BBDD relacional en MySQL que contenga al menos los datos de uno de los insights extraídos en el ejercicio práctico #1</strong>

...

<strong>2. Por qué hacemos esto y no accedemos directamente a los resultados del ejercicio #1?</strong>

...
