# Frontend Despacho — Innovatech Chile

Frontend desarrollado con **React + Vite** para la gestión de despachos de Innovatech Chile. Permite visualizar, registrar y administrar los despachos de la empresa a través de una interfaz web moderna y responsiva.

---

## Tecnologías

- React 18
- Vite
- Docker / Nginx

---

## Requisitos previos

- [Docker](https://www.docker.com/) y Docker Compose instalados

---

## Variables de entorno

Copia el archivo `.env.example` como `.env` en la raíz del proyecto y completa los valores:

```bash
cp .env.example .env
```

| Variable | Descripción |
|---|---|
| `VITE_API_URL` | URL base de la API de despachos (ej: `http://<EC2_HOST>:8081`) |

---

## Correr localmente

```bash
docker-compose up --build
```

La aplicación estará disponible en [http://localhost:80](http://localhost:80).

---

## Estructura Docker

El proyecto usa un **Dockerfile multi-stage**:

- **Stage 1** — Node 20 Alpine compila el proyecto con `npm run build`
- **Stage 2** — Nginx Alpine sirve los archivos estáticos del directorio `dist/`

El archivo `nginx.conf` configura el proxy inverso hacia el backend y el fallback a `index.html` para el enrutamiento SPA.

---

## CI/CD — GitHub Actions

El pipeline se encuentra en `.github/workflows/deploy.yml` y se activa automáticamente con cada `push` a la rama `deploy`.

### Flujo del pipeline

```
Push a rama deploy
       │
       ▼
Checkout del código
       │
       ▼
Login a Docker Hub
       │
       ▼
Build y push de imagen → DOCKERHUB_USERNAME/frontend-despacho:latest
       │
       ▼
SSH a EC2 pública
       │
       ▼
docker pull + docker stop/rm + docker run
```

### Secrets requeridos en GitHub

| Secret | Descripción |
|---|---|
| `DOCKERHUB_USERNAME` | Usuario de Docker Hub |
| `DOCKERHUB_TOKEN` | Token de acceso de Docker Hub |
| `EC2_FRONTEND_HOST` | IP pública de la instancia EC2 |
| `EC2_SSH_KEY` | Clave privada SSH para conectarse a EC2 |

---

## Infraestructura

La aplicación se despliega en una instancia **AWS EC2 pública**, accesible directamente desde internet en el puerto `80`. El contenedor se ejecuta con `--restart unless-stopped` para garantizar disponibilidad ante reinicios del servidor.
