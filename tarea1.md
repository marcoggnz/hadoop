# Tarea 1: Despliegue del Cluster y Realización de Consultas

Como aparece mencionado en el [README.md](README.md) esta tarea se ha realizado sobre una máquina virtual configurada en Cloudera.

## Ingestión de datos en Hadoop

Para empezar, se abre la línea de comandos y se crea un directorio nuevo con el nombre <code>dataset-hadoop</code> en la ruta <code>/home/cloudera/dh-course/dataset-hadoop</code>. A continuación, se copian los tres archivos que se van a utilizar a lo largo de la tarea desde el repositorio https://github.com/dgarciaesc/sample_dataset:

* <strong>movies.dat</strong>: contiene información sobre películas, incluyendo su ID, título, y género al que pertenecen. 
* <strong>users.dat</strong>: contiene información de los usuarios que publican calificaciones sobre las películas. Esta base de datos incluye el ID de cada usuario, género, edad, profesión, y código postal de residencia.
* <strong>ratings.dat</strong>: contiene información sobre el ID de usuario, ID de la película, la calificación y la variable timestamp.
> **Note:** La información detallada sobre la estructura y las variables de los 3 ficheros se encuentra explicada en [README.txt](https://github.com/marcoggnz/hadoop/blob/main/data/README.txt).

![image](https://github.com/user-attachments/assets/e89e96fd-6210-4970-aa5a-47dc18cd5ba4)

Se puede comprobar que los datos se encuentran almacenados correctamente mostrando el contenido de las primeras filas de cada archivo mostrando las primeras filas de cada archivo:

```
head -n 5 ratings.dat
head -n 5 users.dat
head -n 5 movies.dat
```

![image](https://github.com/user-attachments/assets/cb7fad58-26fd-4566-86e9-b7639b525b43)

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

Una vez dentro de Hive, se crea la base de datos:
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
    movie_id INT,
    title STRING,
    genres STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ("field.delim"="::");
```

Los tres archivos de datos tienen dos puntos (::) como delimitadores de columnas, por tanto, hay que tener esto en cuenta a la hora de crear las tablas.

A continuación, se debe mover el fichero <code> movies.dat</code> del sistema de ficheros local a HDFS mediante el comando:

```
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/movies.dat /user/cloudera/movies/
```

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

Esta tabla se encuentra en la ruta <code>/user/hive/warehouse/ex1.db</code>. A continuación, se copia el archivo que contiene los datos desde el directorio local al entorno HDFS:

```
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/users.dat /user/cloudera/users/
```

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
hdfs dfs -put /home/cloudera/dh-course/dataset-hadoop/ratings.dat /user/cloudera/ratings/
```

Para terminar la preparación, se deben cargar los datos en las tablas de Hive mediante los siguientes comandos:

```
LOAD DATA INPATH '/user/cloudera/movies/movies.dat' INTO TABLE movies;
LOAD DATA INPATH '/user/cloudera/users/users.dat' INTO TABLE users;
LOAD DATA INPATH '/user/cloudera/ratings/ratings.dat' INTO TABLE ratings;
```

Una simple consulta permite comprobar que los datos están guardados correctamente:

```
SELECT * FROM movies LIMIT 10;
SELECT * FROM users LIMIT 10;
SELECT * FROM ratings LIMIT 10;
```

![image](https://github.com/user-attachments/assets/2992fa5d-4763-42f8-bcf0-3824f0cc5e2e)

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
> **Note:** Para realizar esta consulta se han tenido en cuenta películas que han recibido al menos 100 valoraciones.

```
SELECT m.title, AVG(r.rating) AS avg_rating, COUNT(*) AS total_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
HAVING COUNT(*) >= 100
ORDER BY avg_rating DESC
LIMIT 3;
```

![image](https://github.com/user-attachments/assets/b58da9c8-c8a9-4ed0-8581-3db503e99f67)

Las 3 mejores películas (con al menos 100 valoraciones) según las calificaciones son Seven Samurai (The Magnificent Seven), The Shawshank Redemptiony The Godfather, con una valoración de 4.56, 4.55 y 4.52, respectivamente.

Consulta utilizada para obtener las 3 peores películas:
> **Note:** Para realizar esta consulta se han tenido en cuenta películas que han recibido al menos 100 valoraciones.

```
SELECT m.title, AVG(r.rating) AS avg_rating, COUNT(*) AS total_ratings
FROM ratings r
JOIN movies m ON r.movie_id = m.movie_id
GROUP BY m.title
HAVING COUNT(*) >= 100
ORDER BY avg_rating ASC
LIMIT 3;
```

![image](https://github.com/user-attachments/assets/26224197-977b-402a-a8d6-80d2a7fcbe60)

Las 3 peores películas (con al menos 100 valoraciones) según las calificaciones son Kazaam, Battlefield Earth y Pokemon the Movie, con una valoración de 1.47, 1.61, 1.62, respectivamente.

<strong>4. ¿Hay alguna profesión en la que deberíamos enfocar nuestros esfuerzos en publicidad? ¿Por qué?</strong>

Consulta utilizada:

```
SELECT u.occupation, COUNT(*) AS total_ratings
FROM ratings r
JOIN users u ON r.user_id = u.user_id
GROUP BY u.occupation
ORDER BY total_ratings DESC
LIMIT 1;
```

![image](https://github.com/user-attachments/assets/6d5d3f28-1a9d-44d5-b480-b687d71b9750)

La profesión con mayor número de valoraciones es la que se corresponde con el número 4. Si se consulta el archivo [README.txt](data/README.txt) se aprecia que es la profesión de "college/grad student".

<strong>5. Se te ocurre algún otro insight valioso que pudiéramos extraer de los datos procesados? Cómo?</strong>

Una consulta interesante sería saber la relación entre la edad del usuario y las puntuaciones. La consulta que se ha utilizado es:

```
SELECT u.age, AVG(r.rating) AS avg_rating
FROM ratings r
JOIN users u ON r.user_id = u.user_id
GROUP BY u.age
ORDER BY u.age;
```

![image](https://github.com/user-attachments/assets/3f22e729-2cd0-4527-9304-1bd0bf81419d)

Con estos resultados se puede llegar a las siguientes conclusiones:

| Edad  | Rating medio | Interpretación                                                |
|-------|--------------|---------------------------------------------------------------|
| <18   | 3.55         | Jóvenes valoran bien, aunque no destacan demasiado            |
| 18-24 | 3.51         | Segmento joven adulto es el más exigente (rating más bajo)    |
| 24-34 | 3.55         | Muy similar a los menores de 18                               |
| 35-44 | 3.62         | Aumenta la puntuación. Quizás menos críticos o gustos amplios |
| 44-49 | 3.64         | Más alta aún                                                  |
| 50-55 | 3.71         | Mucho más positivos                                           |
| >56   | 3.77         | Los más positivos de todos                                    |

### Ejercicio 2

<strong>El CEO está preocupado con la eficiencia de las queries usadas para extraer los datos de los ejercicios prácticos #1 y #2 y exige poder ver estos resultados desde una web.</strong>

<strong>1. Implementa, a través de Sqoop, una BBDD relacional en MySQL que contenga al menos los datos de uno de los insights extraídos en el ejercicio práctico #1</strong>

Para relizar este ejercicio se ha elegido el insight "Top 10 de usuarios más activos en puntuaciones de películas" del ejercicio 1.

Primero se vuelve a ejecutar la consulta guardándose el resultado en la tabla top_users:

```
CREATE TABLE top_users AS
SELECT user_id, COUNT(*) AS num_ratings
FROM ratings
GROUP BY user_id
ORDER BY num_ratings DESC
LIMIT 10;
```

Mediante el siguiente comando, se confirma que la tabla ha sido creada correctamente:

```
SELECT * FROM top_users;

```

![image](https://github.com/user-attachments/assets/cc99ed0d-bfbf-469c-bdbd-e5fee474edc6)

A continuación, se accede a MySQL con el siguiente comando:

```
mysql -uroot -pcloudera
```

Y se crea una nueva database con el nombre 'ex1' en MySQL:
```
CREATE DATABASE ex1;
```

![image](https://github.com/user-attachments/assets/41cb158e-a369-45d8-98b9-85ce4d6ec813)

Y se cambia el database al que se acaba de crear:

```
USE ex1;
```
 
Dentro de este database se crea la tabla top_users:

```
CREATE TABLE top_users (
  user_id INT,
  num_ratings INT
);
```

Por último, con la ayuda de sqoop se exporta la tabla desde Hive a MySQL ejecutando el siguiente comando en el sistema de archivos local:
 
```
sqoop export --connect jdbc:mysql://localhost/ex1 --table top_users --export-dir /user/hive/warehouse/ex1.db/top_users_export --input-fields-terminated-by '\t' --username root --password cloudera --num-mappers 1 
```

Una vez realizada la exportación, se verifican los resultados en MySQL:

![image](https://github.com/user-attachments/assets/264f054a-46ab-42d9-9940-e2df47164b61)

Ya estaría creada correctamente la tabla en MySQL. El siguiente paso sería programar una pequeña app en Python o Node.js para poder conectarse a MySQL y mostrar los resultados, o bien conectarse desde una herramienta de visualización como Grafana.

<strong>2. Por qué hacemos esto y no accedemos directamente a los resultados del ejercicio #1?</strong>

Existen varias la razones para pensar que la mejor opción es exportar los datos a MySQL si se quieren visualizar en una aplicación web. En la siguiente tabla se muestran las caracter´isticas principales de HIVE/HDFS y MySQL (RDBMS):

| Hive/HDFS  | MySQL/RDBMS                                                                                 |
|------------|---------------------------------------------------------------------------------------------|
| Diseñado para procesamiento batch en Big Data   | Diseñado para acceso rápido a datos estructurados      |
| Altísima latencia para pequeñas queries| 	Muy baja latencia para búsquedas rápidas                       |
| No apto para servir datos en tiempo real (web/API) | Ideal para servir resultados a una web o aplicación |
| Gran capacidad de almacenamiento | Excelente integración con frontends y APIs                            |
| No soporta bien updates/inserts frecuentes | Ideal para cambios dinámicos y transacciones                |

Por tanto, mientras que Hive es ideal para procesamiento y análisis masivo, MySQL se comporta de manera más eficiente cuando se tienen que realizar consultas rápidas y servir datos en una web en tiempo real.
