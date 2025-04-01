# Desarrollo de la práctica

## Planteamiento de Negocio

Como titulado de Datahack, has sido recientemente contratado en MovieBuster, una startup de reciente fundación que pretende revolucionar el mundo del ocio digital, empezando por la manera en la que se oferta ocio audiovisual a los clientes.
El CEO de MovieBuster te ha encomendado el nada fácil desafío de crear el departamento de analítica desde cero, este departamento tendrá como función principal la extracción y análisis de los datos necesarios para generar insights que permitan a la mesa ejecutiva la definición de la estrategia de MovieBuster. Como primera tarea, y de cara a decidir el Roadmap de proyectos que serán realizados durante los próximos meses, el CEO te encarga un análisis pormenorizado del mercado actual de películas, para, con los datos en la mano, poder decidir cuál va a ser la estrategia a seguir.

## Parte 1 (práctica)

...

### Ejercicio 1

...

### Ejercicio 2

...

## Parte 2 (teoría)

El CEO está muy contento con el trabajo realizado y quiere apostar aún más por tecnologías Big Data. Está pensando en montar otra infraestructura Hadoop que procese eventos de películas provenientes de distintas fuentes (cines, plataformas de streaming, etc..) y necesita estimación del tamaño plataforma teniendo en cuenta que:

| Source    | Daily Events | Event Size |
|-----------|--------------|------------|
| Source 1  | 10,000       | 15 KB      |
| Source 2  | 120,000      | 300 Bytes  |
| Source 3  | 150,000      | 100 KB     |
| Source 4  | 170,000      | 800 KB     |
| Source 5  | 2,000        | 1500 KB    |

Las características de las maquinas es que son capaces de tener hasta 22 discos de 2 Teras para almacenamiento cada una. Indicar el número de máquinas necesarias para poder almacenar todo el volumen de datos durante el próximo año, así como la justificación de porque se necesita dicha capacidad para un clúster Hadoop.

...

## Parte 3 (teoría)

En la empresa están también pensando en conectar su plataforma Big Data con otras herramientas de la empresa y nos piden consejo sobre cómo podría integrarse/ejecutarse:
* Herramienta de BI (p.ej.: Microstrategy)
* Web de consultas sobre pedidos realizados
* Generación de informes SQL usando R que se ejecutan mensualmente
* Recopilación de información de redes sociales

Para cada una de estas tareas indica qué posibles herramientas del ecosistema Hadoop aplicarían por requisitos de casuística teniendo en cuenta las ventajas e inconvenientes de cada una de ellas (por ejemplo, uso de Impala consume mucha RAM).
