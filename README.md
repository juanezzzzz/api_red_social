# 🗣️ API Red Social (NestJS + MongoDB)

![NestJS](https://img.shields.io/badge/NestJS-E0234E?style=flat&logo=nestjs&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Swagger](https://img.shields.io/badge/Swagger-85EA2D?style=flat&logo=swagger&logoColor=black)

> API REST de una **red social** construida con **NestJS** y **MongoDB**: usuarios, roles, publicaciones, comentarios, reacciones y seguidores.

---

## 📖 ¿Qué es?

**API Red Social** es un backend REST escrito en **TypeScript con NestJS** que expone la lógica de una red social sobre una base de datos **MongoDB** (vía Mongoose). Está organizado en **6 módulos** independientes, con **validación de datos** por DTOs, **documentación Swagger** automática y **manejo de errores** centralizado.

## 🧩 ¿Qué hace?

- 👤 Gestiona **usuarios** (con contraseña cifrada con **bcrypt**) y **roles**.
- 📝 Permite crear y administrar **publicaciones**.
- 💬 Añade **comentarios** a las publicaciones.
- ❤️ Registra **reacciones** (like, love, care, haha, wow, sad, angry) con un endpoint _toggle_.
- 🔗 Maneja la red de **seguidores** (seguir / dejar de seguir, listar seguidos y seguidores).

---

## 🧱 Arquitectura

Arquitectura modular de NestJS (cada recurso es un módulo autocontenido con su controlador, servicio, DTOs y schema):

```
api_red_social/
├── src/
│   ├── main.ts                  # Bootstrap: Swagger, ValidationPipe y filtro global
│   ├── app.module.ts            # Módulo raíz: conexión a MongoDB + registro de módulos
│   ├── common/
│   │   ├── filters/             # Filtro global de excepciones HTTP
│   │   └── helpers/             # Helper de respuestas estandarizadas
│   └── modules/
│       ├── usuarios/            # 👤 Usuarios
│       ├── roles/               # 🛡️ Roles
│       ├── publicaciones/       # 📝 Publicaciones
│       ├── comentarios/         # 💬 Comentarios
│       ├── reacciones/          # ❤️ Reacciones
│       └── seguidores/          # 🔗 Seguidores
│           └── (controller · service · module · dto/ · schemas/)
├── test/
└── package.json
```

Cada petición pasa por: **ValidationPipe** (valida el DTO) → **Controller** → **Service** (lógica) → **Mongoose Model** (MongoDB). Los errores se formatean con un **filtro global** de excepciones.

---

## 🛠️ Stack

| Tecnología | Uso |
|---|---|
| **NestJS 11** | Framework backend (Node.js + TypeScript) |
| **MongoDB + Mongoose** | Base de datos NoSQL y ODM |
| **class-validator / class-transformer** | Validación de DTOs (`whitelist` activado) |
| **bcrypt** | Cifrado de contraseñas |
| **@nestjs/swagger** | Documentación interactiva de la API |
| **@nestjs/config** | Variables de entorno |
| **Jest** | Pruebas unitarias |

---

## 🗃️ Modelo de datos (6 recursos)

| Recurso | Campos principales | Relaciones |
|---|---|---|
| **Usuario** | `nombre`, `correo` (único), `password` (cifrada), `activo` | → Rol |
| **Rol** | `nombre` (único), `activo` | — |
| **Publicación** | `contenido` | → Usuario (autor) |
| **Comentario** | `contenido` | → Publicación, Usuario |
| **Reacción** | `tipo` (like/love/care/haha/wow/sad/angry) | → Usuario, Publicación |
| **Seguidor** | `activo` | → seguidor (Usuario), seguido (Usuario) |

> 🧷 Todos los documentos incluyen `timestamps` (createdAt / updatedAt) automáticos, y varias entidades usan `activo` como bandera de baja lógica.

---

## 📡 Endpoints

### 👤 Usuarios &nbsp;`/Usuarios`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/Usuarios` | Crear usuario |
| `GET` | `/Usuarios` | Listar (con búsqueda por query) |
| `GET` | `/Usuarios/:id` | Obtener por ID |
| `PUT` | `/Usuarios/:id` | Actualizar |
| `DELETE` | `/Usuarios/:id` | Eliminar |

### 🛡️ Roles &nbsp;`/roles`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/roles` | Crear rol |
| `GET` | `/roles` | Listar roles activos |
| `GET` | `/roles/inactivo` | Listar roles inactivos (baja lógica) |
| `GET` | `/roles/:id` | Obtener por ID |
| `PUT` | `/roles/:id` | Reemplazo total |
| `PATCH` | `/roles/:id` | Actualización parcial |
| `PATCH` | `/roles/:id/restore` | Restaurar rol eliminado |
| `DELETE` | `/roles/:id` | Eliminar (baja lógica) |

### 📝 Publicaciones &nbsp;`/publicaciones`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/publicaciones` | Crear |
| `GET` | `/publicaciones` | Listar todas |
| `GET` | `/publicaciones/:id` | Obtener por ID |
| `PUT` | `/publicaciones/:id` | Actualizar |
| `DELETE` | `/publicaciones/:id` | Eliminar |

### 💬 Comentarios &nbsp;`/comentarios`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/comentarios` | Crear comentario |
| `GET` | `/comentarios/publicacion/:publicacionId` | Comentarios de una publicación |
| `GET` | `/comentarios/:id` | Obtener por ID |
| `DELETE` | `/comentarios/:id` | Eliminar |

### ❤️ Reacciones &nbsp;`/reacciones`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/reacciones/toggle` | Añadir / quitar reacción |
| `GET` | `/reacciones/publicacion/:publicacionId` | Reacciones de una publicación |

### 🔗 Seguidores &nbsp;`/seguidores`

| Método | Ruta | Acción |
|---|---|---|
| `POST` | `/seguidores/seguir` | Seguir a un usuario |
| `POST` | `/seguidores/dejar-seguir` | Dejar de seguir |
| `GET` | `/seguidores/siguiendo/:usuarioId` | A quiénes sigue el usuario |
| `GET` | `/seguidores/seguidores/:usuarioId` | Quiénes siguen al usuario |

---

## ⚙️ Configuración y ejecución

### Requisitos

- **Node.js** 18+
- **MongoDB** (local o Atlas)

### Variables de entorno

Crea un archivo `.env` en la raíz:

```env
MONGO_URI=mongodb://localhost:27017/red_social
PORT=3000
```

### Ejecutar

```bash
# 1. Clonar
git clone https://github.com/juanezzzzz/api_red_social.git
cd api_red_social

# 2. Dependencias
npm install

# 3. Modo desarrollo (con recarga)
npm run start:dev
```

La API queda en **`http://localhost:3000`** 🚀

### 📚 Documentación Swagger

Con el servidor arriba, la documentación interactiva está en:

```
http://localhost:3000/swagger
```

Desde ahí puedes explorar y probar todos los endpoints.

---

## 🗺️ Roadmap

- [ ] **Autenticación JWT** y protección de rutas
- [ ] Unificar el prefijo de rutas a minúsculas (`/usuarios` en lugar de `/Usuarios`)
- [ ] Limpiar dependencias (`package.json` incluye un paquete `mongose` sobrante)
- [ ] Ampliar la cobertura de **pruebas** (Jest)
- [ ] **Dockerización** (app + MongoDB)

---

## 🧭 Flujo de desarrollo

Construido con **Git Flow**: ramas `feature/*` por módulo (usuarios, roles, publicaciones, comentarios, reacciones, seguidores) → `develop` → `main`, integrando cambios vía Pull Requests.

---

## 👤 Autor

**Juan Esteban Valencia A.** — [@juanezzzzz](https://github.com/juanezzzzz)
