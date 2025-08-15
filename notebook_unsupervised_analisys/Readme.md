# Análisis No Supervisado (Machine Learning) — Segmentación de Usuarios APIBus

## 📌 Propuesta de Aplicación
El objetivo de esta fase es aplicar técnicas de *aprendizaje no supervisado* para segmentar a los usuarios de **APIBus** en grupos con comportamientos de compra similares.  
Esta segmentación permitirá:
- Diseñar estrategias de marketing personalizadas.
- Optimizar promociones y precios.
- Detectar usuarios con alto riesgo de cancelación.
- Mejorar la experiencia del cliente mediante ofertas adaptadas.

---

## ⚙️ Elección del mecanismo a utilizar
Se ha elegido el algoritmo **K-Means Clustering** por su efectividad y facilidad de interpretación en datos numéricos.  
Este método agrupa los datos en *k* clústeres, minimizando la distancia de cada punto al centroide del grupo.  
Es apropiado para este proyecto porque no se dispone de etiquetas previas y el objetivo es descubrir patrones ocultos.

**Variables utilizadas:**
- `recency_days`: días desde la última compra.
- `boletos_pagados`: número total de boletos pagados.
- `gasto_pagado`: monto total gastado.
- `tasa_cancelacion`: proporción de boletos cancelados.

---

## 📚 Marco Teórico
El *aprendizaje no supervisado* es un enfoque de *machine learning* donde el modelo aprende patrones únicamente a partir de las características de los datos, sin una variable objetivo conocida.  

**K-Means:**
- Algoritmo iterativo que asigna puntos a clústeres minimizando la varianza interna.
- Requiere especificar el número de clústeres (*k*).
- Sensible a la escala de los datos, por lo que se aplicó **normalización** previa usando `StandardScaler`.
- Se evaluó el número óptimo de clústeres con la métrica **Silhouette Score**.

---

## 🖥️ Aplicación del Mecanismo
1. **Carga de datos** desde MySQL o dataset local simulado.
2. **Cálculo de métricas RFM** y tasa de cancelación.
3. **Selección de variables**: `recency_days`, `boletos_pagados`, `gasto_pagado`, `tasa_cancelacion`.
4. **Normalización** con `StandardScaler`.
5. **Evaluación de k** usando *Silhouette Score* para k = 2 a 8.
6. **Entrenamiento** de K-Means con el valor óptimo de k.
7. **Asignación de clúster** a cada usuario.

---

## 📊 Gráficos Generados
1. **Silhouette Score vs k** — determina el k óptimo.
2. **Gráfico de dispersión 2D** (`recency_days` vs `gasto_pagado`) coloreado por clúster.
3. **Mapa de calor** con el promedio de variables por clúster.

---

## 📈 Resultados Obtenidos
- Número óptimo de clústeres: **3**.
- Segmentos identificados:
  1. **Clientes frecuentes y alto gasto**.
  2. **Clientes ocasionales de gasto medio**.
  3. **Clientes con alta cancelación y baja frecuencia**.

Estos resultados permiten crear campañas y estrategias diferenciadas para cada grupo, optimizando el uso de recursos y maximizando el retorno de inversión.

---

## ✅ Conclusión de la Fase del Proyecto
La segmentación obtenida proporciona información valiosa sobre el comportamiento de los usuarios de APIBus.  
Gracias a esta fase, es posible:
- Dirigir promociones de forma más eficiente.
- Reducir cancelaciones.
- Mejorar la experiencia de los clientes más valiosos.

La metodología aplicada puede repetirse periódicamente para mantener los segmentos actualizados y adaptarse a cambios en los patrones de comportamiento.

