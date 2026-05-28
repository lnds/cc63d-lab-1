# CC63D - Lab 1: Incident Manager

Plataforma de gestión de incidentes — proyecto base del curso **CC63D — Arquitecturas de Servicios, DevOps, SRE y Cloud** (FCFM, Universidad de Chile, 2026).

Este es el **monolito de partida** que evolucionaremos clase a clase durante el curso:
contenedor → docker-compose con PostgreSQL → Kubernetes → Cloud Run.

---

## ¿Qué hace esta app?

Una herramienta de gestión de incidentes estilo PagerDuty / Grafana OnCall:

- Registrar **servicios** y sus SLOs (availability, latency, etc.)
- Gestionar **turnos on-call** por servicio
- Crear, actualizar y resolver **incidentes** con timeline completo
- Notificación automática al on-call cuando se crea un incidente
- Redactar **post-mortems blameless** con root cause, impact y action items

> Construimos una herramienta SRE mientras aprendemos SRE.

---

## Estructura del proyecto

```
.
├── app.py              # Monolito Flask con todos los endpoints
├── requirements.txt    # Dependencias Python
├── seed.sh             # Datos de ejemplo + flujo completo de un incidente
└── static/             # Frontend SPA en HTML/CSS/JS vanilla
    ├── index.html
    ├── app.js
    └── style.css
```

**Tecnologías:**
- Python 3.12+
- Flask 3.x
- SQLite (la BD se crea en `data/incidents.db` al primer arranque)
- Frontend vanilla (sin frameworks, sin build step)

---

## Levantar la app localmente

```bash
git clone https://github.com/lnds/cc63d-lab-1.git
cd cc63d-lab-1
pip install -r requirements.txt
python app.py
```

Abrir [http://localhost:8080](http://localhost:8080).

Para cargar datos de ejemplo (3 servicios, 2 turnos on-call, 1 incidente resuelto + post-mortem):

```bash
bash seed.sh
```

---

## Endpoints de la API

### Servicios

```
GET    /services                 # Lista todos los servicios
POST   /services                 # Crea un servicio
GET    /services/:id             # Detalle de un servicio
```

### Turnos On-Call

```
GET    /oncall                   # Lista todos los turnos
POST   /oncall                   # Crea un turno
GET    /oncall/current/:service_id   # Quién está de turno ahora
```

### Incidentes

```
GET    /incidents                # Lista todos (filtro: ?status=open)
POST   /incidents                # Crea un incidente (notifica al on-call)
GET    /incidents/:id            # Detalle + timeline completo
PATCH  /incidents/:id            # Actualiza status / agrega evento al timeline
```

### Post-Mortems

```
POST   /postmortems              # Crea un post-mortem (incident debe estar resuelto)
GET    /postmortems              # Lista todos
GET    /postmortems/:id          # Detalle
```

---

## Lab 1 — Containerizar el monolito

**Objetivo:** convertir esta app en un contenedor Docker reproducible.

### Lo que debes hacer

1. **Escribir un `Dockerfile`** que:
   - Use una imagen base de Python (`python:3.12-slim` o equivalente)
   - Instale las dependencias de `requirements.txt`
   - Copie el código de la app
   - Exponga el puerto 8080
   - Defina el comando de arranque

2. **Construir la imagen:**
   ```bash
   docker build -t incident-manager:v1 .
   ```

3. **Ejecutar el contenedor:**
   ```bash
   docker run -p 8080:8080 incident-manager:v1
   ```
   Verificar que la app responde en `http://localhost:8080`.

4. **Probar persistencia de datos:**
   - Crear servicios y un incidente desde la UI
   - Detener el contenedor (`docker stop`)
   - Volver a levantarlo: **los datos se pierden** (SQLite vive dentro del container)
   - Solucionar con un **volumen montado**:
     ```bash
     docker run -p 8080:8080 -v $(pwd)/data:/app/data incident-manager:v1
     ```

5. **Explorar la imagen:**
   - `docker images` → tamaño de la imagen
   - `docker history incident-manager:v1` → capas
   - **Optimizar:** reordenar el `COPY` para aprovechar el cache de capas (copiar `requirements.txt` antes que el código)

### Entregable

- `Dockerfile` funcional
- Captura de pantalla / breve descripción del flujo:
  1. App corriendo en el contenedor
  2. Demostración del problema de persistencia
  3. Solución con volumen
  4. Tamaño de la imagen antes y después de optimizar

### Reflexión (para discutir en clase)

- ¿Qué pasa con SQLite cuando escala? ¿Por qué necesitaremos PostgreSQL en la siguiente clase?
- ¿Cómo gestionas la configuración (URL de DB, secrets)? ¿Variables de entorno?
- ¿Qué problemas trae meter la base de datos en el mismo contenedor que la app?

> Estos problemas los resolveremos en la **Clase 3** con `docker-compose`, PostgreSQL y migraciones con Flyway.

---

## Referencias

- [Docker — Get Started](https://docs.docker.com/get-started/)
- [12-Factor App](https://12factor.net/) — especialmente Factor III (Config) y Factor X (Dev/Prod parity)
- [Flask documentation](https://flask.palletsprojects.com/)

---

## Licencia

Material educativo del curso CC63D. Uso libre para estudiantes y docentes.
