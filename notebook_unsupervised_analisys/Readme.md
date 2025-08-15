# Segmentación de usuarios — *APIBus (Aprendizaje No Supervisado)*

> Notebook: `segmentacion_usuarios_apibus-No-Supervisado.ipynb`  
> Objetivo: Realizar un análisis de **segmentación de clientes** de APIBus mediante técnicas de **aprendizaje no supervisado** (*clustering*).  
> Se busca identificar grupos de usuarios con patrones de comportamiento similares para optimizar estrategias comerciales, de marketing y fidelización.

## 🧭 Tabla de contenido
- [Contexto](#contexto)
- [Arquitectura general](#arquitectura-general)
- [Datos de entrada](#datos-de-entrada)
- [Preprocesamiento y normalización](#preprocesamiento-y-normalización)
- [Modelado (Clustering)](#modelado-clustering)
- [Métricas y validación](#métricas-y-validación)
- [Visualización](#visualización)
- [Cómo ejecutar](#cómo-ejecutar)
- [Variables de entorno](#variables-de-entorno)
- [Salida y artefactos](#salida-y-artefactos)
- [Aplicaciones prácticas](#aplicaciones-prácticas)
- [Siguientes pasos](#siguientes-pasos)
- [Licencia](#licencia)

---

## Contexto
Este cuaderno conecta (opcionalmente) a **MySQL** para obtener datos de usuarios y boletos del esquema `apibus`.  
Posteriormente, genera métricas RFM (*Recency*, *Frequency*, *Monetary*) y variables relacionadas para identificar patrones de comportamiento de compra.  
Se utilizan algoritmos de *clustering* para descubrir segmentos ocultos y caracterizar perfiles de clientes.

## Arquitectura general
- **Ingesta de datos**: MySQL (`usuarios`, `boletos`, etc.) o datasets locales simulados.
- **Feature engineering**: construcción de métricas RFM y tasas de cancelación.
- **Preprocesamiento**: escalado y transformación de variables numéricas.
- **Modelado**: uso de `KMeans` y evaluación con *Silhouette Score* para elegir `k` óptimo.
- **Visualización**: gráficos 2D y 3D para interpretación de clusters.

## Datos de entrada
- **usuarios**: ID, datos demográficos (si disponibles).
- **boletos**: `fecha_reservacion`, `fecha_viaje`, `estado`, `precio`.
- **uniones**: relación usuario ↔ boletos para cálculo de métricas.

Métricas calculadas:
- **`recency_days`**: días desde la última compra.
- **`boletos_pagados`**: número total de boletos comprados/pagados.
- **`gasto_pagado`**: monto total gastado.
- **`tasa_cancelacion`**: boletos cancelados / boletos totales.

## Preprocesamiento y normalización
- Selección de variables numéricas para clustering: `recency_days`, `boletos_pagados`, `gasto_pagado`, `tasa_cancelacion`.
- Estandarización con `StandardScaler`.
- Uso de `ColumnTransformer` para facilitar futuras ampliaciones.

## Modelado (Clustering)
1. Se calcula el **Silhouette Score** para `k` en rango [2, 8] para identificar número óptimo de clusters.
2. Se entrena un **KMeans** con `n_clusters = k_óptimo`.
3. Se asigna el número de cluster a cada usuario.

## Métricas y validación
- **Silhouette Score** como métrica principal de calidad de clusters.
- Inspección visual de los clusters.
- Comparación de métricas promedio por cluster (ej. gasto promedio, frecuencia de compra).

## Visualización
- **Gráfico de Silhouette vs k** para decidir número de clusters.
- **Gráficos 2D** de pares de variables coloreados por cluster.
- (Opcional) **Visualización 3D** si se aplica reducción de dimensionalidad (PCA).

## Cómo ejecutar
1. Clona este repositorio y entra a la carpeta del proyecto.
2. Crea un entorno virtual y activa.
3. Instala dependencias:
   ```bash
   pip install pandas numpy scikit-learn sqlalchemy pymysql matplotlib seaborn
   ```
4. Configura tus variables de entorno (ver abajo).
5. Abre y ejecuta el notebook `segmentacion_usuarios_apibus-No-Supervisado.ipynb`.

### Requisitos
- Python 3.9+
- Jupyter Notebook o VSCode con soporte para `.ipynb`

## Variables de entorno
| Variable        | Ejemplo         | Descripción                         |
|-----------------|-----------------|-------------------------------------|
| `APIBUS_USER`   | `root`          | Usuario de MySQL                    |
| `APIBUS_PWD`    | `********`      | Contraseña de MySQL                 |
| `APIBUS_HOST`   | `localhost`     | Host de MySQL                       |
| `APIBUS_PORT`   | `3309`          | Puerto                              |
| `APIBUS_DB`     | `apibus`        | Base de datos                       |

> Si no defines estas variables, se utilizarán datos sintéticos.

## Salida y artefactos
- Gráficos de Silhouette y clusters.
- Archivo CSV/Excel con usuarios y su cluster asignado (si se activa en el notebook).
- Insights sobre comportamiento por cluster.

## Aplicaciones prácticas
- **Marketing segmentado**: campañas específicas para cada tipo de cliente.
- **Fidelización**: beneficios o promociones adaptadas al segmento.
- **Gestión de cancelaciones**: identificar usuarios con alta tasa de cancelación.
- **Optimización de precios**: ajustar precios según sensibilidad del segmento.

## Siguientes pasos
- Probar otros algoritmos de clustering (DBSCAN, Agglomerative).
- Incorporar variables demográficas.
- Integrar resultados en el sistema APIBus/ByteBuss para personalización en tiempo real.

## Licencia
Proyecto bajo licencia **MIT** (o la que definas).
