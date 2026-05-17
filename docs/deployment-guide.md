
# Nexora Agent — Guía de Despliegue

## ☁️ Opciones de Despliegue

### Opción 1: Docker Compose (Recomendado para producción propia)

**`docker-compose.yml`**
```yaml
version: '3.9'
services:
  frontend:
    build:
      context: ./frontend
      dockerfile: ../docker/Dockerfile.frontend
    ports: ["3000:80"]
    environment:
      - VITE_API_URL=http://backend:4000/api/v1
    depends_on: [backend]

  backend:
    build:
      context: ./backend
      dockerfile: ../docker/Dockerfile.backend
    ports: ["4000:4000"]
    env_file: ./backend/.env
    depends_on: [postgres, redis]
    volumes:
      - ./storage:/app/storage

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: nexora_db
      POSTGRES_USER: nexora_user
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports: ["5432:5432"]

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
    volumes:
      - redis_data:/data

  nginx:
    image: nginx:alpine
    ports: ["80:80", "443:443"]
    volumes:
      - ./docker/nginx.conf:/etc/nginx/conf.d/default.conf
      - ./ssl:/etc/ssl/certs
    depends_on: [frontend, backend]

volumes:
  postgres_data:
  redis_data:
```

**Comandos:**
```bash
# Producción
docker-compose -f docker-compose.yml up -d --build

# Ver logs
docker-compose logs -f backend

# Escalar backend
docker-compose up -d --scale backend=3
```

---

### Opción 2: Vercel + Railway (Serverless, más fácil)

**Frontend → Vercel:**
```bash
# Instalar Vercel CLI
npm i -g vercel

# Deploy desde /frontend
cd frontend
vercel --prod

# Variables de entorno en Vercel Dashboard:
# VITE_API_URL = https://nexora-backend.railway.app/api/v1
```

**Backend → Railway:**
```bash
# Instalar Railway CLI
npm i -g @railway/cli

# Login y deploy
railway login
cd backend
railway init
railway up

# Agregar PostgreSQL en Railway Dashboard → Add Service → PostgreSQL
# Agregar Redis en Railway Dashboard → Add Service → Redis
# Variables de entorno se configuran automáticamente desde los servicios
```

---

### Opción 3: AWS / GCP / Azure

**AWS ECS + RDS:**
```bash
# Build y push imagen a ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com
docker build -t nexora-backend ./backend
docker tag nexora-backend:latest <account>.dkr.ecr.us-east-1.amazonaws.com/nexora-backend:latest
docker push <account>.dkr.ecr.us-east-1.amazonaws.com/nexora-backend:latest

# Crear cluster ECS (via console o Terraform)
# RDS PostgreSQL: db.t3.micro para staging, db.r5.large para producción
# ElastiCache Redis: cache.t3.micro
```

---

## 🐳 Dockerfiles

**`docker/Dockerfile.backend`**
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npx prisma generate

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app .
RUN addgroup -S nexora && adduser -S nexora -G nexora
USER nexora
EXPOSE 4000
CMD ["node", "src/app.js"]
```

**`docker/Dockerfile.frontend`**
```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY ../docker/nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

**`docker/nginx.conf`**
```nginx
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;

    # SPA routing
    location / {
        try_files $uri $uri/ /index.html;
    }

    # API proxy
    location /api/ {
        proxy_pass http://backend:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # WebSocket
    location /ws {
        proxy_pass http://backend:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # Gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
}
```

---

## ✅ Checklist Pre-Lanzamiento

### Seguridad
- [ ] `JWT_SECRET` tiene al menos 64 caracteres aleatorios
- [ ] Contraseñas hasheadas con bcrypt (rounds ≥ 12)
- [ ] HTTPS habilitado con certificado SSL válido
- [ ] Variables de entorno en sistema seguro (no en `.env` en producción)
- [ ] Rate limiting activo en todos los endpoints públicos
- [ ] CORS configurado solo para dominios autorizados
- [ ] Headers de seguridad: `helmet.js` activo
- [ ] SQL injection imposible (Prisma ORM protege by default)
- [ ] Logs no exponen datos sensibles (passwords, tokens)

### Base de Datos
- [ ] Migraciones ejecutadas en producción: `npx prisma migrate deploy`
- [ ] Backups automáticos configurados (mínimo diario)
- [ ] Índices creados para queries frecuentes
- [ ] Pool de conexiones configurado correctamente
- [ ] Usuario de DB con permisos mínimos necesarios

### Rendimiento
- [ ] Build del frontend minificado: `npm run build`
- [ ] Gzip habilitado en Nginx
- [ ] Cache de Redis activo para queries repetidas
- [ ] CDN configurado para assets estáticos
- [ ] Lazy loading de rutas en React (`React.lazy`)
- [ ] Imágenes optimizadas (WebP)

### Monitoreo
- [ ] Health check endpoint activo: `GET /health`
- [ ] Logging centralizado (Winston → archivo/CloudWatch)
- [ ] Alertas configuradas para errores críticos
- [ ] Uptime monitoring (UptimeRobot o similar)
- [ ] Error tracking (Sentry)

### CI/CD
- [ ] Pipeline de CI corriendo tests antes de deploy
- [ ] Variables de entorno en secrets del CI/CD
- [ ] Deploy automático en merge a `main`
- [ ] Rollback configurado en caso de fallo

---

## 🏃 Comandos de Mantenimiento

```bash
# Ver estado de contenedores
docker-compose ps

# Reiniciar un servicio
docker-compose restart backend

# Ejecutar migraciones en producción
docker-compose exec backend npx prisma migrate deploy

# Backup de base de datos
docker-compose exec postgres pg_dump -U nexora_user nexora_db > backup_$(date +%Y%m%d).sql

# Restaurar backup
docker-compose exec -T postgres psql -U nexora_user nexora_db < backup_20250517.sql

# Ver logs en tiempo real
docker-compose logs -f --tail=100 backend

# Limpiar contenedores y volúmenes (¡CUIDADO en producción!)
docker-compose down -v
```

---

## 📞 Soporte

- 📧 Email: support@nexora.ai
- 📖 Docs: https://docs.nexora.ai
- 🐛 Issues: https://github.com/nexora-ai/agent/issues
