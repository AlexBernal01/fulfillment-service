Fulfillment Service

ShopScale · Team 5 · Spring 2026
Benemérita Universidad Autónoma de Puebla — Facultad de Ciencias de la Computación

What this is

Microservicio encargado de la logística y notificaciones dentro de ShopScale. Su función principal es crear envíos después de una compra exitosa, asignar una paquetería, generar el número de guía, permitir el rastreo del paquete y mantener actualizado el estado del envío.

También se encarga de enviar notificaciones al cliente sobre cambios importantes del pedido sin afectar el flujo principal de compra, usando procesamiento asíncrono.

Tech stack

Layer| Technology
Framework| FastAPI (Python)
Database| PostgreSQL via Supabase
ORM| SQLAlchemy
Migrations| Alembic
DB Driver| psycopg2-binary
Async Tasks| BackgroundTasks

Project structure

fulfillmentService/
├── alembic.ini
├── requirements.txt
├── .env.example
├── docs/
│   ├── openapi.yaml        ← Contrato completo de la API
│   └── INTEGRATION.md      ← Guía de integración con otros equipos
├── migrations/
│   ├── env.py
│   └── versions/
│       └── 0001_initial_schema.py
└── app/
    ├── main.py
    ├── models/
    │   ├── __init__.py
    │   └── models.py
    ├── api/
    │   └── v1/
    │       ├── shipments.py
    │       ├── tracking.py
    │       └── notifications.py
    ├── config/
    ├── controllers/
    ├── middleware/
    └── routes/

Local setup

1. Clone and create virtual environment

git clone https://github.com/TU-USUARIO/fulfillmentService.git
cd fulfillmentService
python -m venv venv
source venv/bin/activate

# En Windows:
venv\Scripts\activate

2. Install dependencies

pip install -r requirements.txt

3. Configure environment

Copiar ".env.example" a ".env" y colocar las credenciales de Supabase:

cp .env.example .env

Obtener "DATABASE_URL" desde:

Settings → Database → Connection string → URI

DATABASE_URL=postgresql://postgres:<password>@db.<project-ref>.supabase.co:5432/postgres

⚠️ Nunca subir ".env" al repositorio.

4. Run migrations

alembic upgrade head

Esto crea las tablas principales:

- shipments
- addresses
- tracking_events
- notifications
- logs

5. Run the server

uvicorn app.main:app --reload

API disponible en:

http://localhost:8000

Swagger docs:

http://localhost:8000/docs

Database migrations

# Aplicar migraciones pendientes
alembic upgrade head

# Crear nueva migración
alembic revision --autogenerate -m "describe_your_change"

# Regresar una migración
alembic downgrade -1

# Regresar todo
alembic downgrade base

Siempre revisar las migraciones antes de aplicarlas.

API contract

Especificación completa OpenAPI 3.0:

docs/openapi.yaml

Puede probarse directamente en Swagger Editor.

Para integración con otros equipos:

docs/INTEGRATION.md

Domain responsibilities

Owned by this service| NOT owned by this service
Creación de envíos| Payment processing (Team 4)
Asignación de paquetería| Cart lifecycle (Team 3)
Generación de guía| Product inventory (Team 2)
Rastreo de paquetes| Authentication (Team 1)
Notificaciones al cliente| Checkout orchestration (Team 3)

Key design decisions

Async Processing

La generación de guías y el envío de notificaciones se manejan de forma asíncrona para no bloquear la confirmación de compra.

Shipment Validation

Antes de crear un envío se valida:

- que el pedido exista
- que la dirección sea válida
- que haya cobertura de envío

Tracking Consistency

Cada envío recibe un número de guía único y se guarda historial de eventos para rastreo.

Status Control

No se permiten cambios de estado inválidos como:

Entregado → En proceso

Solo se aceptan transiciones lógicas.

Notification Safety

Si falla el envío de correo o notificación, el sistema guarda el intento para reintentar después.