# Mi tienda virtual

Aplicacion web de comercio electronico compuesta por un frontend en React y un backend REST en NestJS. El sistema permite consultar productos, autenticarse, administrar productos y cargar imagenes. La comunicacion entre ambas aplicaciones se realiza mediante la API del backend.

## Estructura del proyecto

```text
FinalProyect - Desarrollo WEB - Backend/
├── Backend-Nest- Mi_tienda_virtual/   # API NestJS, PostgreSQL y WebSocket
├── Frontend-React- Mi_tienda_virtual/ # Aplicacion React con Vite
└── README.md                          # Esta guia
```

## Tecnologias principales

### Backend

- NestJS 10
- TypeScript
- PostgreSQL 14
- TypeORM
- JWT y Passport para autenticacion
- Swagger para documentacion de la API
- Socket.IO para mensajes en tiempo real
- Docker Compose para PostgreSQL

### Frontend

- React 19
- TypeScript
- Vite
- React Router
- TanStack React Query
- Zustand
- Axios
- Tailwind CSS
- React Hook Form

## Requisitos previos

- Node.js compatible con el backend. El backend declara Node `17.x` en su `package.json`.
- npm o Yarn.
- Docker Desktop con Docker Compose disponible.
- Git, si se clona el proyecto desde un repositorio.

## Instalacion

Desde la carpeta raiz del proyecto, instala las dependencias de cada aplicacion.

### 1. Configurar el backend

```powershell
cd "Backend-Nest- Mi_tienda_virtual"
Copy-Item .env.template .env
npm install
```

Edita el archivo `.env` y verifica estos valores cuando uses la base de datos de Docker:

```env
STAGE=dev
DB_PASSWORD=MySecr3tPassWord@as2
DB_NAME=TesloDB
DB_HOST=localhost
DB_PORT=5433
DB_USERNAME=postgres
PORT=3000
HOST_API=http://localhost:3000/api
JWT_SECRET=Est3EsMISE3Dsecreto32s
```

> El archivo `docker-compose.yaml` publica el puerto interno `5432` de PostgreSQL como `5433` en el equipo local. Por eso `DB_PORT` debe ser `5433` al usar Docker. Si utilizas una instalacion local de PostgreSQL en el puerto `5432`, puedes conservar ese puerto y no levantar el contenedor.

### 2. Configurar el frontend

En otra terminal, desde la carpeta raiz:

```powershell
cd "Frontend-React- Mi_tienda_virtual"
Copy-Item .env.template .env
npm install
```

El archivo `.env` del frontend debe contener:

```env
VITE_API_URL=http://localhost:3000/api
```

No subas los archivos `.env` al repositorio. Usa los archivos `.env.template` como referencia y cambia los secretos en entornos reales.

## Ejecucion en desarrollo

### 1. Levantar PostgreSQL

Desde la carpeta del backend:

```powershell
cd "Backend-Nest- Mi_tienda_virtual"
docker compose up -d
```

Para comprobar el estado del contenedor:

```powershell
docker compose ps
```

Para detener PostgreSQL:

```powershell
docker compose down
```

> La carpeta `Backend-Nest- Mi_tienda_virtual/postgres` contiene los datos persistidos de PostgreSQL. No elimines esa carpeta si necesitas conservar la base de datos local.

### 2. Levantar la API

En una terminal ubicada en la carpeta del backend:

```powershell
npm run start:dev
```

La API estara disponible en:

- API base: `http://localhost:3000/api`
- Documentacion Swagger: `http://localhost:3000/api`
- Archivos publicos: `http://localhost:3000`

### 3. Cargar datos iniciales

Con el backend ejecutandose, abre en el navegador o utiliza un cliente HTTP:

```text
GET http://localhost:3000/api/seed
```

El seed recrea los datos iniciales de productos y usuarios. Tambien invalida tokens existentes y elimina los cambios realizados en la base de datos, por lo que debe ejecutarse con cuidado.

### 4. Levantar el frontend

En otra terminal:

```powershell
cd "Frontend-React- Mi_tienda_virtual"
npm run dev
```

Vite mostrara la URL local, normalmente:

```text
http://localhost:5173
```

El frontend debe iniciarse despues de que la API este disponible y use la misma URL definida en `VITE_API_URL`.

## Funcionalidades

- Catalogo de productos y consulta por genero.
- Detalle de producto mediante slug.
- Registro, inicio de sesion y comprobacion del estado de autenticacion.
- Rutas protegidas para usuarios autenticados.
- Panel administrativo para listar, crear, editar y eliminar productos.
- Carga y servicio de imagenes de productos.
- Endpoint de seed para preparar datos de demostracion.
- Gateway WebSocket para mensajes en tiempo real.

## Rutas principales del frontend

| Ruta | Descripcion |
| --- | --- |
| `/` | Catalogo principal |
| `/product/:idSlug` | Detalle de producto |
| `/gender/:gender` | Productos filtrados por genero |
| `/auth/login` | Inicio de sesion |
| `/auth/register` | Registro de usuario |
| `/admin` | Panel administrativo protegido |
| `/admin/products` | Gestion de productos |
| `/admin/products/:id` | Edicion de producto |

## Modulos principales del backend

| Modulo | Responsabilidad |
| --- | --- |
| `products` | Productos y operaciones del catalogo |
| `auth` | Registro, login, JWT, roles y proteccion de rutas |
| `files` | Carga y consulta de imagenes |
| `seed` | Carga de datos iniciales |
| `messages-ws` | Comunicacion mediante WebSocket |
| `common` | DTOs y funcionalidades compartidas |

## Scripts disponibles

### Backend

Ejecuta los comandos dentro de `Backend-Nest- Mi_tienda_virtual`:

```powershell
npm run start:dev # Desarrollo con recarga automatica
npm run build     # Compilar la aplicacion
npm run start     # Ejecutar la compilacion
npm run lint      # Revisar y corregir problemas de ESLint
npm test          # Pruebas unitarias
npm run test:e2e  # Pruebas end-to-end
npm run test:cov  # Pruebas con cobertura
```

### Frontend

Ejecuta los comandos dentro de `Frontend-React- Mi_tienda_virtual`:

```powershell
npm run dev      # Servidor de desarrollo
npm run build    # Verificar TypeScript y crear la compilacion
npm run preview  # Servir la compilacion localmente
npm run lint     # Revisar ESLint
```

## Flujo recomendado de trabajo

1. Levantar PostgreSQL con Docker.
2. Iniciar el backend en el puerto `3000`.
3. Ejecutar el seed si la base de datos esta vacia o se necesitan datos de demostracion.
4. Iniciar el frontend con Vite.
5. Abrir la URL de Vite y verificar que `VITE_API_URL` apunte al backend.

## Solucion de problemas comunes

### El backend no puede conectarse a PostgreSQL

- Confirma que el contenedor `teslodb` este activo con `docker compose ps`.
- Verifica que `.env` use `DB_HOST=localhost` y `DB_PORT=5433` cuando la base provenga de Docker.
- Comprueba que `DB_NAME`, `DB_USERNAME` y `DB_PASSWORD` coincidan con la configuracion del contenedor.

### El frontend no carga productos

- Confirma que el backend este ejecutandose en `http://localhost:3000`.
- Comprueba `VITE_API_URL=http://localhost:3000/api`.
- Reinicia Vite despues de modificar el archivo `.env`, porque las variables se leen al iniciar el servidor.

### Las imagenes no aparecen

- Verifica que el backend este activo.
- Comprueba que las imagenes se encuentren en las carpetas publicas configuradas por el backend.
- Revisa la consola del navegador y la URL generada para los archivos del producto.

## Seguridad

Los valores del `.env.template` son solo para desarrollo. En produccion debes utilizar una contrasena de base de datos y un `JWT_SECRET` nuevos, no exponerlos en el repositorio y configurar `STAGE=prod` junto con los valores de infraestructura correspondientes.
