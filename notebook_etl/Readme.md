
# Proceso ETL – ByteBuss 

Este README resume y estandariza el flujo ETL implementado en el notebook **`Practica_MySQL_Usuarios_Boletos.ipynb`**, generando una guía reproducible para cargar datos, limpiarlos, analizarlos y exportar un dataset listo para consumo.

## Contenido del Notebook (outline)
- # Práctica – Extracción y Análisis desde MySQL (Tablas **usuarios** y **boletos**)
- ## 8) Consultas SQL sugeridas (copiables)

---

## Dependencias detectadas
Paquetes importados automáticamente del notebook (puedes ajustar):  
SQLAlchemy, matplotlib, pandas

### Instalación rápida
```bash
pip install SQLAlchemy matplotlib pandas
```

---

## Fuentes de Datos detectadas

### 1) Base de Datos
- Uso de SQL: **Sí, se usa SQLAlchemy/`read_sql`**
- MySQL: **Conexión MySQL (`mysql+pymysql`/`mysql://`) detectada**

### 2) API(s)
- (no se detectaron llamadas directas a endpoints; ajusta si aplica)

### 3) CSV
- (no se detectaron lecturas CSV)

### 4) Exportaciones
- `boletos_export.csv`
- `boletos_usuarios_export.csv`
- `usuarios_export.csv`

---

## Estructura sugerida del ETL
```
notebook_etl/
  b1d10529-1e73-4b99-bc90-44e572544be4.ipynb
  README.md
  data/
    raw/
    interim/
    processed/
```

---

## Reproducibilidad – plantilla de .env
```dotenv
DB_HOST=localhost
DB_PORT=3306
DB_NAME=bytebuss
DB_USER=root
DB_PASS=secret

API_BASE=http://localhost:3000/api
# API_TOKEN=Bearer <tu_token_si_aplica>
```

---

## Pasos Estándar (para alinear el notebook)

### 1) Importación de Librerías
Asegúrate de importar y cargar variables de entorno:
```python
import os
import pandas as pd
from dotenv import load_dotenv
load_dotenv()
```

### 2) Conexión a MySQL (si aplica)
```python
from sqlalchemy import create_engine
engine = create_engine(f"mysql+pymysql://{os.getenv('DB_USER')}:{os.getenv('DB_PASS')}@{os.getenv('DB_HOST')}:{os.getenv('DB_PORT','3306')}/{os.getenv('DB_NAME')}")
```

### 3) Ingesta
- **SQL**: usa `pd.read_sql(<query>, engine)`
- **API**: `import requests` y `requests.get(f"{os.getenv('API_BASE')}/trips", params={...})`
- **CSV**: `pd.read_csv("data/raw/<archivo>.csv")`

### 4) Transformaciones
- Tipado de fechas, limpieza de nulos/duplicados, normalización de texto.
- Reglas de negocio (campos derivados: año/mes/día, etc.).

### 5) EDA y Gráficas
Incluye matplotlib/seaborn (detectado)

### 6) Exportación
- `data/processed/bytebuss_trips_clean.csv`
- `data/processed/bytebuss_trips_clean.parquet`

```python
import os
os.makedirs("data/processed", exist_ok=True)
df.to_csv("data/processed/bytebuss_trips_clean.csv", index=False)
# si usas pyarrow/fastparquet:
# df.to_parquet("data/processed/bytebuss_trips_clean.parquet", index=False)
```

---

## Notas
- Si usas asociaciones/joins complejos en SQL, documenta las llaves y filtros.
- Versiona los catálogos (orígenes/destinos) si provienen de la API para reproducibilidad.
- Mantén una sección de **cambios** en el notebook/README cuando agregues reglas nuevas.
