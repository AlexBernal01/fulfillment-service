Servicio de Logística y Notificaciones

ShopScale · Equipo 5 · Primavera 2026
Benemérita Universidad Autónoma de Puebla — Facultad de Ciencias de la Computación

Qué es esto

Microservicio encargado de la logística y notificaciones dentro de ShopScale. Su función principal es crear envíos después de una compra exitosa, asignar una paquetería, generar el número de guía, permitir el rastreo del paquete y mantener actualizado el estado del envío.

También se encarga de enviar notificaciones al cliente sobre cambios importantes del pedido sin afectar el flujo principal de compra, utilizando procesamiento asíncrono.

Tecnologías utilizadas

Capa| Tecnología
Framework| FastAPI (Python)
Base de datos| PostgreSQL con Supabase
ORM| SQLAlchemy
Migraciones| Alembic
Driver DB| psycopg2-binary
Procesos asíncronos| BackgroundTasks

Estructura del proyecto

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

Configuración local

1. Clonar el repositorio y crear entorno virtual

git clone https://github.com/TU-USUARIO/fulfillmentService.git
cd fulfillmentService
python -m venv venv
source venv/bin/activate

# En Windows:
venv\Scripts\activate

2. Instalar dependencias

pip install -r requirements.txt

3. Configurar variables de entorno

Copiar ".env.example" a ".env" y colocar las credenciales de Supabase:

cp .env.example .env

Obtener "DATABASE_URL" desde:

Settings → Database → Connection string → URI

DATABASE_URL=postgresql://postgres:<password>@db.<project-ref>.supabase.co:5432/postgres

⚠️ Nunca subir ".env" al repositorio.

4. Ejecutar migraciones

alembic upgrade head

Esto crea las tablas principales:

- shipments
- addresses
- tracking_events
- notifications
- logs

5. Ejecutar el servidor

uvicorn app.main:app --reload

API disponible en:

http://localhost:8000

Documentación Swagger:

http://localhost:8000/docs

Migraciones de base de datos

# Aplicar migraciones pendientes
alembic upgrade head

# Crear nueva migración
alembic revision --autogenerate -m "describe_your_change"

# Regresar una migración
alembic downgrade -1

# Regresar todo
alembic downgrade base

Siempre revisar las migraciones antes de aplicarlas.

Contrato API

Especificación completa OpenAPI 3.0:

docs/openapi.yaml

Puede probarse directamente en Swagger Editor.

Para integración con otros equipos:

docs/INTEGRATION.md

Responsabilidades del dominio

Este servicio sí maneja| Este servicio NO maneja
Creación de envíos| Procesamiento de pagos (Equipo 4)
Asignación de paquetería| Carrito de compras (Equipo 3)
Generación de guía| Inventario de productos (Equipo 2)
Rastreo de paquetes| Autenticación (Equipo 1)
Notificaciones al cliente| Orquestación del checkout (Equipo 3)

Decisiones importantes de diseño

Procesamiento asíncrono

La generación de guías y el envío de notificaciones se manejan de forma asíncrona para no bloquear la confirmación de compra.

Validación de envíos

Antes de crear un envío se valida:

- que el pedido exista
- que la dirección sea válida
- que haya cobertura de envío

Consistencia del rastreo

Cada envío recibe un número de guía único y se guarda historial de eventos para permitir trazabilidad completa.

Control de estados

No se permiten cambios de estado inválidos como:

Entregado → En proceso

Solo se aceptan transiciones lógicas.

Seguridad en notificaciones

Si falla el envío de correo o notificación, el sistema guarda el intento para reintentar posteriormente.