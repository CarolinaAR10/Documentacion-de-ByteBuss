# ByteBuss – API (Node + Express + MySQL)

Backend REST para la gestión de viajes y boletos de autobús. Implementa autenticación JWT, CRUD de entidades básicas y endpoints públicos para búsqueda de viajes, orígenes y destinos.

> **Base URL local:** `http://localhost:<PORT>` (por defecto `http://localhost:3000` si `APP_PORT` no está definido).  
> **Prefijo de API:** `/api`

---

## 🧭 Tabla de Contenidos
- [Stack](#-stack)
- [Requisitos Previos](#-requisitos-previos)
- [Instalación y Ejecución](#-instalación-y-ejecución)
- [Variables de Entorno](#-variables-de-entorno)
- [Migraciones / Sync & Seeders](#-migraciones--sync--seeders)
- [Modelos / Entidades](#-modelos--entidades)
- [Rutas y Endpoints](#-rutas-y-endpoints)
  - [Autenticación](#autenticación)
  - [Usuarios](#usuarios)
  - [Personas](#personas)
  - [Rutas y Viajes](#rutas-y-viajes)
- [Autenticación y Autorización](#-autenticación-y-autorización)
- [Swagger / Documentación](#-swagger--documentación)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Tests](#-tests)
- [Troubleshooting](#-troubleshooting)
- [Licencia](#-licencia)

---

## 📦 Stack
- **Lenguaje/Framework:** Node.js + Express
- **ORM:** Sequelize 6 (dialect: MySQL)
- **BD:** MySQL 8+
- **Auth:** JWT (Bearer) con `bcrypt`
- **Otros:** CORS, dotenv, Swagger (generado desde `config/swagger.js`), Docker (opcional)

## ⚙️ Requisitos Previos
- **Node:** ≥ 18.x  
- **MySQL:** con un schema creado (p.ej. `bytebuss`)
- **Docker:** para levantar servicios con `docker compose`

## 🚀 Instalación y Ejecución
```bash
# 1) Clonar e instalar dependencias
git clone <URL_DEL_REPO>
cd <carpeta_backend>
npm install

# 2) Variables de entorno
cp .env.example .env
# editar .env con tus credenciales de BD y JWT

# 3) Sincronizar modelos con la BD
#   Opción A) usando sync en tiempo de arranque (la app ya incluye sequelize.sync({ alter:true }))
#   Opción B) si usas Sequelize-CLI: npx sequelize db:migrate && npx sequelize db:seed:all

# 4) Levantar el servidor
npm run dev   # desarrollo con nodemon
# o
npm start     # producción

# 5) Docker (opcional)
docker compose up -d --build
```

> El servidor se inicializa en `app.js`, registra modelos/asociaciones y expone rutas bajo `/api`.

## 🔐 Variables de Entorno
| Clave | Ejemplo | Notas |
|---|---|---|
| `APP_PORT` | `3000` | Puerto del servidor Express |
| `NODE_ENV` | `development` | `development`/`production` |
| `DB_HOST` | `localhost` | Host de MySQL |
| `DB_PORT` | `3306` | Puerto de MySQL |
| `DB_NAME` | `bytebuss` | Nombre de la BD |
| `DB_USER` | `root` | Usuario de BD |
| `DB_PASS` | `secret` | Password de BD |
| `JWT_SECRET` | `supersecret` | **Obligatoria** para firmar tokens |
| `JWT_EXPIRES_IN` | `1d` | Expiración del token (ej. `1d`, `12h`) |
| `CORS_ORIGIN` | `http://localhost:4200` | Frontend permitido |

> También puedes soportar una sola `DATABASE_URL` tipo: `mysql://user:pass@host:3306/bytebuss` (ajusta `config/db.js`).

## 🛠️ Migraciones / Sync & Seeders
- En el arranque se usa `sequelize.sync({ alter: true })` para mantener el esquema sincronizado (útil en dev).  
- Si prefieres **migraciones** explícitas con Sequelize-CLI:
  ```bash
  npx sequelize db:migrate
  npx sequelize db:seed:all
  # revert
  npx sequelize db:migrate:undo
  npx sequelize db:seed:undo
  ```
- Ubica tus **seeders** (usuarios demo, roles, etc.) en `seeders/` según tu configuración.

## 🧱 Modelos / Entidades (resumen)
| Entidad | Descripción | Campos Clave (ejemplo) |
|---|---|---|
| `Usuario` | Cuentas y permisos | `id, nombreUsuario, email, passwordHash, rol, activo` |
| `Persona` | Datos personales | `id, usuarioId, nombre, apellido, telefono` |
| `Ruta` | Origen/Destino | `id, origen, destino` |
| `Viaje` | Programación | `id, rutaId, autobusId?, salida(fechaHora), precio, estado` |
| *(otras)* | *(según avance)* | — |

> Importante en Sequelize: si defines alias en asociaciones, usa **el mismo `as`** en tus `include` (ver *Troubleshooting*).

---

## 🔀 Rutas y Endpoints
**Base de rutas en `app.js`:**
```js
app.use('/api', viajeRoutes);            // /api/trips, /api/rutas/origenes, /api/rutas/destinos
app.use('/api/auth', authRoutes);        // /api/auth/login, /api/auth/verify
app.use('/api/personas', personaRoutes); // CRUD personas
app.use('/api/usuarios', usuarioRoutes); // CRUD usuarios (admin para list/hard delete)
```

### Autenticación
| Método | Ruta | Descripción | Body | Auth |
|---|---|---|---|---|
| **POST** | `/api/auth/login` | Iniciar sesión | `{ correo_electronico, contrasena }` | Público |
| **GET** | `/api/auth/verify` | Verificar token y obtener usuario | — | Bearer |

**Ejemplo – Login**
```http
POST /api/auth/login
Content-Type: application/json

{ "correo_electronico": "usuario@ejemplo.com", "contrasena": "contraseña123" }
```
**200 →** `{ "success": true, "token": "<jwt>", "user": { ... } }`

### Usuarios
> Archivo de rutas: `routes/usuario.js` (usa middlewares `authenticate` y `authorize`).

| Método | Ruta | Descripción | Body (resumen) | Auth |
|---|---|---|---|---|
| **POST** | `/api/usuarios` | Crear usuario | `{ nombre, nombreUsuario, email, password, rol? }` | Público |
| **GET** | `/api/usuarios` | Listar usuarios | — | **Admin** |
| **GET** | `/api/usuarios/:id` | Detalle de usuario | — | Bearer |
| **PUT** | `/api/usuarios/:id` | Actualizar usuario | `{ ... }` | Bearer |
| **DELETE** | `/api/usuarios/:id` | Eliminación lógica | — | Bearer |
| **DELETE** | `/api/usuarios/:id/hard` | Eliminación permanente | — | **Admin** |

### Personas
> Archivo de rutas: `routes/persona.js`

| Método | Ruta | Descripción | Body (resumen) | Auth |
|---|---|---|---|---|
| **GET** | `/api/personas` | Listar personas | — | Público/Bearer* |
| **GET** | `/api/personas/:id` | Detalle persona | — | Público/Bearer* |
| **POST** | `/api/personas` | Crear persona | `{ ... }` | Público/Bearer* |
| **PUT** | `/api/personas/:id` | Actualizar persona | `{ ... }` | Público/Bearer* |
| **DELETE** | `/api/personas/:id` | Eliminar persona | — | Público/Bearer* |

> *Si decides proteger estas rutas, agrega `authenticate` en el router.

### Rutas y Viajes
> Archivo de rutas: `routes/viajes.routes.js`

| Método | Ruta | Descripción | Query/Body | Auth |
|---|---|---|---|---|
| **GET** | `/api/trips` | Buscar viajes disponibles | `?origen=CDMX&destino=Puebla&fecha=2025-08-15` | Público |
| **GET** | `/api/rutas/origenes` | Listado de orígenes disponibles | — | Público |
| **GET** | `/api/rutas/destinos` | Listado de destinos disponibles | — | Público |

**Ejemplo – Buscar trips**
```http
GET /api/trips?origen=CDMX&destino=Puebla&fecha=2025-08-15
```
**200 →** `[{ "id": 10, "ruta": { "origen":"CDMX", "destino":"Puebla" }, "salida": "...", "precio": 350.00, ... }]` *(estructura de ejemplo)*

---

## 🔐 Autenticación y Autorización
- `POST /api/usuarios` es público (registro).  
- Resto de rutas **requieren** JWT mediante `authenticate`, excepto las públicas de búsqueda (`/trips`, `/rutas/*`) y las que decidas dejar abiertas.
- `authorize(['admin'])` protege listados sensibles y eliminaciones permanentes.

## 📜 Swagger / Documentación
- Swagger se inicializa con `swaggerDocs(app)` desde `config/swagger.js`.
- Ruta común: `/api-docs` o `/docs` (verifica en tu `swagger.js`).  
  Agrega aquí la URL exacta cuando la confirmes.

## 🧱 Estructura del Proyecto
```
src/
  app.js
  config/
    db.js
    ensureDb.js
    swagger.js
  controllers/
    authController.js
    persona.js
    usuario.js
    viajes.controller.js
    rutas.controller.js
  middlewares/
    authMiddleware.js
  models/
    index.js           # registra modelos y asociaciones
    <...>
  routes/
    authRoutes.js
    persona.js
    usuario.js
    viajes.routes.js
```

## 🧪 Tests
```bash
npm test
# o según tu configuración (jest/mocha/supertest)
```

## 🧯 Troubleshooting
- **`SequelizeEagerLoadingError: ... 'as'`** → si definiste asociaciones con alias (`as: 'persona'`), usa **el mismo `as`** en los `include`.  
- **`JWT_SECRET` faltante** → no se puede firmar/verificar tokens. Define `JWT_SECRET` en `.env`.  
- **Conexión MySQL** → verifica host/puerto/credenciales; prueba `testConnection()` en `config/db.js`.  
- **CORS** → configura `CORS_ORIGIN` en `.env` o usa `app.use(cors({ origin: ... }))`.

## 🧾 Licencia
MIT (o la que definas).
