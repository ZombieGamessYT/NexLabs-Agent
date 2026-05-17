
# Nexora Agent — API Services Documentation

**Base URL:** `https://api.nexora.ai/v1`  
**Autenticación:** Bearer JWT Token  
**Content-Type:** `application/json`

---

## 🔐 Autenticación

### POST `/auth/login`
Iniciar sesión y obtener tokens JWT.

**Request:**
```json
{
  "email": "admin@nexora.ai",
  "password": "tu_password"
}
```
**Response 200:**
```json
{
  "success": true,
  "data": {
    "user": { "id": "usr_abc123", "name": "Admin Nexora", "email": "admin@nexora.ai", "role": "SUPER_ADMIN" },
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
    "expiresIn": 604800
  }
}
```

### POST `/auth/refresh`
Renovar access token usando refresh token.

**Request:**
```json
{ "refreshToken": "eyJhbGciOiJIUzI1NiIs..." }
```

### POST `/auth/logout`
Invalidar tokens del usuario actual.

### GET `/auth/me`
Obtener perfil del usuario autenticado.

---

## 🤖 Agentes

### GET `/agents`
Listar todos los agentes con filtros opcionales.

**Query Params:**
| Param | Tipo | Descripción |
|-------|------|-------------|
| `status` | string | `active`, `standby`, `error` |
| `type` | string | Tipo de agente |
| `page` | number | Página (default: 1) |
| `limit` | number | Items por página (default: 20, max: 100) |
| `sort` | string | `tasks`, `success`, `latency`, `name` |
| `order` | string | `asc`, `desc` |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "agents": [
      {
        "id": "agt_alpha7",
        "name": "Alpha-7 Analyzer",
        "type": "Análisis",
        "status": "active",
        "tasksTotal": 342,
        "tasksSuccess": 339,
        "successRate": 99.1,
        "latencyAvg": 0.8,
        "config": { "maxRetries": 3, "timeout": 5000 },
        "createdAt": "2025-01-15T10:30:00Z",
        "updatedAt": "2025-05-17T14:32:00Z"
      }
    ],
    "pagination": { "page": 1, "limit": 20, "total": 12, "pages": 1 }
  }
}
```

### POST `/agents`
Crear un nuevo agente.

**Request:**
```json
{
  "name": "Sigma DataBot",
  "type": "ETL",
  "config": {
    "maxRetries": 3,
    "timeout": 10000,
    "schedule": "0 */6 * * *",
    "webhookUrl": "https://hooks.example.com/sigma"
  }
}
```

### GET `/agents/:id`
Obtener detalles completos de un agente.

### PUT `/agents/:id`
Actualizar configuración de un agente.

### DELETE `/agents/:id`
Eliminar un agente (requiere rol SUPER_ADMIN).

### POST `/agents/:id/start`
Iniciar ejecución manual de un agente.

**Response 200:**
```json
{
  "success": true,
  "data": { "taskId": "tsk_xyz789", "agentId": "agt_alpha7", "status": "running", "startedAt": "2025-05-17T14:33:00Z" }
}
```

### POST `/agents/:id/stop`
Detener un agente en ejecución.

### GET `/agents/:id/stats`
Obtener estadísticas históricas del agente.

**Query Params:** `period` (`24h`, `7d`, `30d`, `90d`)

**Response 200:**
```json
{
  "success": true,
  "data": {
    "period": "7d",
    "totalTasks": 342,
    "successRate": 99.1,
    "avgLatency": 0.8,
    "errorCount": 3,
    "timeline": [
      { "date": "2025-05-11", "tasks": 48, "errors": 0, "avgLatency": 0.7 },
      { "date": "2025-05-12", "tasks": 52, "errors": 1, "avgLatency": 0.9 }
    ]
  }
}
```

---

## 📋 Logs

### GET `/logs`
Obtener logs del sistema con filtros.

**Query Params:**
| Param | Tipo | Descripción |
|-------|------|-------------|
| `level` | string | `INFO`, `WARN`, `ERROR`, `DEBUG` |
| `agentId` | string | Filtrar por agente |
| `from` | ISO date | Fecha inicio |
| `to` | ISO date | Fecha fin |
| `page` | number | Paginación |
| `limit` | number | (default: 50, max: 500) |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "logs": [
      {
        "id": "log_001",
        "level": "ERROR",
        "message": "Connection timeout after 5000ms",
        "agent": { "id": "agt_mu12", "name": "Mu ErrorHandler" },
        "metadata": { "attempt": 2, "maxRetries": 3 },
        "timestamp": "2025-05-17T14:30:55Z"
      }
    ],
    "pagination": { "page": 1, "total": 2847 }
  }
}
```

### GET `/logs/export`
Exportar logs como CSV o JSON.

**Query Params:** `format` (`csv`, `json`), `from`, `to`, `level`

**Response:** Archivo descargable

### DELETE `/logs/purge`
Purgar logs antiguos (requiere SUPER_ADMIN).

**Request:**
```json
{ "olderThanDays": 90 }
```

---

## 📊 Analíticas

### GET `/analytics/overview`
Métricas generales del sistema.

**Response 200:**
```json
{
  "success": true,
  "data": {
    "totalAgents": 12,
    "activeAgents": 8,
    "systemUptime": 98.7,
    "tasksCompleted": 1847,
    "avgLatency": 1.24,
    "errorRate": 0.003,
    "cpuUsage": 34.2,
    "memoryUsage": 58.7,
    "requestsPerMin": 247
  }
}
```

### GET `/analytics/timeseries`
Serie temporal de métricas.

**Query Params:** `metric` (`tasks`, `errors`, `latency`, `cpu`, `memory`), `period`, `interval` (`hour`, `day`, `week`)

### GET `/analytics/top-agents`
Agentes con mejor rendimiento.

**Query Params:** `metric` (`tasks`, `success_rate`, `latency`), `limit`

---

## 📄 Reportes

### GET `/reports`
Listar reportes generados.

### POST `/reports/generate`
Generar un nuevo reporte PDF.

**Request:**
```json
{
  "type": "performance",
  "title": "Reporte de Rendimiento — Mayo 2025",
  "period": { "from": "2025-05-01", "to": "2025-05-17" },
  "filters": { "agentIds": ["agt_alpha7", "agt_beta2"] },
  "includeCharts": true,
  "format": "pdf"
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "reportId": "rpt_abc456",
    "status": "generating",
    "estimatedTime": "15s",
    "webhookNotify": true
  }
}
```

### GET `/reports/:id`
Obtener estado y metadata de un reporte.

### GET `/reports/:id/download`
Descargar reporte en PDF.

**Response:** `application/pdf` stream

### DELETE `/reports/:id`
Eliminar un reporte.

---

## 👥 Usuarios (Admin)

### GET `/users`
Listar usuarios del sistema. *(SUPER_ADMIN only)*

### POST `/users`
Crear nuevo usuario.

**Request:**
```json
{
  "name": "María González",
  "email": "maria@nexora.ai",
  "password": "SecurePass123!",
  "role": "OPERATOR"
}
```

### PUT `/users/:id`
Actualizar usuario.

### PUT `/users/:id/password`
Cambiar contraseña de usuario.

### DELETE `/users/:id`
Eliminar usuario. *(SUPER_ADMIN only)*

### PUT `/users/:id/toggle-active`
Activar/desactivar usuario.

---

## ⚠️ Códigos de Error

| Código | Descripción |
|--------|-------------|
| `400` | Bad Request — parámetros inválidos |
| `401` | Unauthorized — token inválido o expirado |
| `403` | Forbidden — permisos insuficientes |
| `404` | Not Found — recurso no existe |
| `409` | Conflict — recurso ya existe (ej: email duplicado) |
| `429` | Too Many Requests — rate limit excedido |
| `500` | Internal Server Error |

**Formato de error estándar:**
```json
{
  "success": false,
  "error": {
    "code": "AGENT_NOT_FOUND",
    "message": "El agente con ID 'agt_xyz' no existe",
    "details": null
  }
}
```

---

## 🔗 WebSockets

**URL:** `wss://api.nexora.ai/v1/ws`

**Eventos disponibles:**
```json
// Suscribirse a logs en tiempo real
{ "event": "subscribe", "channel": "logs", "filters": { "level": "ERROR" } }

// Suscribirse a estado de agentes
{ "event": "subscribe", "channel": "agents:status" }

// Recibir actualizaciones
{ "event": "agent:status_changed", "data": { "agentId": "agt_mu12", "status": "error", "timestamp": "..." } }
{ "event": "log:new", "data": { "level": "ERROR", "message": "...", "agentId": "agt_mu12" } }
```
