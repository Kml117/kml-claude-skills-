---
name: data-engineering-pipelines
description: Guía operativa para ingeniería de datos — adquisición, ETL/ELT, orquestación, almacenamiento y Big Data. Úsala cuando el usuario trabaje con Apache Spark, Airflow, dbt, Kafka, Airbyte o Scrapy; diseñe pipelines de ingesta; elija entre Data Warehouse, Data Lake o Lakehouse; o necesite decidir entre procesamiento batch vs streaming. Aplica también ante preguntas sobre Docker/Terraform para datos, formatos de tabla abiertos (Iceberg) o la arquitectura "Modern Data Stack".
---

# Ingeniería de Datos: Adquisición, Transporte y Almacenamiento

## Cuándo se usa esta skill
Todo lo que pasa **antes** de que un científico de datos o un modelo toque los datos: cómo se obtienen, se mueven, se transforman y se almacenan.

*(Si la pregunta es sobre fundamentos de SQL en sí — JOINs, funciones de ventana, CTEs — eso vive en `data-science-foundations`. Aquí se asume ese SQL como dado y se enfoca en dónde y cómo se ejecuta a escala.)*

## ⚠️ Vigencia de este contenido
Las afirmaciones de consolidación de mercado envejecen rápido. Verifica antes de citar como vigente:
- El estado de la fusión Fivetran + dbt Labs y si sigue reflejando la estructura del mercado
- Si Apache Iceberg sigue siendo el formato de tabla abierto dominante o si surgió un competidor relevante

## Mapa de decisión de framework

| Necesidad | Framework | Nota clave |
|---|---|---|
| Procesamiento distribuido masivo (cientos de GB a petabytes) | **Apache Spark** | No lo uses para archivos <10 GB — la sobrecarga de clúster supera al beneficio; usa DuckDB en su lugar |
| Orquestación de pipelines (programar, monitorear, reintentar) | **Apache Airflow** | Airflow **no** es un motor de transferencia de datos — no muevas millones de registros por XCom, solo coordina |
| Transformación SQL dentro del warehouse (la T de ELT) | **dbt** | Solo escribe SELECT; dbt se encarga del CREATE TABLE/VIEW. No lo uses para extraer datos ni para streaming |
| Ingesta de eventos en tiempo real, arquitecturas event-driven | **Apache Kafka** | Registro inmutable distribuido; no es una base de datos de consulta ad-hoc |
| Conectar APIs/SaaS al warehouse sin código custom | **Airbyte** | >10k conectores; no hace transformaciones complejas en vuelo — eso va en dbt después |
| Web scraping masivo, concurrente y tolerante a fallos | **Scrapy** | Asíncrono (Twisted); para sitios con JS pesado necesita integración con navegador headless |

## Combinaciones ganadoras

**"Modern Data Stack" (ELT cloud)**: `Airbyte` (extrae y carga) → `Snowflake/BigQuery` (almacena) → `dbt` (transforma) → `Airflow` (orquesta todo). Es la arquitectura dominante en empresas medianas 2024-2026.

**"Lakehouse Medallion" (Big Data)**: `Spark` procesa archivos en capas Bronce→Plata→Oro; `Apache Iceberg` da transacciones ACID sobre Parquet en el lago; `Unity Catalog` gobierna accesos. Para petabytes.

**"Cómputo local híbrido" (presupuesto ajustado)**: Datos en `S3` como Parquet → `DuckDB` los lee y transforma localmente sin warehouse activo → `Airflow` orquesta. Para datasets que caben en RAM de una instancia.

## Anti-patrones

- **Spark para archivos pequeños**: la latencia de arranque del clúster supera al tiempo de procesamiento — usa DuckDB.
- **dbt para streaming**: dbt compila SQL batch contra el warehouse; forzarlo a correr cada minuto sobre Kafka genera costos masivos y bloqueos de concurrencia.
- **Airflow como motor de datos**: mover millones de registros por variables XCom entre tareas satura la base de metadatos del scheduler y causa OOM.

## Glosario mínimo
- **ETL vs ELT**: ETL transforma antes de cargar (área intermedia); ELT carga crudo al warehouse y transforma allí (patrón moderno, aprovecha el motor analítico cloud).
- **Idempotencia**: ejecutar el mismo pipeline con los mismos parámetros N veces produce el mismo resultado — diseña todas las tareas así.
- **DAG**: grafo dirigido acíclico que define el orden de ejecución de tareas sin ciclos.
- **Lakehouse**: fusión de lago de datos (flexible, barato) + warehouse (ACID, gobernado); Iceberg/Delta Lake son las capas que lo habilitan.
- **Formato de tabla abierto (Iceberg)**: metadatos sobre archivos Parquet que añaden transacciones, time travel y evolución de esquema sin vendor lock-in.

## Tendencia 2024-2026
Apache Iceberg se consolidó como el formato abierto dominante. Hadoop/MapReduce y Hive son tecnologías legadas. El foco se desplaza de acumular datos batch a garantizar **frescura del dato** para alimentar agentes de IA en tiempo real. La fusión Fivetran + dbt Labs refleja la consolidación del mercado hacia stacks unificados.
