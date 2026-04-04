# UNIVERSIDAD CATÓLICA ANDRÉS BELLO
## Dirección General de los Estudios de Postgrado
### Área de Ingeniería
#### Maestría en Sistemas de Información

---

# ANTEPROYECTO DE TRABAJO DE GRADO

**Título:**
> *Propuesta de un Sistema de Información basado en Ingeniería de Datos para la Estimación de la Calidad de Experiencia (QoE) mediante la correlación de indicadores de señalización, consumo y comportamiento de uso en operadoras de telecomunicaciones móviles*

---

| | |
|---|---|
| **Autor:** | [Tu nombre completo] |
| **Tutor Propuesto:** | [Nombre del tutor] |
| **Fecha:** | Febrero, 2026 |
| **Ciudad:** | Caracas, Venezuela |

---

## 1. PLANTEAMIENTO DEL PROBLEMA

### 1.1 Contexto

Las operadoras de telecomunicaciones móviles operan en un entorno altamente competitivo donde la retención de clientes depende directamente de la experiencia percibida por el usuario durante el uso de los servicios de datos. Esta experiencia, conocida como Calidad de Experiencia (Quality of Experience, QoE), representa un concepto multidimensional que va más allá de los indicadores técnicos tradicionales de red, incorporando la percepción subjetiva del usuario sobre la calidad del servicio recibido.

A nivel global, las operadoras de telecomunicaciones generan volúmenes masivos de datos operacionales de manera continua. Entre estos se encuentran los registros de eventos de señalización generados por los nodos de la red core (SGSN/MME), los registros de detalle de llamadas (CDR) de facturación que capturan el consumo de datos por suscriptor, y los CDR de servicios que reflejan el comportamiento de uso de aplicaciones específicas como redes sociales, mensajería y servicios de streaming. Sin embargo, en la práctica, estos datos son procesados de manera aislada y con fines exclusivamente operativos o de facturación, desaprovechando su potencial para generar inteligencia sobre la experiencia real del usuario.

### 1.2 Descripción del Problema

En la actualidad, la mayoría de las operadoras de telecomunicaciones móviles en Venezuela y en la región latinoamericana carecen de un sistema integrado que permita correlacionar las diferentes fuentes de datos operacionales disponibles para estimar la Calidad de Experiencia del suscriptor. Esta situación genera una brecha significativa entre los indicadores técnicos de red que se monitorean actualmente (KPIs de señalización y facturación) y la experiencia real percibida por el usuario final.

Como consecuencia de esta limitación, la toma de decisiones en los diferentes niveles organizacionales de la operadora (operacional, táctico y estratégico) se realiza de manera reactiva, basada en indicadores técnicos aislados que no reflejan fielmente el impacto sobre la experiencia del cliente. Esto dificulta la priorización de inversiones en red, la detección temprana de degradaciones que afectan la experiencia de usuario, y la identificación de segmentos de clientes en riesgo de abandono (churn).

La evidencia empírica y la literatura académica (ITU-T P.10/G.100, 3GPP TS 22.261) demuestran que existe una correlación estadísticamente significativa entre los indicadores de señalización del plano de control (tasas de éxito de Attach, Handover, Paging, CSFALLBACK, SRVCC), los patrones de consumo de datos (volumen de descarga/carga por suscriptor) y el comportamiento de uso de aplicaciones (tipo y diversidad de servicios consumidos), con el nivel de QoE experimentado por el usuario. Sin embargo, esta correlación no ha sido formalizada ni implementada en un sistema de información que permita su aprovechamiento operativo y gerencial en el contexto de las operadoras venezolanas.

### 1.3 Formulación del Problema

¿De qué manera un Sistema de Información basado en Ingeniería de Datos puede integrar y correlacionar los indicadores de señalización, consumo y comportamiento de uso disponibles en una operadora de telecomunicaciones móviles para estimar la Calidad de Experiencia (QoE) del suscriptor y apoyar la toma de decisiones en los niveles operacional, táctico y estratégico de la organización?

---

## 2. OBJETIVOS DE LA INVESTIGACIÓN

### 2.1 Objetivo General

Proponer un Sistema de Información basado en Ingeniería de Datos para la estimación de la Calidad de Experiencia (QoE) mediante la correlación de indicadores de señalización, consumo y comportamiento de uso en operadoras de telecomunicaciones móviles, orientado a la toma de decisiones en los niveles operacional, táctico y estratégico de la organización.

### 2.2 Objetivos Específicos

1. **Analizar** el estado del arte de los modelos de estimación de QoE basados en datos de red y comportamiento de usuario en el contexto de telecomunicaciones móviles.

2. **Identificar** y caracterizar los indicadores de señalización, consumo y comportamiento de uso disponibles en la infraestructura de datos de una operadora de telecomunicaciones móviles que sean predictores válidos de QoE.

3. **Diseñar** la arquitectura de un pipeline de Ingeniería de Datos que integre las múltiples fuentes de datos operacionales (logs de señalización SGSN/MME, CDR de facturación y CDR de servicios) para el procesamiento y correlación de indicadores de QoE.

4. **Desarrollar** un modelo de estimación de QoE basado en la correlación multicapa de indicadores de señalización, consumo y comportamiento de uso, sustentado en estándares internacionales (ITU-T, 3GPP).

5. **Implementar** un prototipo funcional del Sistema de Información propuesto, incluyendo dashboards diferenciados por nivel organizacional (operacional, táctico y estratégico) que soporten la toma de decisiones.

6. **Evaluar** el desempeño del modelo de estimación de QoE propuesto mediante la validación con datos operacionales reales anonimizados de una operadora de telecomunicaciones móviles.

---

## 3. JUSTIFICACIÓN DE LA INVESTIGACIÓN

### 3.1 Justificación Teórica

Esta investigación contribuye al cuerpo de conocimiento existente sobre estimación de QoE en redes móviles, aportando un modelo de correlación multicapa que integra simultáneamente tres tipos de datos operacionales: señalización de control (plano de señalización), registros de consumo (CDR de facturación) y registros de comportamiento de uso (CDR de servicios). A diferencia de los modelos existentes que típicamente utilizan una sola fuente de datos, el enfoque multicapa propuesto permite una estimación más robusta y contextualizada de la experiencia del usuario, contribuyendo al avance del conocimiento en el área de Sistemas de Información aplicados a telecomunicaciones.

### 3.2 Justificación Práctica

Desde el punto de vista práctico, la implementación del sistema propuesto permitirá a las operadoras de telecomunicaciones:

- **Nivel Operacional:** Detectar proactivamente degradaciones de experiencia de usuario en tiempo real, reduciendo el tiempo medio de detección y resolución de problemas que afectan la QoE.
- **Nivel Táctico:** Identificar segmentos de clientes con QoE deteriorada, zonas geográficas críticas y períodos de alta degradación, facilitando la priorización de acciones correctivas y la gestión del riesgo de churn.
- **Nivel Estratégico:** Proveer indicadores de QoE agregados que sustenten decisiones de inversión en infraestructura de red, estrategias de retención de clientes y posicionamiento competitivo.

### 3.3 Justificación Metodológica

El presente trabajo propone una metodología replicable para la estimación de QoE a partir de datos operacionales existentes en cualquier operadora de telecomunicaciones móviles, sin requerir inversión adicional en sistemas de sonda o medición activa. Esto representa un aporte metodológico significativo, especialmente relevante para operadoras en mercados emergentes con restricciones presupuestarias.

### 3.4 Relevancia para la Maestría en Sistemas de Información

Este trabajo integra de manera directa las áreas de conocimiento del programa de la Maestría en Sistemas de Información de la UCAB, incluyendo: Big Data, Ingeniería de Datos, Cloud Computing, Data Warehouse, Business Intelligence y Ciencia de Datos aplicada, constituyendo un caso de estudio integral que demuestra la aplicación práctica de estas disciplinas en un contexto industrial real.

---

## 4. ALCANCE Y LIMITACIONES

### 4.1 Alcance

- El sistema propuesto se diseñará e implementará como prototipo funcional sobre datos operacionales reales anonimizados de una operadora de telecomunicaciones móviles en Venezuela.
- El modelo de estimación de QoE cubrirá los servicios de datos móviles (internet móvil), incluyendo la correlación con servicios de aplicaciones OTT (Instagram, WhatsApp, entre otros).
- El pipeline de datos será diseñado sobre tecnologías de procesamiento distribuido (Apache Spark) con capacidad de escalamiento para entornos de producción.
- Los dashboards desarrollados cubrirán los tres niveles organizacionales: operacional, táctico y estratégico.
- La validación del modelo se realizará mediante análisis estadístico de correlación entre los indicadores propuestos y métricas de comportamiento de usuario como proxy de QoE.

### 4.2 Limitaciones

- El estudio estará delimitado a los datos disponibles en los sistemas de la operadora colaboradora, sujeto a los acuerdos de confidencialidad y anonimización correspondientes.
- No se realizarán mediciones directas de QoE subjetivo mediante encuestas o pruebas con usuarios, por lo que el modelo se basará en estimación indirecta (inferencia) a partir de indicadores objetivos.
- El alcance técnico se limitará a redes 4G/LTE (SGSN-MME), sin incluir en esta fase la señalización de redes 5G.
- La implementación será un prototipo de validación, no un sistema de producción certificado.

---

## 5. MARCO TEÓRICO REFERENCIAL

### 5.1 Calidad de Experiencia (QoE) en Telecomunicaciones Móviles

La Calidad de Experiencia (QoE) es definida por la ITU-T (P.10/G.100) como "el grado de deleite o molestia del usuario de una aplicación o servicio", representando una medida subjetiva que integra factores técnicos, de contenido, contextuales y expectativas del usuario. A diferencia de la Calidad de Servicio (QoS), que mide parámetros técnicos objetivos de la red (throughput, latencia, jitter, packet loss), la QoE incorpora la percepción humana como elemento central de evaluación.

### 5.2 Estimación de QoE a partir de Datos de Red

La literatura académica ha demostrado la viabilidad de estimar QoE a partir de indicadores de red objetivos, enfoque conocido como "Network-based QoE Estimation" o "QoE Inference from Operational Data". Este enfoque es especialmente relevante cuando no se dispone de mediciones directas de satisfacción del usuario, siendo ampliamente referenciado en publicaciones del IEEE, ITU-T y 3GPP.

### 5.3 Indicadores de Señalización como Predictores de QoE

Los eventos del plano de control de la red móvil (Attach, Handover, Paging, CSFALLBACK, SRVCC) constituyen indicadores validados de la capacidad de la red para proveer servicio continuo y de calidad al usuario. Las tasas de éxito/fallo de estos procedimientos, junto con los códigos de causa de fallo y tiempos de procesamiento, permiten inferir el nivel de accesibilidad, retención y continuidad del servicio experimentado por el suscriptor.

### 5.4 CDR como Fuente de Inteligencia de Comportamiento

Los registros de detalle (CDR) de facturación y servicios representan una fuente de datos de comportamiento de usuario ampliamente disponible en las operadoras. La correlación entre el volumen de consumo de datos, los patrones de uso de aplicaciones específicas y la evolución temporal de estos indicadores permite inferir cambios en la calidad de la experiencia del usuario de manera indirecta pero estadísticamente válida.

### 5.5 Ingeniería de Datos y Procesamiento Distribuido

La Ingeniería de Datos comprende el conjunto de disciplinas, procesos y tecnologías orientados al diseño, construcción y mantenimiento de sistemas de recolección, almacenamiento y procesamiento de datos a escala. Apache Spark constituye el framework de procesamiento distribuido de referencia para el procesamiento de grandes volúmenes de datos operacionales, habilitando el análisis de los datos a la escala generada por una operadora de telecomunicaciones.

### 5.6 Business Intelligence e Inteligencia de Negocios

El Business Intelligence (BI) comprende el conjunto de estrategias, tecnologías y herramientas utilizadas para la transformación de datos operacionales en información relevante para la toma de decisiones empresariales. En el contexto de telecomunicaciones, un sistema de BI orientado a QoE permite conectar los indicadores técnicos de red con el impacto en la experiencia del cliente y los resultados del negocio.

---

## 6. MARCO METODOLÓGICO

### 6.1 Tipo y Diseño de la Investigación

La presente investigación es de tipo **aplicada**, con diseño **no experimental** de carácter **descriptivo-correlacional**. Se enmarca en el paradigma cuantitativo, utilizando datos operacionales reales para el diseño, implementación y validación del sistema propuesto.

### 6.2 Enfoque Metodológico

Se adoptará una metodología de desarrollo de sistemas en fases iterativas, combinando:

- **Revisión documental:** Análisis del estado del arte en modelos de estimación de QoE.
- **Análisis de datos:** Exploración y caracterización de las fuentes de datos disponibles.
- **Diseño de sistema:** Arquitectura del pipeline de Ingeniería de Datos y modelo de estimación.
- **Implementación:** Desarrollo del prototipo funcional.
- **Validación:** Evaluación estadística del modelo propuesto.

### 6.3 Fuentes de Datos

| Fuente | Descripción | Variables Clave |
|--------|-------------|-----------------|
| Logs de Señalización SGSN/MME | Eventos del plano de control de la red 4G/LTE | Tipo de evento, resultado (éxito/fallo), código de causa, duración, timestamp |
| CDR de Facturación | Registros de consumo de datos por suscriptor | Identificador de suscriptor (anonimizado), volumen descarga, volumen carga, timestamp |
| CDR de Servicios | Registros de uso de aplicaciones OTT por suscriptor | Identificador de suscriptor (anonimizado), tipo de servicio/aplicación, timestamp |

### 6.4 Arquitectura Técnica Propuesta

El sistema se implementará sobre la siguiente pila tecnológica:

- **Ingesta y Procesamiento:** Apache Spark / PySpark
- **Orquestación:** Apache Airflow
- **Almacenamiento:** PostgreSQL / Data Warehouse en Cloud
- **Desarrollo:** Python (pandas, numpy, pyspark)
- **Contenerización:** Docker / Docker Compose
- **Visualización:** Power BI / Tableau / Python Dash
- **Infraestructura:** Cloud Computing (AWS / GCP)

### 6.5 Modelo de Estimación de QoE Propuesto

El modelo integrará cuatro dimensiones de indicadores:

1. **Indicador de Accesibilidad** (señalización): Tasa de éxito de Attach y procedimientos de registro
2. **Indicador de Retención** (señalización): Tasa de éxito de Handover, SRVCC y CSFALLBACK
3. **Indicador de Consumo** (CDR facturación): Volumen y tendencia de consumo de datos
4. **Indicador de Engagement** (CDR servicios): Diversidad y tipo de aplicaciones utilizadas

La combinación ponderada de estos cuatro indicadores generará un **Score de QoE estimado** por suscriptor, zona geográfica y período temporal, clasificado en niveles (Alto / Medio / Bajo / Crítico).

### 6.6 Técnicas de Análisis

- Análisis exploratorio de datos (EDA)
- Análisis de correlación estadística (Pearson, Spearman)
- Clustering de suscriptores por perfil de QoE (K-means)
- Series de tiempo para detección de degradaciones
- Validación cruzada del modelo de estimación

---

## 7. PLAN DE TRABAJO Y CRONOGRAMA TENTATIVO

| Fase | Actividades | Duración Estimada |
|------|-------------|-------------------|
| **Fase 1:** Revisión Bibliográfica | Revisión de literatura sobre QoE, modelos de estimación, Ingeniería de Datos en telecomunicaciones | 4 semanas |
| **Fase 2:** Análisis de Datos | Exploración y caracterización de las tres fuentes de datos disponibles | 3 semanas |
| **Fase 3:** Diseño del Sistema | Arquitectura del pipeline ETL y modelo de estimación de QoE | 4 semanas |
| **Fase 4:** Implementación | Desarrollo del prototipo: pipeline, modelo y dashboards | 8 semanas |
| **Fase 5:** Validación | Evaluación estadística del modelo y ajustes | 3 semanas |
| **Fase 6:** Documentación | Redacción del trabajo de grado | 6 semanas |
| **Fase 7:** Presentación | Preparación y defensa ante jurado | 2 semanas |
| **TOTAL** | | **~30 semanas** |

---

## 8. CONTRIBUCIONES ESPERADAS

### 8.1 Contribución Académica

- Modelo de estimación de QoE multicapa basado en correlación de indicadores de señalización, consumo y comportamiento de uso, validado con datos reales de una operadora venezolana.
- Marco metodológico replicable para la implementación de sistemas de estimación de QoE en operadoras con restricciones de acceso a datos de experiencia directa.

### 8.2 Contribución Práctica

- Prototipo funcional de Sistema de Información con dashboards diferenciados por nivel organizacional.
- Modelo de Score de QoE estimado aplicable a decisiones operacionales, tácticas y estratégicas.
- Identificación de indicadores de alerta temprana de degradación de experiencia de usuario.

### 8.3 Contribución al Sector de Telecomunicaciones en Venezuela

- Caso de estudio aplicable al contexto de las operadoras venezolanas y latinoamericanas.
- Metodología de bajo costo de implementación (basada en datos operacionales existentes).
- Modelo de inteligencia de negocios orientado a la mejora de la experiencia del cliente.

---

## 9. REFERENCIAS BIBLIOGRÁFICAS PRELIMINARES

- ITU-T Recommendation P.10/G.100 (2017). *Vocabulary for performance, quality of service and quality of experience*. International Telecommunication Union.

- 3GPP TS 22.261 (2023). *Service requirements for the 5G system*. 3rd Generation Partnership Project.

- Barakovic, S., & Skorin-Kapov, L. (2013). Survey and challenges of QoE management issues in wireless networks. *Journal of Computer Networks and Communications*.

- Hoßfeld, T., Schatz, R., & Egger, S. (2012). SOS: The MOS is not enough! *Quality of Multimedia Experience (QoMEX)*.

- Khorsandroo, S., Tostes Gomes, R. L., & de Oliveira Albuquerque, R. (2019). Time series trends to predict mobile network QoE degradation. *IEEE Access*.

- Zaharia, M., et al. (2016). Apache Spark: A unified engine for big data processing. *Communications of the ACM*, 59(11), 56-65.

- Kimball, R., & Ross, M. (2013). *The Data Warehouse Toolkit: The Definitive Guide to Dimensional Modeling*. Wiley.

> *Nota: Esta lista bibliográfica es preliminar y será ampliada durante la fase de revisión bibliográfica del trabajo de grado.*

---

## 10. DATOS DEL AUTOR Y TUTOR

### Autor

| Campo | Información |
|-------|-------------|
| Nombre completo | [Tu nombre completo] |
| Cédula de identidad | V-[Tu CI] |
| Correo electrónico | [Tu correo] |
| Teléfono | [Tu teléfono] |
| Empresa actual | [Nombre de la operadora] |
| Cargo actual | Especialista en Packet Core |
| Años de experiencia en telecomunicaciones | [X años] |

### Tutor Propuesto

| Campo | Información |
|-------|-------------|
| Nombre completo | [Nombre del tutor] |
| Cédula de identidad | V-[CI del tutor] |
| Correo electrónico | [Correo del tutor] |
| Institución | UCAB / [Empresa] |
| Especialidad | [Área de especialidad] |

---

*Caracas, Febrero de 2026*

---

> **Nota del autor:** Este anteproyecto está sujeto a revisión y ajustes según las observaciones del tutor asignado y la Coordinación de la Maestría en Sistemas de Información de la UCAB.
