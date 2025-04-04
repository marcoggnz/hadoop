# Tarea 3: Estimación de Arquitecturas Hadoop para Distintos Casos de Uso

<strong>En la empresa están también pensando en conectar su plataforma Big Data con otras herramientas de la empresa y nos piden consejo sobre cómo podría integrarse/ejecutarse:
1. Herramienta de BI (p.ej.: Microstrategy)
2. Web de consultas sobre pedidos realizados
3. Generación de informes SQL usando R que se ejecutan mensualmente
4. Recopilación de información de redes sociales

Para cada una de estas tareas indica qué posibles herramientas del ecosistema Hadoop aplicarían por requisitos de casuística teniendo en cuenta las ventajas e inconvenientes de cada una de ellas (por ejemplo, uso de Impala consume mucha RAM).</strong>

##

## 1. Herramientas de Business Intelligence

Herramientas recomendadas: Hive (con HiveServer2 + JDBC/ODBC) e Impala.

| Herramienta | Ventajas                                                                                                            | Desventajas                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **Hive**    | - Gran compatibilidad (JDBC/ODBC)<br>- Optimizado para consultas analíticas grandes<br>- Bajo coste relativo en RAM | - Latencia alta para consultas interactivas (minutos)<br>- No óptimo para dashboards interactivos frecuentes |
| **Impala**  | - Consultas interactivas rápidas (segundos)<br>- Buena integración con herramientas BI modernas como MicroStrategy<br>- Experiencia interactiva | - Alto consumo de RAM<br>- Necesita recursos dedicados (alta memoria)<br>- Complejidad en gestión de clusters |

Por tanto, Impala se utilizaría para dashboards interactivos o tiempo real y Hive si las consultas no fueranen tiempo real o se de el caso de que haya que optimizar recursos económicos.

## 2. Web de consultas sobre pedidos realizados (uso interactivo)

Herramientas recomendadas: HBase, Apache Phoenix sobre HBase y Impala / Hive LLAP.

| Herramienta | Ventajas                                                                                                            | Desventajas                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **HBase**    |	- Consultas rápidas de tipo clave-valor (milisegundos)<br>- Ideal para consultas frecuentes con baja latencia<br>- Excelente escalabilidad horizontal | - No soporta consultas SQL directamente<br>- Difícil para consultas analíticas complejas |
| **Phoenix**  | - Permite SQL estándar sobre HBase<br>- Ideal para integrar fácilmente aplicaciones web | - Rendimiento ligeramente inferior al de HBase puro |
| **Impala o Hive LLAP**  | - Consultas interactivas SQL rápidas<br>- Ideal para consultas analíticas de complejidad media | - Mayor coste en RAM (Impala)<br>- Coste en memoria considerable y recursos (Hive LLAP)<br>- No ideal para consultas transaccionales individuales frecuentes |

Como conclusión, HBase y Phoenix se utilizarían para realizar consultas individuales rápidas y frecuentes; e Impala o Hive LLAP si las consultas involucran agregaciones y complejidad analítica media.

## 3. Generación de informes SQL mensuales usando R

Herramientasa recomendadas: Hive y SparkSQL.

| Herramienta | Ventajas                                                                                                            | Desventajas                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **Hive**    | - Excelente para consultas batch grandes (ideal mensuales)<br>- Integración fácil desde R mediante JDBC<br>- Poco consumo relativo de recursos comparado con herramientas en memoria | - Consultas lentas si la complejidad es muy alta (minutos u horas) |
| **Spark SQL**  | - Muy rápido al cargar datos en memoria<br>- Perfecto para integración directa con scripts R (p.ej. SparkR, sparklyr)<br>- Soporta gran variedad de funciones estadísticas | - Mayor consumo de memoria (RAM)<br>- Mayor complejidad operativa |

Hive permitiría la realización de informes estándar SQL de gran tamaño, que se realicen mensualmente y con baja urgencia; mientras que SparkSQL permitiría realizar los informes que requieran cálculosa complejos o algoritmos avanzados en R.

## 4. Recopilación de información de redes sociales (procesamiento en tiempo real o streaming)

Herramientas recomendadas: Apache Kafka, Apache Flume y Apache NiFi.

| Herramienta | Ventajas                                                                                                            | Desventajas                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **Apache Kafka**    |		- Excelente para ingestión de datos en tiempo real<br>- Muy escalable y robusto<br>- Compatible con múltiples fuentes (RRSS APIs) | - Necesita infraestructura adicional (clusters Kafka)<br>- Alta complejidad operativa |
| **Flume**  | 	- Muy fácil integración con HDFS, ideal logs/texto<br>- Configuración sencilla para datos básicos | - Menos flexible que Kafka<br>- No óptimo para grandes tasas de ingesta o gran escalabilidad |
| **NiFi**  | - Fácil uso visual (drag-and-drop)<br>- Excelente para integración rápida de fuentes de datos complejas (APIs redes sociales)<br>- Gestión de errores visual e intuitiva | - Escalabilidad algo inferior a Kafka en grandes volúmenes<br>- Menos eficiente en rendimiento extremo |

Por tanto, se utilizaría Apache Kafka si se necesita escalabilidad extrema y robustez; NiFi si se prioriza facilidad visual e integración rápida con APIs y procesamiento moderado; y Flume si solo se recopilan datos de texto simple/logs y se busca simplicidad absoluta (no recomendado para APIs complejas).

## Conclusiones

Como conclusión, Impala es la herramienta mejor posicionada para realizar tareas de BI permitiendo consultas interactivas de forma rápida; HBase y Phoenix permitirían son las mejores herramientas para realizar consultas en la wed de pedidos siendo ideales para integración de aplicaciones web; SparkSQL es mi opción para la realización de informes SQL mensuales utilizando R, debido a su rápida ejecución y su facilidad para integrarse con este lenguaje; y, por último, Kafka es la mejor herramienta para la recopilación de información en redes sociales gracias a su escalabilidad, robustez y rendimiento a la hora de garantizar tiempo real en las operaciones.

Con esta información MovieBuster podrá evaluar  qué herramientas elegir según la prioridad: rendimiento interactivo, escalabilidad, simplicidad o coste en recursos.
