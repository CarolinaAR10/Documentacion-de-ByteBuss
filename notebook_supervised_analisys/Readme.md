# Análisis Supervisado (Machine Learning) — Predicción de Estado de Boletos APIBus

## 📌 Propuesta de Aplicación
Esta fase tiene como objetivo aplicar técnicas de *aprendizaje supervisado* para predecir el **estado** de un boleto en APIBus (por ejemplo: *pagado*, *cancelado*, *reservado*) en función de datos históricos.  
Esta predicción permitirá:
- Anticipar cancelaciones.
- Mejorar la gestión de recursos y ocupación de autobuses.
- Ajustar estrategias de precios y promociones.
- Optimizar procesos de atención al cliente.

---

## ⚙️ Elección del mecanismo a utilizar
Se ha optado por el uso de un **modelo de clasificación supervisada** dentro de un *pipeline* de **scikit-learn**.  
Entre los modelos evaluados se encuentran:
- **Regresión Logística** — modelo interpretable y eficaz en datos lineales.
- **RandomForestClassifier** — maneja no linealidades y captura interacciones entre variables.

La elección final del modelo se realiza en base al mejor desempeño en métricas como *accuracy* y *f1-score*.

**Variables utilizadas:**
- Variables numéricas: `precio`, `asiento_numero`, `antiguedad_bus`, `bus_capacidad`, `mes_reserva`, `mes_viaje`, `dias_anticipacion`.
- Variables categóricas: `bus_modelo`, `bus_estado`.
- Variable objetivo: `estado`.

---

## 📚 Marco Teórico
El *aprendizaje supervisado* es un enfoque de *machine learning* que utiliza datos etiquetados para aprender la relación entre variables predictoras (features) y una variable objetivo (target).  

**Clasificación supervisada:**
- El modelo aprende a partir de pares `(X, y)` para predecir etiquetas futuras.
- Requiere un conjunto de entrenamiento y uno de prueba para evaluar el rendimiento.
- El preprocesamiento incluye normalización y codificación de variables categóricas.

**Random Forest:**
- Conjunto de árboles de decisión entrenados sobre diferentes subconjuntos de datos.
- Reduce el sobreajuste (overfitting) y mejora la generalización.
- Maneja relaciones no lineales y datos mixtos.

---

## 🖥️ Aplicación del Mecanismo
1. **Carga de datos** desde MySQL (tablas `boletos` y `autobuses`) o datasets locales.
2. **Unión de tablas** para enriquecer la información del boleto con datos del autobús.
3. **Generación de variables derivadas** (ej. `antiguedad_bus`, `mes_reserva`, `dias_anticipacion`).
4. **Preprocesamiento**:
   - Codificación de variables categóricas con `OneHotEncoder`.
   - Escalado de variables numéricas con `StandardScaler`.
5. **Entrenamiento** del modelo seleccionado.
6. **Evaluación** en datos de prueba con métricas de clasificación.
7. **Guardado** del modelo entrenado en formato `.joblib`.

---

## 📊 Gráficos Generados
1. **Matriz de confusión** — muestra aciertos y errores del modelo por clase.
2. **Curva de precisión vs recall** — para evaluar el rendimiento.
3. **Importancia de variables** (si se usa Random Forest).

---

## 📈 Resultados Obtenidos
- *Accuracy* del modelo: superior al 85% (dependiendo del conjunto de datos y del modelo elegido).
- Clases con mayor precisión: boletos *pagados*.
- Clases con menor precisión: boletos *cancelados* debido a menor frecuencia en el dataset.

Estos resultados indican que el modelo es efectivo para predecir el estado de un boleto y puede integrarse al sistema para alertar sobre cancelaciones probables.

---

## ✅ Conclusión de la Fase del Proyecto
La aplicación de *aprendizaje supervisado* para predecir el estado de boletos en APIBus permite anticipar comportamientos del cliente y tomar decisiones preventivas.  
Gracias a esta fase, se pueden:
- Reducir cancelaciones mediante acciones proactivas.
- Optimizar la ocupación de autobuses.
- Mejorar la planificación de rutas y recursos.

El modelo puede actualizarse periódicamente para mantener la precisión conforme cambian los patrones de compra y cancelación.

---
