
# Nexora Agent — Dashboard de Control Inteligente

![Version](https://img.shields.io/badge/version-2.1.0-blue) ![Stack](https://img.shields.io/badge/stack-React%20%2B%20Node.js-6366f1) ![License](https://img.shields.io/badge/license-MIT-green)

## 📋 Descripción

**Nexora Agent** es un dashboard de administración avanzado para gestión de agentes AI, monitoreo de sistemas, análisis de datos y generación de reportes. Diseñado con un estilo futurista dark-mode, interface responsiva y arquitectura escalable.

---

## 🚀 Stack Tecnológico

| Capa | Tecnología | Versión |
|------|-----------|---------|
| Frontend | React | 18.x |
| Estilos | TailwindCSS | 3.x |
| Gráficos | Chart.js | 4.x |
| Backend | Node.js + Express | 20.x / 4.x |
| Autenticación | JWT + bcrypt | - |
| Base de Datos | PostgreSQL + Prisma ORM | 15.x / 5.x |
| Cache | Redis | 7.x |
| Despliegue | Docker + Docker Compose | - |

---

## 📁 Estructura del Proyecto

```
nexora-agent/
├── frontend/                    # React App
│   ├── src/
│   │   ├── components/          # Componentes reutilizables
│   │   │   ├── ui/              # Botones, Cards, Badges, Inputs
│   │   │   ├── charts/          # ActivityChart, DonutChart, BarChart
│   │   │   ├── layout/          # Sidebar, Topbar, PageWrapper
│   │   │   └── modals/          # NewAgentModal, ConfirmModal
│   │   ├── pages/               # Vistas principales
│   │   │   ├── Overview.jsx     # Resumen general
│   │   │   ├── Agents.jsx       # Gestión de agentes
│   │   │   ├── Analytics.jsx    # Analíticas avanzadas
│   │   │   ├── Logs.jsx         # Logs en tiempo real
│   │   │   ├── Reports.jsx      # Generación de reportes
│   │   │   ├── Admin.jsx        # Panel de administración
│   │   │   └── Settings.jsx     # Configuración
│   │   ├── hooks/               # Custom hooks
│   │   │   ├── useAgents.js     # Datos de agentes con polling
│   │   │   ├── useAuth.js       # Estado de autenticación
│   │   │   └── useTheme.js      # Modo oscuro/claro
│   │   ├── services/            # Llamadas a la API
│   │   │   ├── api.js           # Cliente Axios configurado
│   │   │   ├── agents.js        # CRUD de agentes
│   │   │   └── reports.js       # Generación de reportes
│   │   ├── context/             # React Context
│   │   │   ├── AuthContext.jsx
│   │   │   └── ThemeContext.jsx
│   │   ├── utils/               # Utilidades
│   │   │   ├── formatters.js    # Formateo de números, fechas
│   │   │   └── exportPDF.js     # Lógica de exportación
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── public/
│   ├── tailwind.config.js
│   ├── vite.config.js
│   └── package.json
│
├── backend/                     # Node.js API
│   ├── src/
│   │   ├── controllers/         # Lógica de endpoints
│   │   │   ├── agentController.js
│   │   │   ├── authController.js
│   │   │   ├── logsController.js
│   │   │   ├── reportsController.js
│   │   │   └── usersController.js
│   │   ├── middleware/          # Middlewares Express
│   │   │   ├── auth.js          # Verificación JWT
│   │   │   ├── rateLimit.js     # Rate limiting
│   │   │   ├── cors.js          # CORS configurado
│   │   │   └── errorHandler.js  # Manejo global de errores
│   │   ├── models/              # Modelos Prisma
│   │   │   ├── Agent.js
│   │   │   ├── User.js
│   │   │   ├── Log.js
│   │   │   └── Report.js
│   │   ├── routes/              # Definición de rutas
│   │   │   ├── agents.js
│   │   │   ├── auth.js
│   │   │   ├── logs.js
│   │   │   ├── reports.js
│   │   │   └── users.js
│   │   ├── services/            # Lógica de negocio
│   │   │   ├── agentService.js
│   │   │   ├── pdfService.js    # Generación de PDFs
│   │   │   └── emailService.js  # Notificaciones
│   │   ├── utils/
│   │   │   ├── logger.js        # Winston logger
│   │   │   └── validators.js    # Validación de inputs (Zod)
│   │   └── app.js
│   ├── prisma/
│   │   ├── schema.prisma        # Esquema de base de datos
│   │   └── migrations/
│   ├── .env.example
│   └── package.json
│
├── docker/
│   ├── Dockerfile.frontend
│   ├── Dockerfile.backend
│   └── nginx.conf
├── docker-compose.yml
└── README.md
```

---

## ⚡ Instalación Rápida

### Prerrequisitos
- Node.js 20+
- PostgreSQL 15+
- Redis 7+ (opcional, para cache)
- Docker (recomendado)

### Con Docker (recomendado)

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu-org/nexora-agent.git
cd nexora-agent

# 2. Configurar variables de entorno
cp backend/.env.example backend/.env
# Editar backend/.env con tus valores

# 3. Levantar todos los servicios
docker-compose up -d

# 4. Ejecutar migraciones
docker-compose exec backend npx prisma migrate deploy

# 5. Crear usuario admin inicial
docker-compose exec backend node scripts/seed.js
```

🌐 **Acceder en:** http://localhost:3000

---

### Sin Docker (desarrollo local)

```bash
# Backend
cd backend
npm install
cp .env.example .env       # Configura tu .env
npx prisma migrate dev
npm run dev                # Puerto 4000

# Frontend (nueva terminal)
cd frontend
npm install
npm run dev                # Puerto 3000
```

---

## 🔐 Variables de Entorno

### Backend (`.env`)

```env
# Servidor
NODE_ENV=development
PORT=4000
FRONTEND_URL=http://localhost:3000

# Base de Datos
DATABASE_URL=postgresql://user:password@localhost:5432/nexora_db

# JWT
JWT_SECRET=tu_super_secreto_aqui_min_32_chars
JWT_EXPIRES_IN=7d
JWT_REFRESH_EXPIRES_IN=30d

# Redis (opcional)
REDIS_URL=redis://localhost:6379

# Email (Nodemailer)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=tu@email.com
SMTP_PASS=tu_app_password

# PDF Export
PDF_STORAGE_PATH=./storage/reports

# Rate Limiting
RATE_LIMIT_WINDOW=15          # minutos
RATE_LIMIT_MAX_REQUESTS=100
```

### Frontend (`.env.local`)

```env
VITE_API_URL=http://localhost:4000/api/v1
VITE_APP_NAME=Nexora Agent
VITE_APP_VERSION=2.1.0
```

---

## 🧩 Componentes UI Principales

### `<AgentCard />`
Tarjeta de estado de un agente.
```jsx
<AgentCard
  name="Alpha-7 Analyzer"
  type="Análisis"
  status="active"         // "active" | "standby" | "error"
  tasks={342}
  successRate={99.1}
  latency="0.8s"
  onView={() => {}}
  onConfig={() => {}}
/>
```

### `<MetricCard />`
Tarjeta de métrica con tendencia.
```jsx
<MetricCard
  title="Agentes Activos"
  value="12"
  change="+12%"
  trend="up"             // "up" | "down"
  icon={<AgentIcon />}
  progress={72}
/>
```

### `<ActivityChart />`
Gráfico de líneas de actividad.
```jsx
<ActivityChart
  data={weeklyData}     // [{ day, tasks, errors }]
  height={200}
/>
```

### `<LogsViewer />`
Visor de logs en tiempo real.
```jsx
<LogsViewer
  logs={logsArray}      // [{ time, level, agent, msg }]
  levels={['INFO','WARN','ERROR']}
  maxHeight="500px"
  autoScroll={true}
/>
```

---

## 🗃️ Esquema de Base de Datos (Prisma)

```prisma
model User {
  id        String   @id @default(cuid())
  name      String
  email     String   @unique
  password  String
  role      Role     @default(VIEWER)
  active    Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  agents    Agent[]
}

model Agent {
  id          String   @id @default(cuid())
  name        String
  type        String
  status      AgentStatus @default(STANDBY)
  tasksTotal  Int      @default(0)
  tasksSuccess Int     @default(0)
  latencyAvg  Float    @default(0)
  config      Json?
  ownerId     String
  owner       User     @relation(fields:[ownerId], references:[id])
  logs        Log[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Log {
  id        String   @id @default(cuid())
  level     LogLevel
  message   String
  agentId   String?
  agent     Agent?   @relation(fields:[agentId], references:[id])
  metadata  Json?
  timestamp DateTime @default(now())
}

model Report {
  id        String   @id @default(cuid())
  name      String
  type      String
  filePath  String
  fileSize  Int
  createdBy String
  createdAt DateTime @default(now())
}

enum Role { SUPER_ADMIN OPERATOR VIEWER }
enum AgentStatus { ACTIVE STANDBY ERROR }
enum LogLevel { INFO WARN ERROR DEBUG }
```

---

## 🔧 Troubleshooting

| Problema | Causa | Solución |
|----------|-------|----------|
| `Cannot connect to database` | DATABASE_URL incorrecta | Verificar credenciales en `.env` |
| `JWT_SECRET too short` | Secreto < 32 chars | Usar al menos 32 caracteres |
| `Port 4000 already in use` | Puerto ocupado | `lsof -ti:4000 \| xargs kill` |
| `Prisma migration failed` | Schema desactualizado | `npx prisma migrate reset` |
| `CORS error en frontend` | FRONTEND_URL mal configurada | Revisar variable en backend `.env` |
| Charts no renderizando | Canvas node no encontrado | Asegurar que el DOM está listo antes de init |

---

## 📄 Licencia

MIT © 2025 Nexora Technologies
