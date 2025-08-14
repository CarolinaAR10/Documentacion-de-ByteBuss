# 📚 Documentación ByteBuss

ByteBuss es un sistema integral para la **gestión inteligente del transporte público**.  
Incluye módulos de backend (API REST), frontend (aplicación web), análisis de datos (ETL y modelos de IA) y scripts para base de datos.

---

## 📂 Estructura del Repositorio

    .
    ├─ api_sourcecode/                 # Código fuente de la API REST (Node.js + Express + Sequelize + MySQL)
    ├─ notebook_etl/                   # Notebooks para extracción, transformación y carga de datos
    ├─ notebook_supervised_analysisys/ # Notebooks de modelos supervisados (clasificación/regresión)
    ├─ notebook_unsupervised_analysisys/ # Notebooks de modelos no supervisados (clustering, segmentación)
    ├─ propuesta_datawarehouse/        # Diseño y propuesta del Data Warehouse (modelo dimensional, KPIs)
    ├─ seeder_sourcecode/              # Scripts SQL para crear o poblar la base de datos
    └─ Readme.md                       # Este documento

---

## 🚀 Descripción General

**ByteBuss** busca digitalizar el transporte público urbano, permitiendo:
- Gestión de rutas, horarios y unidades.
- Compra y validación de boletos vía NFC.
- Seguimiento en tiempo real de autobuses.
- Análisis de datos para optimizar la operación.
- Integración con un Data Warehouse para métricas y reportes.

---

## 🔹 Componentes

### **1. API REST (`api_sourcecode/`)**
- Framework: **Node.js + Express**
- ORM: **Sequelize**
- Base de datos: **MySQL**
- Documentación con **Swagger**
- Autenticación con **JWT**

📄 Más info: `api_sourcecode/readme.md`

---

### **2. Frontend Web (`front_sourcecode/`)**
- Framework: **Angular**
- Integración con API REST
- Autenticación con JWT
- UI adaptable (responsive)

📄 Más info: `front_sourcecode/readme.md`

---

### **3. Notebooks de análisis**
- `notebook_etl/`: Limpieza y carga de datos.
- `notebook_supervised_analysisys/`: Modelos supervisados (predicción de ocupación, retrasos).
- `notebook_unsupervised_analysisys/`: Clustering y segmentación de rutas/usuarios.

---

### **4. Data Warehouse (`propuesta_datawarehouse/`)**
- Modelo dimensional.
- Proceso ETL propuesto.
- KPIs sugeridos (ocupación promedio, ingresos por ruta, puntualidad).

---

### **5. Scripts SQL (`seeder_sourcecode/`)**
- `db_backup_onlystructure.sql`: Estructura de la base de datos.
- `db_backup_withdata.sql`: Estructura + datos de ejemplo para pruebas.

---

## 🛠️ Cómo Empezar

### 1️⃣ Clonar el repositorio
    git clone https://github.com/<usuario>/<repo>.git
    cd <repo>

### 2️⃣ Configurar entorno
- Revisar `.env.example` en `api_sourcecode/` y `front_sourcecode/`.
- Crear archivos `.env` con las variables necesarias.

### 3️⃣ Levantar el proyecto (modo desarrollo)

**API**
    cd api_sourcecode
    npm install
    npm run dev

**Frontend**
    cd front_sourcecode
    npm install
    npm start

---

## 🗺️ Arquitectura del Sistema

```
+----------------+      REST/JSON      +-------------------+
|  Frontend Web  |  <----------------> |  Backend API      |
|  (Angular)     |                     |  (Node.js/Express)|
+----------------+                     +---------+---------+
                                                |
                                                | Sequelize ORM
                                                v
                                        +-------------------+
                                        |   Base de Datos   |
                                        |     (MySQL)       |
                                        +-------------------+
```

---

## 🌍 Alineación con ODS (Agenda 2030)

- **ODS 9**: Industria, innovación e infraestructura.
- **ODS 11**: Ciudades y comunidades sostenibles.
- **ODS 13**: Acción por el clima.

La implementación de ByteBuss contribuye a la movilidad sostenible, la optimización de rutas y la reducción de emisiones.

---

## 📌 Roadmap
- [ ] Integrar CI/CD con GitHub Actions.
- [ ] Completar suite de pruebas (API y Front).
- [ ] Agregar dashboards de métricas en el frontend.
- [ ] Implementar notificaciones en tiempo real (WebSocket).

---

## 📄 Licencia
Este proyecto se distribuye bajo la licencia **MIT**.

---

✍️ **Equipo Los Pozoles** — Caro, Raúl, Zacek y Edgar.
