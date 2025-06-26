
## Tarea 1. Crea una base de datos para la replicación

En esta sección, prepararás una base de datos de Cloud SQL para PostgreSQL con el objetivo de llevar a cabo la replicación con Datastream.

### Crea la base de datos de Cloud SQL

1. Ejecuta el siguiente comando para habilitar la API de Cloud SQL:
```bash
	gcloud services enable sqladmin.googleapis.com
```

Ejemplo:

![[Pasted image 20250619185938.png]]


2. Ejecuta el siguiente comando para crear una instancia de la base de datos de Cloud SQL para PostgreSQL:

```bash
POSTGRES_INSTANCE=postgres-db
DATASTREAM_IPS=34.72.28.29,34.67.234.134,34.67.6.157,34.72.239.218,34.71.242.81
gcloud sql instances create ${POSTGRES_INSTANCE} \
    --database-version=POSTGRES_14 \
    --cpu=2 --memory=10GB \
    --authorized-networks=${DATASTREAM_IPS} \
    --region=us-central1 \
    --root-password pwd \
    --database-flags=cloudsql.logical_decoding=on
```

**Nota:** Este comando crea la base de datos en . Para otras regiones, asegúrate de reemplazar **`DATASTREAM_IPS`** por las [IP públicas de Datastream](https://cloud.google.com/datastream/docs/ip-allowlists-and-regions) de tu región.

Después de crear la instancia de la base de datos, toma nota de su IP pública, ya que la necesitarás cuando crees el perfil de conexión de Datastream.

### Completa la base de datos con datos de muestra

Ejecuta el siguiente comando en Cloud Shell para conectarte a la base de datos de PostgreSQL.

```bash
gcloud sql connect postgres-db --user=postgres
```

Ejemplo:

![[Pasted image 20250619191529.png]]

la password es **pwd**

Una vez que te hayas conectado a la base de datos, ejecuta el siguiente comando de SQL para crear un esquema y una tabla de muestra:


```sql
CREATE SCHEMA IF NOT EXISTS test;

CREATE TABLE IF NOT EXISTS test.example_table (
id  SERIAL PRIMARY KEY,
text_col VARCHAR(50),
int_col INT,
date_col TIMESTAMP
);

ALTER TABLE test.example_table REPLICA IDENTITY DEFAULT; 

INSERT INTO test.example_table (text_col, int_col, date_col) VALUES
('hello', 0, '2020-01-01 00:00:00'),
('goodbye', 1, NULL),
('name', -987, NOW()),
('other', 2786, '2021-01-01 00:00:00');
```


### Configura la base de datos para la replicación

*  Ejecuta el siguiente comando de SQL para crear una publicación y una ranura de replicación:

	```sql
		CREATE PUBLICATION test_publication FOR ALL TABLES;
		ALTER USER POSTGRES WITH REPLICATION;
		SELECT PG_CREATE_LOGICAL_REPLICATION_SLOT('test_replication', 'pgoutput');
	```

Ejemplo:

![[Pasted image 20250619191851.png]]
## Tarea 2. Crea los recursos de Datastream e inicia la replicación

Ahora que la base de datos ya está lista, crea los perfiles de conexión de Datastream y comienza a transmitir para iniciar la replicación.

1. Desde el **menú de navegación**, haz clic en **Ver todos los productos**. En **Análisis**, selecciona **Datastream**.
2. Haz clic en **Habilitar** para habilitar la API de Datastream.

	![[WQ_Mz0dupWOEc1cPQqW4fvBeWy56au6SeEw7oxmoB6g=.png]]

### **Crea perfiles de conexión**

Crea dos perfiles de conexión, uno para la fuente de PostgreSQL y otro para el destino de BigQuery.

#### Perfil de conexión de PostgreSQL

1. En la consola de Cloud, ve a la pestaña **Perfiles de conexión** y haz clic en **Crear perfil**.

![[+WHJQ79_7o5oOPvGKWt+zv6Sx94pJrO3sNI_feYqLL0=.png]]

2. Selecciona el tipo de perfil de conexión **PostgreSQL**.
![[_1cLHpUGslpbHEz2e0T5h3H3zNcsweIwbY_xrCpbrjQ=.png]]
