# Predicción de estado de boletos — *APIBus (Aprendizaje Supervisado)*

> Notebook: `supervisado_apibus.ipynb`  
> Objetivo: Entrenar y evaluar un modelo de **clasificación supervisada** que predice el **`estado`** de un boleto (p. ej., “pagado”, “cancelado”, “reservado”), usando datos de MySQL (`apibus`) y la tabla de **autobuses** como apoyo. Incluye además un bloque **opcional** de **regresión** para estimar el **`precio`** del boleto.

## 🧭 Tabla de contenido
- [Contexto](#contexto)
- [Arquitectura general](#arquitectura-general)
- [Datos de entrada](#datos-de-entrada)
- [Ingeniería de características](#ingeniería-de-características)
- [Modelado](#modelado)
- [Métricas y evaluación](#métricas-y-evaluación)
- [Cómo ejecutar](#cómo-ejecutar)
- [Variables de entorno](#variables-de-entorno)
- [Salida y artefactos](#salida-y-artefactos)
- [Inferencia (ejemplo rápido)](#inferencia-ejemplo-rápido)
- [Buenas prácticas / Notas](#buenas-prácticas--notas)
- [Siguientes pasos](#siguientes-pasos)
- [Licencia](#licencia)

---

## Contexto
El cuaderno conecta (opcionalmente) a **MySQL** para leer datos del esquema `apibus`, entrena un modelo de **clasificación** para predecir el `estado` del boleto y guarda el **mejor modelo** a disco (formato `joblib`).  
Adicionalmente, ofrece un flujo **opcional** de **regresión** para estimar `precio` usando un **RandomForestRegressor**.

> Si no hay base de datos disponible, el cuaderno contempla generar/usar datos **sintéticos** o cargar datos desde archivos locales (según configuración del usuario).

## Arquitectura general
- **Ingesta**: `boletos` y `autobuses` desde MySQL (o datos sintéticos).
- **Unión**: `boletos` ←→ `autobuses` (prefijo `bus_` tras la unión).
- **Feature engineering**: variables de calendario y de flota.
- **Preprocesamiento**: `ColumnTransformer` con `OneHotEncoder` (cat) + `StandardScaler` (num).
- **Modelo (Clasificación)**: *Pipeline* de scikit-learn (modelo exacto elegible en el notebook; típicamente `LogisticRegression`, `RandomForestClassifier` o similares).
- **Modelo (Regresión, opcional)**: `RandomForestRegressor`.
- **Evaluación**: *hold-out* train/test + métricas clásicas.
- **Persistencia**: `joblib` para guardar el mejor modelo.

## Datos de entrada
Tablas principales (esquema **`apibus`**):
- `boletos`: información del boleto (estado, fechas, asiento, precio, `autobus_id`, etc.).
- `autobuses`: metadatos del bus (capacidad, modelo, año, estado, …).

> La conexión usa credenciales por variables de entorno (ver [Variables de entorno](#variables-de-entorno)).

## Ingeniería de características
A partir de la unión `boletos` + `autobuses` (`left_on="autobus_id"`, `right_on="bus_id"` con prefijo `bus_`), se generan, entre otras, las siguientes **features** (nombres según el notebook):

- `antiguedad_bus = 2025 - bus_anio`
- `mes_reserva = month(fecha_reservacion)`
- `mes_viaje = month(fecha_viaje)`
- `dias_anticipacion = fecha_viaje - fecha_reservacion (días)`
- Variables de asiento/capacidad: `asiento_numero`, `bus_capacidad`
- Categóricas del bus: `bus_modelo`, `bus_estado`
- (Opcional) `precio` como variable objetivo para **regresión**

> La **variable objetivo** para *clasificación* es `estado`.

## Modelado
**Clasificación (`estado`)**  
Se implementa un *pipeline* de **scikit-learn** con:
- `ColumnTransformer` →
  - `OneHotEncoder(handle_unknown="ignore")` para variables categóricas
  - `StandardScaler()` para variables numéricas
- **Modelo** (elegible en el cuaderno; por defecto suele usarse una de estas opciones):
  - `LogisticRegression` o
  - `RandomForestClassifier`

**Regresión (opcional, `precio`)**  
- `RandomForestRegressor(n_estimators=400, random_state=42)` dentro de un *pipeline* análogo.

## Métricas y evaluación
- **Clasificación**: *accuracy*, *f1-score*, matriz de confusión (según celdas del notebook).
- **Regresión**: `MAE` y `R²` (impresos al finalizar el entrenamiento).

> El cuaderno divide el conjunto en **train/test** con `train_test_split(test_size=0.2, random_state=42)`.

## Cómo ejecutar
1. **Clona** el repositorio y entra a la carpeta del proyecto.
2. (Opcional) Crea y activa un **entorno virtual**.
3. **Instala dependencias** mínimas:
   ```bash
   pip install pandas numpy scikit-learn sqlalchemy pymysql matplotlib joblib
   ```
4. Configura tus **variables de entorno** (ver abajo).
5. Abre `supervisado_apibus.ipynb` en Jupyter/VSCode y ejecuta las secciones en orden.

### Requisitos
- Python 3.9+
- Acceso a MySQL (opcional si vas a usar datos sintéticos)
- Jupyter (o similar) para correr el notebook

## Variables de entorno
El notebook contempla variables con prefijo `APIBUS_` para la conexión MySQL:

| Variable        | Ejemplo         | Descripción                         |
|-----------------|-----------------|-------------------------------------|
| `APIBUS_USER`   | `root`          | Usuario de MySQL                    |
| `APIBUS_PWD`    | `********`      | Contraseña de MySQL                 |
| `APIBUS_HOST`   | `localhost`     | Host de MySQL                       |
| `APIBUS_PORT`   | `3309`          | Puerto                              |
| `APIBUS_DB`     | `apibus`        | Base de datos                       |

> Si no defines estas variables, el cuaderno usa **valores por defecto** y/o datos locales/sintéticos.

## Salida y artefactos
- **Modelo de clasificación** guardado en: `modelo_boleto_estado.joblib`
- (Opcional) **Modelo de regresión** para `precio` (análogo, si se ejecuta la sección 8).
- **Gráficas** exploratorias (distribución de `estado`, etc.).

## Inferencia (ejemplo rápido)
Ejemplo mínimo (como en el notebook) para predecir `estado` con el modelo guardado:

```python
import joblib
import pandas as pd

modelo = joblib.load("modelo_boleto_estado.joblib")

nuevo = pd.DataFrame([{
    "precio": 520.0,
    "asiento_numero": 14,
    "antiguedad_bus": 5,
    "bus_capacidad": 45,
    "mes_reserva": 6,
    "mes_viaje": 7,
    "dias_anticipacion": 12,
    "bus_modelo": "Irizar i6",
    "bus_estado": "activo"
}])

print("Predicción de estado:", modelo.predict(nuevo)[0])
```

## Buenas prácticas / Notas
- Verifica **desbalance** de clases en `estado`; considera *class weights* o *resampling* si es necesario.
- Ajusta **hiperparámetros** (Grid/Random Search) para mejorar métricas.
- Versiona los artefactos (`joblib`) y guarda **semillas** (`random_state`) para reproducibilidad.
- Asegúrate de **alinear** las columnas de inferencia con las usadas en entrenamiento.

## Siguientes pasos
- Añadir **validación cruzada** y *hyperparameter tuning*.
- Exportar el *pipeline* como **API** (FastAPI/Flask) para servir predicciones.
- Monitoreo de **deriva de datos** y *model performance*.
- Integración con el proyecto **ByteBuss/APIBus** para predicción en tiempo real (checkout & check-in, cancelaciones, etc.).

## Licencia
Este proyecto se distribuye bajo la licencia **MIT** (o la que definas).

---

> ✉️ ¿Dudas o mejoras? Abre un *issue* o envía un *pull request*.
