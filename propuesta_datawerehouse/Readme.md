# Propuesta de Datawarehouse

## Contexto de un datawarehouse en el proyecto
Un **Datawarehouse** es un repositorio centralizado que integra y almacena datos de diferentes fuentes, con el objetivo de facilitar el análisis y la toma de decisiones estratégicas. En el contexto del proyecto ByteBuss, su función principal sería unificar información de operaciones, ventas y factores externos, permitiendo un análisis más profundo sobre patrones de uso, demanda de boletos y optimización de recursos.  
Estos datos provendrían de sistemas internos (reservas, rutas, horarios) y de **fuentes externas** relevantes que puedan influir en el comportamiento de los usuarios.

## Propuesta de 3 orígenes de datos alternativos
- **Información meteorológica**: Condiciones climáticas actuales y pronósticos que pueden afectar la afluencia de pasajeros y la demanda de rutas específicas.
- **Eventos locales**: Información sobre conciertos, ferias, partidos u otras actividades que puedan aumentar la demanda de transporte en ciertas zonas y fechas.
- **Tráfico en tiempo real**: Datos de congestión vial y rutas alternas para mejorar la puntualidad y la planificación de horarios.

## 5 Experimentos de asociación (mezcla) de datos
1. Relacionar la información meteorológica con el nivel de ocupación de las rutas.
2. Analizar el incremento de ventas antes y durante eventos locales.
3. Vincular datos de tráfico con retrasos en salidas y llegadas.
4. Combinar historial de ventas con patrones de clima para predicciones.
5. Medir la eficiencia de rutas ajustadas por congestión en comparación con rutas fijas.

## Toma de decisiones
- Ajustar horarios y frecuencias según pronósticos climáticos adversos.
- Implementar rutas temporales o refuerzos durante eventos locales de alta demanda.
- Usar datos de tráfico para reprogramar salidas en tiempo real y minimizar retrasos.
- Desarrollar promociones estratégicas en temporadas o días de baja afluencia.
- Priorizar rutas con mejor rendimiento operativo en función de factores externos.
