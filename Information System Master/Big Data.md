
# Essentials Concepts

- Business Intelligence (BI) : analyze only structor data and save ir in central storage. This method is with a past object  with statistics and report to fix errors.
- Business Analytics (BA) :
	- Analiza datos estructurados.
	- Enfoque hacia el futuro. 
	- Emplea modelos predictivos y prescriptivos, estudiando tendencias para no cometer los errores del pasado.
- Big Data : 
	- Procesa datos estructurados, semiestructurados y no estructurados.
	- Procesamiento en tiempo real y por lotes.
	- Se almacena la informacion en sistemas distribuidos.

## Cadena de valor de Big Data
- **Identificacion e Ingesta** : Implica detectar las fuentes (generadas por maquinas o personas) y conectarse para recopilar la informacion. El flujo de entrada puede orquestarse por lotes(Batch), micro lotes o flujo continuo(Streaming).

- **Preprocesamiento** : es un paso crucial que incluye filtrar el ruido, transformar formatos incompatibles y limpiar datos inexactos o incompletos para asegurar la calidad antes del analisis.
- **Almacenamiento** : debido a los enormes volumenes se abandonan las bases de datos relacionales tradicionales en favor de sistemas distribuidos (como Hadoop) o bases de datos NoSQL.
> hadoop y nosql son las unicas bases de datos que se usan?
- **Analisis y Exposicion** : se aplican analiticas para encontrar patrones ocultos y correlaciones. Finalmente, los resultados se visualizan mediante tableros de control interactivos o se exponen para el consumo de otras aplicaciones a traves de APIs.


## Data Lake
- **Raw Data** : Se almacena el nivel de detalle mas pequeno y granular posible. Mantener los datos sin resumir maximiza la capacidad de extraer nuevos insights en el futuro.
- **Inmutabilidad** : Los datos nunca se actualizan ni se eliminan; simplemente se agrega una nueva version.
- **Datos Eternamente Verdaderos** : como consecuencia de la inmutabilidad, cada dato registrado incluye una marca de tiempo. Un dato que fue verdad en un segundo especifico, sera verdad a perpetuidad para ese instante historico.
## Paradigmas de Procesamiento

- **Batch (Procesamientos por Lotes)** : agrupa grandes conjuntos de datos y los procesa en intervalos de tiempo espaciados. Es altamente escalable pero tiene una alta latencia, ideal para inforamcion historica estatica.
- **Streaming (Flujo Continuo)**  : procesa los datos a medida que se reciben( en el orden de milisegundos a segundos). Ofrece baja latencia casi en tiempo real, aunque las consultas pueden basarse en datos parciales.
> NOTA: de la combinacion de estos flujos nacen las arquitecturas hibridas de datos modernas, como las arquitecturas Lambda y Kappa.

Datawarehouse y data marts son repositorios de informacion. 

El analisis predictivo se refiere a las probabilidades de prediccion empleadas para definir tendencias a futuras. Utiliza modelos de aprendizaje supervisados, no supervisados y semisupervisado.

El analisis prescriptivo se aplica para predecir eventos futuros e impulsar decisiones proactivas fuera de los limites de la interaccion humana,


# Second Section

## Sistemas Distribuidos, escabilidad y distribucion de los datos

- Objetivo de un sistema distribuido es facilitar a los usuarios el acceso a los recursos remotos y compartirlos de manera controlada y eficiente, lo que permite abaratar costos, colaborar e intercambiar informacion.
- Un sistema distribuido consta de componentes autonomos.
### Transparencia en la distribucion

**Tipos**


| Transparencia | Descripcion |
| ------------- | ----------- |
| Acceso        |             |
| Ubicacion     |             |
| Migracion     |             |
| Reubicacion   |             |
| Replicacion   |             |
| Concurrencia  |             |
| Falla         |             |

Transparencia de Acceso : se encarga de esconder las diferencias en la representacion de los datos y la manera en que el usuario accede a dichos recursos.
Transparencia de ubicacion : no poder determinar la ubicacion fisica de los datos.
Transparencia de migracion : por hacer una migracion de los datos, sin que el usuario lo persiba.
Transparencia de reubicacion : poder reubicar los recursos mientras siguen en uso.
Transparencia de replicacion : incrementa la disponibilidad o mejora el rendimiento.
Transparencia de Concurrencia : por compartir el mismo recurso

### Arquitecturas de los sistemas distribuidos


**Arquitectura de  3 Capas**
- El nivel de interfaz de usuario
- El nivel de procesamiento o middleware
- El nivel de datos(Servidor de base datos)

- Cluster de Servidores.
### Arquitecturas descentralizadas
Sistemas P2P
- alta escalabilidad
- Tolerancia a fallas
- Descentralizacion
- Privacidad
- Equilibrio de carga
Desventajas:
- Alta complejidad
- Control
- Seguridad
- Trafico
### Escalabilidad y distribucion de los datos.
- Software
- Hardware
Se puede escalar de la dos maneras:

- Vertical : Aumentar la capacidad de recursos o componentes internos para incrementar su rendimiento.
- Horizontal : Es la cualidad que tiene un componente de cooperar con componentes de su misma naturaleza y de esta manera incrementar el rendimiento de la tarea que se esta realizando. Importante Balanceador.

### Distribucion de los datos - Shared Nothing

- Shared Nothing: solo se comparte la comunicacion entre ellos para distribuirse la carga.
### Tecnicas de distribucion de los datos
- Fragmentacion o Sharding : es el particionamiento de los datos en porciones mas pequenas para distribuirlos entre varios nodos.
	- Fragmentacion horizontal el particionamiento se hace por registros o filas
	- Fragmentacion vertical el particionamiento se hace por atributos o columnas
	- Mixta se combinan las dos anteriores.
	Beneficios:
	- Mayor concurrencia
	- Paralelizacion de consultas
	- Distribucion de carga
- Replicacion : es el proceso de copiar y mantener actualizados los datos en varios nodos del sistema.
	Beneficios:
	- Mejora de la disponibilidad y accesibilidad de los datos
	- Mejora del rendimiento
	- Recuperacion de fallos
	- Reduccion de carga
	Desventajas:
	- Costo
	- Consistencia
	- Sobrecarga
	- Fiabilidad
	**Tipos de Replicacion**
	Segun la cantidad de datos replicados:
	- Replicacion Total
	- Replicacion Parcial
	Segun el momento
	- Sincrona
	- Asincrona
	Segun el modelo de replicacion:
	- Maestro / esclavo


# Hadoop

Es un framework!

Modulos de Hadoop:
## HDFS Commom
Consiste en la coleccion de bibliotecas y utilidades comunes que soportan los otros modulos para la ejecucion del framework
## HDFS
Este componente es el sistema de archivos distribuidos que esta disenado para almacenar de forma fiable grandes conjuntos de datos y proporciona acceso de alto rendimiento a los datos de aplicacion de usuario

**HDFS** trabaja de la sigueinte manera:
- Al recibir un archivo, lo divide en bloques de 64 o 120 MB, de acuerdo a la configuracion previa.
- Esos bloques son almacenados de manera distribuida en los nodos del cluster con un factor de replicacion de 3
- Cuando se lee el archivo, se recupera leyendo cada bloque de alguna de sus replicas.
Caracteristicas:
- Almacenamiento
- Procesamiento distribuido
- Tolerancia a fallos y disponibilidad
- Localidad de datos
![[Pasted image 20260404230946.png]]
## MapReduce

Es un modelo de programacion que permite escribir con facilidad, aplicaciones que procesan en paralelo grandes cantidades de datos, tanto estructurados como no estructurados, en muchos nodos.

Funciones:

- Mapper
- Reducer
	Fases Primarias:
	- Shuffle
	- Sort
	- Secondary Sort
	- Reduce
	- Partitioner
	- Counter
![[Pasted image 20260404225844.png]]
## YARN (YET ANOTHER RESOURCE NEGOTIATOR)

 Es un sistema de gestion de clusteres.
 Se ouede describir como un gestor de recursos redisenado
 YARN desacopla las capacidades de gestion de recursos y planificacion de MapReduce del componente de procesamiento de datos, permitiendo a Hadoop soportar enfoques mas variados de procesamiento y una gama mas amplia de aplicaciones.

# Ecosistema

![[Pasted image 20260404230259.png]]

Para **Ingestion de datos**, algunas herramientas mas usadas son:
- **Scoop** Permite ejecutar app MapReduce que introducen o extraen informacion de bases de datos SQL. Datos estructurados
- **Flume** sirve para introducir datos en Streaming en Hadoop. Datos SemiEstructurados y no estructurados.
- **Kafka** tiene como objetivo proporcionar una plataforma unificada, de alto rendimiento y de baja latencia, para la manipulación en tiempo real de fuentes de datos. Puede verse como una cola de mensajes, bajo el patrón publicación-suscripción, masivamente escalable y concebida como un registro de transacciones distribuidas.
- **Storm** : componente encargado de procesar flujos de datos en tiempo real. 

Para la coordinacion se tiene, **Zookeper**
Es un servicio de coordinacion para aplicaciones distribuidas, responsable de mantener la informacion de configuracion y ofrecer coordinacion de manera distribuida.

Para el almacenamiento se tienen las bases de datos:
- **HBASE** tiene un motor de procesamiento en memoria que le agiliza enormemente las operaciones de lectura-escritura sobre hadoop, permitiendo asi trabajar con datos en streaming.
- **HCatalog** utiliza los metadatos de Hive. puede accedera los datos en el estandar HDFS o HBase.
- **Impala** es una bd sql sobre hadoop. Proporciona la posibilidad de realizar consultas recurrentes y de baja latencia para analitica y bi.

Para el procesamiento se tiene:
- **HIVE**  proporciona la interfaz sql a hadoop para convertirse en un data warehouse.
- **PIG** Convierte una descripcion de alto nivel de como deben ser procesados los datos en jobs de MapReduce.
- **Spark** motor de procesamiento en memoria compatible con HDFS. Aumenta la velocidad de MapReduce en 100 veces. Soporta aplicaciones ETL, Machine learning y streaming de datos, así como consultas SQL.
- **Tez** framework de programación de flujos de datos. Es la evolución de MapReduce que ejecuta sobre Yarn, optimizando el código para alcanzar mejoras de hasta 10 veces en el rendimiento. Muchas tecnologías están adoptando Tez como motor de ejecución principal.
- **Hue** (Hadoop User Experience): es la interfaz web para la gestión de Hadoop. Permite crear tablas en Hive, realizar consultas, navegar el sistema de archivos, cambiar permisos y propietarios. También puede diseñar jobs de MapReduce y conocer su estado. Además, posibilita llevar a cabo la gestión por parte de los administradores de las cuentas de usuario.

Para la planificacion se cuenta con:
- **Oozie** : es un gestor de workflow. Te permite definir cuándo quieres que tus jobs MapReduce se ejecuten, ya sea de forma programada o cuando haya disponibles nuevos datos
> Se puede usar **Airflow**.

Para la administracion tenemos:
- **Apache Ambari** es un framework de código abierto para el aprovisionamiento, la gestión y la supervisión de clústeres Hadoop. Es útil para instalar servicios Hadoop a través de diferentes nodos del clúster y gestionar la configuración de Hadoop Services en el clúster.
