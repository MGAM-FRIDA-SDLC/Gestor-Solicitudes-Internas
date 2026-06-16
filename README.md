# Gestor de Solicitudes Internas

Web app para crear, gestionar y hacer seguimiento de solicitudes internas (soporte, aprobaciones, requerimientos).

## Stack

- **Frontend**: React 18 + Vite + React Router v6
- **Backend**: Node.js + Express
- **Base de datos**: PostgreSQL (esquema `reto_c` en base `ia_challenge`)

---

## Requisitos previos

- Node.js 18+
- PostgreSQL corriendo localmente con la base de datos `ia_challenge` y el esquema `reto_c`

---

## Configuración

### Variables de entorno del backend

El archivo `backend/.env` ya está configurado con los siguientes valores:

```
PORT=3001
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=Admin123
DB_NAME=ia_challenge
DB_SCHEMA=reto_c
```

Modificar si la configuración local difiere.

---

## Instalación de dependencias

Desde la raíz del proyecto:

```bash
# Instalar dependencias del backend
cd backend && npm install

# Instalar dependencias del frontend
cd ../frontend && npm install
```

---

## Ejecución en desarrollo

Abrir **dos terminales** separadas:

### Terminal 1 — Backend

```bash
cd backend
npm run dev
```

El servidor Express quedará disponible en: `http://localhost:3001`

### Terminal 2 — Frontend

```bash
cd frontend
npm run dev
```

La aplicación React quedará disponible en: `http://localhost:5173`

> El frontend hace proxy automático de `/api/*` hacia el backend en el puerto 3001.

---

## Verificar que el backend funciona

```
GET http://localhost:3001/api/health
```

Respuesta esperada:
```json
{ "status": "ok", "db": "connected" }
```

---

## Módulos de la aplicación

### Módulo 1 — Nueva Solicitud (`/nueva`)

Formulario para crear solicitudes con:
- Tipo de solicitud (dropdown con datos de `reto_c.tipos_solicitud`)
- Urgencia: Alta / Media / Baja (selector visual)
- Solicitante (nombre libre)
- Área (dropdown con datos de `reto_c.areas`)
- Descripción (textarea con validación mínima)

Al crear, se genera un número de ticket (`TKT-00001`) y se registra el evento inicial en `reto_c.historial_solicitudes`.

### Módulo 2 — Bandeja de Solicitudes (`/bandeja`)

Tabla con todas las solicitudes con:
- Filtros por tipo, urgencia y estado
- Cambio de estado inline (Recibida → En revisión → Resuelta)
- Click en una fila navega al detalle

### Módulo 3 — Detalle con Historial (`/solicitudes/:id`)

- Información completa de la solicitud
- Panel de cambio de estado
- Timeline cronológico de todos los cambios de estado con timestamps

---

## API Endpoints

| Método | URL | Descripción |
|--------|-----|-------------|
| GET | `/api/health` | Health check + DB connection |
| GET | `/api/areas` | Lista de áreas |
| GET | `/api/tipos-solicitud` | Lista de tipos de solicitud |
| GET | `/api/solicitudes` | Lista de solicitudes (con filtros) |
| GET | `/api/solicitudes/:id` | Detalle de una solicitud |
| POST | `/api/solicitudes` | Crear nueva solicitud |
| PATCH | `/api/solicitudes/:id/estado` | Cambiar estado |
| GET | `/api/solicitudes/:id/historial` | Historial de cambios |

### Filtros disponibles para GET /api/solicitudes

```
?tipo_solicitud_id=1
?urgencia=Alta
?estado=Recibida
```

### Body para POST /api/solicitudes

```json
{
  "tipo_solicitud_id": 1,
  "titulo": "Soporte técnico - Juan Pérez",
  "descripcion": "Descripción detallada...",
  "urgencia": "Alta",
  "solicitante": "Juan Pérez",
  "area_solicitante_id": 2
}
```

### Body para PATCH /api/solicitudes/:id/estado

```json
{
  "estado": "En revisión",
  "comentario": "Se asignó al equipo técnico"
}
```

---

## Tablas de base de datos

| Tabla | Descripción |
|-------|-------------|
| `reto_c.areas` | Áreas de la empresa |
| `reto_c.tipos_solicitud` | Tipos de solicitudes (catálogo) |
| `reto_c.solicitudes` | Solicitudes registradas |
| `reto_c.historial_solicitudes` | Historial de cambios de estado |

---

## Build de producción del frontend

```bash
cd frontend
npm run build
```

Los archivos se generan en `frontend/dist/`.
