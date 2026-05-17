# Nexora Agent — Dashboard de Control Inteligente

![Deploy Pages](https://github.com/ZombieGamessYT/NexLabs-Agent/actions/workflows/deploy-pages.yml/badge.svg)
![Deploy Vercel](https://github.com/ZombieGamessYT/NexLabs-Agent/actions/workflows/deploy-vercel.yml/badge.svg)
![Version](https://img.shields.io/badge/version-2.1.0-blue)
![Stack](https://img.shields.io/badge/stack-React%20%2B%20Node.js-6366f1)
![License](https://img.shields.io/badge/license-MIT-green)

<div align="center">

![Nexora Agent Dashboard](https://raw.githubusercontent.com/ZombieGamessYT/NexLabs-Agent/main/assets/dashboard-preview.png)

**Dashboard de Control Inteligente para gestión de agentes AI**

[🌐 Ver en Vivo](https://zombiegamessyt.github.io/NexLabs-Agent/) • [📦 Repositorio](https://github.com/ZombieGamessYT/NexLabs-Agent) • [📖 API Docs](docs/api-services.md) • [🚀 Deploy](docs/deployment-guide.md)

</div>

---

## 📋 Descripción

**Nexora Agent** es un dashboard de administración avanzado para gestión de agentes AI, monitoreo de sistemas, análisis de datos y generación de reportes. Diseñado con un estilo futurista dark-mode, interfaz responsiva y arquitectura escalable.

### 🎯 Funcionalidades
- 🔐 **Autenticación JWT** — Login seguro con sesión persistente
- 🤖 **Gestión de Agentes** — CRUD completo, 12 agentes, filtros por estado
- 📊 **Analíticas** — Gráficos de actividad, CPU, RAM, latencia, tasa de éxito
- 📋 **Logs en Tiempo Real** — Filtros INFO / WARN / ERROR con exportación
- 📄 **Reportes PDF** — Generación y descarga de reportes de rendimiento
- 👥 **Panel de Admin** — Gestión de usuarios y permisos por rol
- 🌙 **Modo Oscuro** — Toggle dark/light mode
- 📱 **Responsivo** — Mobile-first, sidebar colapsable

---

## 🚀 Stack Tecnológico

| Capa | Tecnología | Versión |
|------|-----------|---------|
| Frontend | React | 18.x |
| Estilos | TailwindCSS | 3.x |
| Gráficos | Chart.js | 4.x |
| Backend | Node.js + Express | 20.x / 4.x |
| Auth | JWT + bcrypt | — |
| Base de Datos | PostgreSQL + Prisma ORM | 15.x / 5.x |
| Cache | Redis | 7.x |
| Despliegue | Docker + Docker Compose | — |

---

## ⚡ Inicio Rápido

```bash
# Clonar el repositorio
git clone https://github.com/ZombieGamessYT/NexLabs-Agent.git
cd NexLabs-Agent

# Ver el dashboard (sin instalación)
open nexora-dashboard.html

# Con Docker (producción)
cp backend/.env.example backend/.env
docker-compose up -d
```

🔑 **Demo:** `admin@nexora.ai` / `nexora2025`

🌐 **En vivo:** [zombiegamessyt.github.io/NexLabs-Agent](https://zombiegamessyt.github.io/NexLabs-Agent/)

---

## 📁 Estructura del Repositorio

```
NexLabs-Agent/
├── nexora-dashboard.html       # Dashboard UI completo
├── vercel.json                 # Config deploy Vercel
├── .gitignore
├── assets/
│   └── dashboard-preview.png  # Preview del dashboard
└── docs/
    ├── api-services.md         # 18 endpoints REST
    ├── architecture.md         # Arquitectura del sistema
    └── deployment-guide.md     # Guía de despliegue
```

---

## ⚙️ CI/CD — GitHub Actions

| Workflow | Trigger | Destino |
|----------|---------|---------|
| `deploy-pages.yml` | Push a `main` | GitHub Pages |
| `deploy-vercel.yml` | Push a `main` / PR | Vercel Production |

> Para Vercel: agrega `VERCEL_TOKEN` en **Settings → Secrets → Actions**

---

## 📖 Documentación

| Documento | Descripción |
|-----------|-------------|
| [API Services](docs/api-services.md) | 18 endpoints REST + WebSocket |
| [Architecture](docs/architecture.md) | Componentes y flujo de datos |
| [Deployment Guide](docs/deployment-guide.md) | Docker · Vercel · Railway · AWS |

---

## 📄 Licencia

MIT © 2025 Nexora Technologies · Hecho con ❤️ usando CREAO
