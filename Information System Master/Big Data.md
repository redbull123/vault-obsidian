
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