# Tarea 2: Dimensionamiento del Clúster de Hadoop

<strong>El CEO está muy contento con el trabajo realizado y quiere apostar aún más por tecnologías Big Data. Está pensando en montar otra infraestructura Hadoop que procese eventos de películas provenientes de distintas fuentes (cines, plataformas de streaming, etc..) y necesita estimación del tamaño plataforma teniendo en cuenta que:

| Fuente    | Eventos diarios | Tamaño por Evento |
|-----------|-----------------|-------------------|
| Fuente 1  | 10,000          | 15 KB             |
| Fuente 2  | 120,000         | 300 Bytes         |
| Fuente 3  | 150,000         | 100 KB            |
| Fuente 4  | 170,000         | 800 KB            |
| Fuente 5  | 2,000           | 1500 KB           |

Las características de las maquinas es que son capaces de tener hasta 22 discos de 2 TB para almacenamiento cada una. Indicar el número de máquinas necesarias para poder almacenar todo el volumen de datos durante el próximo año, así como la justificación de por qué se necesita dicha capacidad para un clúster Hadoop.</strong>

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

| Fuente    | Volumen Diario de Datos                          |
|-----------|--------------------------------------------------|
| Fuente 1  | 10,000 * 15 KB = 150,000 KB = 146.48 MB          |
| Fuente 2  | 120,000 * 300 Bytes = 36,000,000 B = 34.33 MB    |
| Fuente 3  | 150,000 * 100 KB = 15,000,000 KB = 14,648.44 MB  |
| Fuente 4  | 170,000 * 800 KB = 136,000,000 KB = 132,812.5 MB |
| Fuente 5  | 2,000 * 1500 KB = 3,000,000 KB = 2,929.69 MB     |
> **Note:** Para realizar la conversión de tamaño de datos se ha utilizado el sistema binario (donde 1 MB equivale a 1,024 KB) más común en entornos técnicos y especialmente en Hadoop.

Total diario = 146.48 MB + 34.33 MB + 14,648.44 MB + 132,812.5 MB + 2,929.69 MB = 150,571.44 MB = 147.7 GB/día

Como el consumo diario aproximado es de 147.7 GB, el consumo anual será de:
147.7 GB/día * 365 días = 53,900.5 GB = 53.9 TB/año

Como Hadoop HDFS tiene un factor de replicación por defecto de 3, es decir, cada bloque de datos se almacena 3 veces para tolerancia de fallor, el volumen total con replicación será el triple. El valomen real con replicación es de 161.7 TB.

Cada máquina tiene 22 discos dfe 2 TB, o lo que es lo mismo, existen 44 TB disponibles por máquina. Sin embargo, no toda la capacidad se puede destinar al almacenamiento de datos. Normalmente se reserva parte del disco para el sistema operativo, logs y/ buffers. Suponiendo que se destina el 90% de la capacidad para almacenamiento, la capacidad disponible por máquina es de 39.8 TB.

Dividiendo el volumen total entre la capacidad útil por máquina de obtiene el número teórico de máquinas necesarias:
161.7 TB / 39.6 TB ≈ 4.08

Como debemos ser conservadores y, por tanto, redondea hacia arriba para asegurarnos de que se cumplen las necesidades, el número de máquinas necesarias es 5. Estas máquinas irían destinadas a almacenamiento de datos, por tanto, se tratarían de DataNodes. Sin embargo, sería necesario contar con una máquina adicional como NameNode, siendo necesario, por tanto, <strong>6 máquinas</strong> para garantizar alta disponibilidad. De esta forma se cumple con el almacenamiento del primer año.

## Conclusiones
Hadoop no solo almacena los datos, también los procesa distribuida y paralelamente. El factor de replicación garantiza tolerancia a fallos, pero, por otro lado, triplica el almacenamiento necesario. Además, se requiere espacio adicional para archivos temporales, resultados intermedios, logs, y otros procesos de MapReduce. Tener varias máquinas permite escalar el procesamiento también en paralelo, siendo clave para una analítica eficiente.

En el caso de haber hecho los cálculos mediante el primer método, considerando un factor de compresión de 0.5 (los datos se reducen a la mitad), un factor de replicación de 3 y una reserva de HDFS de 0.2, serían necesarias mínimo 3 máquinas, sin embargo, debemos ser conservadores en este primer diseño y optar por garantizar la arquitectura. Después del primer año, se podría volver a revisar esta aproximación y dimensionar de nuevo el clúster.
