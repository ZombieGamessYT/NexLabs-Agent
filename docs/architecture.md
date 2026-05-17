# Nexora Agent — Arquitectura del Sistema

## Stack
- **Frontend:** React 18 + TailwindCSS + Chart.js
- **Backend:** Node.js + Express + Prisma ORM
- **Base de Datos:** PostgreSQL 15
- **Cache:** Redis 7
- **Auth:** JWT + bcrypt
- **Deploy:** Docker + Nginx

## Páginas del Dashboard
| Página | Descripción |
|--------|-------------|
| Overview | Métricas generales, gráficos de actividad, top agentes |
| Agents | Gestión CRUD de agentes, filtros por estado |
| Analytics | Analíticas avanzadas, CPU, RAM, tendencias |
| Logs | Logs en tiempo real con filtros por nivel |
| Reports | Generación y descarga de reportes PDF |
| Admin | Gestión de usuarios y roles |
| Settings | Configuración del sistema y API keys |

## Endpoints API
Ver [docs/api-services.md](docs/api-services.md) — 18 endpoints documentados.

## Despliegue
Ver [docs/deployment-guide.md](docs/deployment-guide.md) — Docker, Vercel, Railway, AWS.
