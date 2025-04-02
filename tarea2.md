# Tarea 2: Dimensionamiento del Clúster de Hadoop

<strong>El CEO está muy contento con el trabajo realizado y quiere apostar aún más por tecnologías Big Data. Está pensando en montar otra infraestructura Hadoop que procese eventos de películas provenientes de distintas fuentes (cines, plataformas de streaming, etc..) y necesita estimación del tamaño plataforma teniendo en cuenta que:

| Fuente    | Eventos diarios | Tamaño por Evento |
|-----------|-----------------|-------------------|
| Fuente 1  | 10,000          | 15 KB             |
| Fuente 2  | 120,000         | 300 Bytes         |
| Fuente 3  | 150,000         | 100 KB            |
| Fuente 4  | 170,000         | 800 KB            |
| Fuente 5  | 2,000           | 1500 KB           |

Las características de las maquinas es que son capaces de tener hasta 22 discos de 2 TB para almacenamiento cada una. Indicar el número de máquinas necesarias para poder almacenar todo el volumen de datos durante el próximo año, así como la justificación de porque se necesita dicha capacidad para un clúster Hadoop.</strong>

##

Existen varios métodos para calcular el número de máquinas necesarias para almacenar datos en un cluster de Hadoop. Los dos métodos principales son los siguientes:

## Cálculo mediante fórmula considerando compresión y reserva de espacio
Este método utiliza la fórmula <code>H = c * r * S / (1 - i)</code> siendo el significado de las variables el siguiente:

| Variable  | Significado                                                                                              |
|-----------|----------------------------------------------------------------------------------------------------------|
| H  | Capacidad total del clúster Hadoop necesaria (en TB, por ejemplo)                                               |
| c  | Compresión de los datos (valor entre 0 y 1, donde 0.5 significa que se reduce a la mitad)                       |
| r  | Factor de replicación de Hadoop (por defecto, 3)                                                                |
| S  | Volumen original de datos (sin replicar ni comprimir)                                                           |
| i  | Porcentaje de espacio que se reserva en HDFS para mantenimiento y funcionamiento (normalmente se usa 0.2 o 20%) |

Características de este método:
* Considera compresión
* Considera reserva de espacio en HDFS
* Menos conservador
* Menor coste estimado
* Recomendado para un diseño técnico detallado

## Cálculo directo sin compresión
Este método está basado en el volumen bruto de datos con replicación. Sus características son:
* No considera compresión
* No considera reserva de espacio en HDFS
* Es más conservador
* El coste estimado es mayor
* Recomandado para una fase inicial o de incertidumbre

Como en este caso se trata de una primera aproximación al diseño de un clúster en Hadoop y además se desconocen datos sobre la compresión o la reserva de espacio HDFS, se ha utilizado este segundo método para calcular las máquinas que harían falta.

Multiplicando el número de eventos diarios y el tamaño por evento se obtiene el volumen diario de datos:

| Fuente    | Volumen Diario de Datos |
|-----------|-----------------|------------------------------|
| Fuente 1  | 10,000 * 15 KB = 150,000 KB                    |
| Fuente 2  | 120,000 * 300 Bytes = 36,000,000 B = 36,000 KB |
| Fuente 3  | 150,000 * 100 KB = 15,000,000 KB               |
| Fuente 4  | 170,000 * 800 KB =           |
| Fuente 5  | 2,000 * 1500 KB = 3,000,000 KB                 |

