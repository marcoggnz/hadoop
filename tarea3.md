# Tarea 3: Estimación de Arquitecturas Hadoop para Distintos Casos de Uso

<strong>En la empresa están también pensando en conectar su plataforma Big Data con otras herramientas de la empresa y nos piden consejo sobre cómo podría integrarse/ejecutarse:
* Herramienta de BI (p.ej.: Microstrategy)
* Web de consultas sobre pedidos realizados
* Generación de informes SQL usando R que se ejecutan mensualmente
* Recopilación de información de redes sociales

Para cada una de estas tareas indica qué posibles herramientas del ecosistema Hadoop aplicarían por requisitos de casuística teniendo en cuenta las ventajas e inconvenientes de cada una de ellas (por ejemplo, uso de Impala consume mucha RAM).</strong>

##

## Herramientas de Business Intelligence

Las herramientas recomendadas son Hive (con HiveServer2 + JDBC/ODBC) e Impala.

| Herramienta | Ventajas                                                                                                            | Desventajas                                                        |
|-------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| **Hive**    | - Gran compatibilidad (JDBC/ODBC)<br>- Optimizado para consultas analíticas grandes<br>- Bajo coste relativo en RAM | - Latencia alta para consultas interactivas (minutos)<br>- No óptimo para dashboards interactivos frecuentes |
| **Impala**  | - Consultas interactivas rápidas (segundos)<br>- Buena integración con herramientas BI modernas como MicroStrategy<br>- Experiencia interactiva | - Alto consumo de RAM<br>- Necesita recursos dedicados (alta memoria)<br>- Complejidad en gestión de clusters |

Por tanto, Impala se utilizaría para dashboards interactivos o tiempo real y Hive si las consultas no fueranen tiempo real o se de el caso de que haya que optimizar recursos económicos.

## Web de consultas sobre pedidos realizados (uso interactivo)

Herramientas recomendadas: HBase, Apache Phoenix sobre HBase y Impala / Hive LLAP.

...

HBase + Phoenix para consultas individuales rápidas y frecuentes y Impala o Hive LLAP si las consultas involucran agregaciones y complejidad analítica media.

## eneración de informes SQL mensuales usando R

...

## Recopilación de información de redes sociales (procesamiento en tiempo real o streaming)

Herramientas recomendadas: Apache Kafka, Apache Flume y Apache NiFi.

...

Kafka si se necesita escalabilidad extrema y robustez, NiFi si se prioriza facilidad visual e integración rápida con APIs y procesamiento moderado, y Flume si solo se recopilan datos de texto simple/logs y se busca simplicidad absoluta (no recomendado para APIs complejas).

## Conclusiones

Con esto, MovieBuster podrá evaluar claramente qué herramientas elegir según la prioridad: rendimiento interactivo, escalabilidad, simplicidad o coste en recursos.
