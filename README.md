# 📡 Databricks Telco — Mini Proyecto

Mini proyecto desarrollado como parte del curso **Master en Azure Databricks & Spark para Data Engineers [A-Z]**.

---

## 🎯 Objetivo

Implementar un pipeline de datos end-to-end en **Azure Databricks** usando la arquitectura **Medallion (Bronze → Silver → Gold)**, aplicando **Unity Catalog** para la gobernanza y gestión de datos de una empresa de telecomunicaciones ficticia.

---

## 🏗️ Arquitectura

```
Fuente de Datos
      │
      ▼
┌─────────────┐
│   BRONZE    │  ← Tablas Externas (External Tables)
│  (Ingesta)  │     Datos crudos sin transformar
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   SILVER    │  ← Tablas Gestionadas (Managed Tables - Delta)
│ (Limpieza)  │     Datos validados y estandarizados
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    GOLD     │  ← Tablas Gestionadas (Managed Tables - Delta)
│  (Análisis) │     Datos listos para consumo / reportes
└─────────────┘
```

---

## 📂 Estructura del repositorio

```
Databricks_Telco/
│
├── databricks-course/
│   ├── unity-catalog-introduction/              # Notebooks introductorios de Unity Catalog
│   │   ├── 01.query_tables_with_unity_catalog.ipynb
│   │   └── 02.access_external_location.ipynb
│   │
│   └── unity-catalog-mini-project/             # Notebooks del mini proyecto Telco
│       ├── 01.create_external_locations.ipynb
│       ├── 02.create_catalog_and_schemas.ipynb
│       ├── 03.create_bronze_tables.ipynb
│       ├── 04.create_silver_tables.ipynb
│       └── 05.create_gold_tables.ipynb
│
└── README.md
```

---

## 🔧 Stack Tecnológico

| Componente | Descripción |
|---|---|
| **Azure Databricks** | Plataforma principal de procesamiento |
| **Apache Spark** | Motor distribuido de procesamiento |
| **Delta Lake** | Formato de almacenamiento con soporte ACID |
| **Unity Catalog** | Gobernanza y control de acceso a datos |
| **Azure Data Lake Storage Gen2** | Almacenamiento en la nube |

---

## 📋 Pasos implementados

1. **Crear External Locations** — Tres ubicaciones externas en Unity Catalog (`sadatabricksext_bronze/silver/gold`) apuntando a los contenedores de ADLS Gen2 usando Storage Credential
2. **Crear Catalog & Schemas** — Catálogo `telco_dev` (sin Managed Location) con tres schemas (`bronze`, `silver`, `gold`), cada uno con su Managed Location en ADLS Gen2
3. **Crear Tablas Bronze (External)** — Tablas externas sobre archivos CSV en ADLS:
   - `telco_dev.bronze.clientes` — `clienteId`, `nombre`, `paterno`, `materno`, `documentoIdentidad`, `telefono`, `email`, `direccion`
   - `telco_dev.bronze.detalles` — `detalleId`, `clienteId`, `servicio`, `plan`, `precioMensual`, `estado`, `fechaInicio`, `fechaFin`
4. **Crear Tablas Silver (Managed Delta)** — Transformación sobre Bronze: renombre de columnas a snake_case, concatenación de nombre completo y agregado de `ingestion_date`
5. **Crear Tabla Gold (Managed Delta)** — `telco_dev.gold.ingreso_por_cliente`: JOIN entre `silver.clientes` y `silver.detalles`, sumando `precio_mensual` por cliente activo (`estado = true`)

---

## 📊 Datos del proyecto

El dataset simula información de una empresa de telecomunicaciones. Archivos fuente en formato CSV almacenados en ADLS Gen2:

| Archivo | Descripción |
|---|---|
| `clientes.csv` | Datos personales del cliente: id, nombre, documento, contacto, dirección |
| `detalles.csv` | Servicios contratados: tipo de servicio, plan, precio mensual, estado, fechas de vigencia |

La tabla resultante en Gold — `ingreso_por_cliente` — agrega el ingreso mensual total por cliente activo, ordenado de mayor a menor.

---
