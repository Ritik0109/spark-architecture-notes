# Databricks Study Notes

---

## Core Topics

### 1. Lakeflow DLT Pipelines
- SQL, Python

### 2. Lakeflow Jobs
- Notebooks, activities, settings

### 3. Lakeflow Connections
- Managed, external

---

## Other Topics

### Databricks Utility Commands
### Magic Commands / Language Magic Commands
### Handling JSON in Spark SQL
- Reading, parsing, explode, schema handling

### SQL UDFs
### Photon Accelerator

---

## CI/CD (SWE Best Practices for DataOps)

### CI
- Coding
- Unit testing
- Data quality checks
- Code review

### CD
- Environments
- Automated deployments
- DAB
- REST API deployment
- YAML files / JSON files

---

## Architecture & Platform Topics

### Medallion Architecture
### Cluster Management
### Unity Catalog

---

### Time Travel

| Command | Description |
|---|---|
| `DESCRIBE HISTORY <table>` | View history of a table |
| `RESTORE TABLE <table> TO VERSION AS OF X` | Restore to a specific version |

- Query or restore previous versions of a table
- Requires Delta logs (JSON) and Parquet files with data

---

### VACUUM

- Deletes historical Delta table Parquet data files **only**
- Default deletion threshold: older than **7 days / 168 hrs**

```sql
VACUUM <table> [RETAIN 0 HOURS] [DRY RUN]
SET spark.databricks.delta.retentionDurationCheck.enabled = False
ALTER TABLE <table> SET TBLPROPERTIES ('<param>' = '<value>')
```

---

### OPTIMIZE

#### Auto Optimize *(not for Unity Catalog)*

| Feature | Description | Command |
|---|---|---|
| Optimize Writes | Writes into optimal file sizes after job completes | `SET spark.databricks.delta.properties.defaults.autoOptimize.optimizeWrite = True` |
| Auto-Compaction | Auto-optimizes file size once files ≥ 50 | `SET spark.databricks.delta.properties.defaults.autoOptimize.autoCompact = True` |

#### Predictive Optimization *(Unity Catalog)*

#### Manual Optimize

```sql
OPTIMIZE <table>
```

- Manually fixes the small file problem

---

### REORG

---

### Advanced Optimizations

#### 32-Column Default Concept

#### Partitioning

- Best for **low cardinality** columns with equal data distribution
- Best for table size **> 1TB**, partition size **≥ 1GB**
- Up to 2 partition columns possible for huge tables
- Partitions physical data files based on selected columns

```sql
CREATE TABLE <table> (<columns>) PARTITIONED BY ('<col1>' [, '<col2>'])
```

#### Z-Ordering *(High Cardinality)*

- Physical data reorganization
- Helps data skipping by reorganizing files so similar values are co-located; uses min/max stats to skip irrelevant files
- Common pattern: **Partitioning + ZORDER**

```sql
OPTIMIZE <table> [WHERE <condition>] ZORDER BY (<columns>)
```

#### Bloom Filtering *(Very High Cardinality)*

- Stores a bit array plus a few hash functions

```sql
CREATE BLOOMFILTER INDEX ON TABLE <table> FOR COLUMNS (<column>)
```

#### Liquid Clustering *(High Cardinality)*

- No need to rewrite data for re-optimization

```sql
CREATE TABLE <table> (<columns>) CLUSTER BY (<columns>)
```

---

### CDC (Change Data Capture)

---

### SCD — Slowly Changing Dimensions

An approach in dimensional modelling to track changes in dimension attributes over time.

| Type | Description |
|---|---|
| **SCD 0** | Data is fixed; does not change (e.g. date of birth) |
| **SCD 1** | Data is overwritten; no history maintained |
| **SCD 2** | Full history via new rows; uses Start/End Dates & Active Flag |
| **SCD 3** | Partial history; previous value stored in a new column |
| **SCD 4** | Historical changes archived to a separate table |
| **SCD 6** (1+2+3) | Hybrid of SCD 1, 2, and 3 |

---

### RLS & CLS
### ACID
### Three-Namespace

---

### Table Creation Methods
- CTAs
- `COPY INTO`
- AutoLoader

---

### Schema Evolution, Enforcement & Drift

---

### Managed vs External Tables

- Databricks managed and external tables follow the same concept as Spark
- Key difference: **Unity Catalog** adds Security & Governance on top

---

### Cloning

#### Shallow Clone
- Only **metadata** is copied; data points to the source table

```sql
CREATE TABLE shallow_clone SHALLOW CLONE <source_table>
```

#### Deep Clone
- All **data and metadata** is copied; acts as an independent table

```sql
CREATE TABLE deep_clone [DEEP] CLONE <source_table>
```

---

### CDF — Change Data Feed

- Tracks **dataset-level** and **row-level** changes

```sql
-- Query changes
SELECT * FROM table_changes('<table>', <version_number>)

-- Enable globally
SET spark.databricks.delta.properties.defaults.enableChangeDataFeed = True

-- Enable per table
ALTER TABLE <table> SET TBLPROPERTIES (delta.enableChangeDataFeed = True)
```

---

### Data Governance

| Area | Description |
|---|---|
| **Access Control / PII Masking** | Control who can access sensitive data |
| **Data Lineage** | Track data origin and transformations |
| **Auditing** | Track who accessed or modified data, and when |
| **Metadata Management** | Store schema, table definitions, and business metadata in catalogs |

---

### Data Quality Control

- Expectations / Constraints
- Schema enforcement: explicitly define schema using `StructType` & `StructField`
- Duplicate checks
- Row count validations

---

## Delta Lake

- Open-source storage architecture
- Data/tables stored as **Parquet files** in the storage layer
- **JSON log files** manage ACID properties and metadata

### Core Topics

#### Deletion Vectors
- Delta log used to mark existing rows as removed or changed **without rewriting Parquet files**

#### Schema Enforcement & Evolution
- Global setting vs DataFrame `mergeSchema` option
