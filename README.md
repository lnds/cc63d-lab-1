# CC63D - Lab 1: Incident Manager

Plataforma de gestión de incidentes — proyecto base del curso **CC63D — Arquitecturas de Servicios, DevOps, SRE y Cloud** (FCFM, Universidad de Chile, 2026).

## ¿Qué hace esta app?

Una herramienta de gestión de incidentes estilo PagerDuty / Grafana OnCall:

- Registrar **servicios** y sus SLOs
- Gestionar **turnos on-call** por servicio
- Crear, actualizar y resolver **incidentes** con timeline
- Notificación automática al on-call cuando se crea un incidente
- Redactar **post-mortems blameless**

## Estructura

```
.
├── app.py              # Monolito Flask
├── requirements.txt    # Dependencias Python
├── seed.sh             # Datos de ejemplo
└── static/             # Frontend SPA (HTML/CSS/JS vanilla)
```

**Tecnologías:** Python 3.12+, Flask 3.x, SQLite, frontend vanilla.

## Levantar la app

```bash
git clone https://github.com/lnds/cc63d-lab-1.git
cd cc63d-lab-1
pip install -r requirements.txt
python app.py
```

Abrir [http://localhost:8080](http://localhost:8080).

Para cargar datos de ejemplo:

```bash
bash seed.sh
```

## Endpoints

```
GET    /services
POST   /services
GET    /services/:id

GET    /oncall
POST   /oncall
GET    /oncall/current/:service_id

GET    /incidents               # ?status=open|investigating|mitigated|resolved
POST   /incidents
GET    /incidents/:id
PATCH  /incidents/:id

POST   /postmortems
GET    /postmortems
GET    /postmortems/:id
```

## Licencia

Material educativo del curso CC63D.
