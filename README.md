Fulfillment Service

ShopScale · Equipo 5 · Primavera 2026
Benemérita Universidad Autónoma de Puebla — Facultad de Ciencias de la Computación

---

¿Qué es esto?

Microservicio encargado de la logística, generación de envíos, rastreo de paquetes y notificaciones al cliente dentro de la plataforma ShopScale.

Este servicio se encarga de:

- crear envíos después de una compra exitosa
- asignar paquetería
- generar números de guía
- actualizar estados del envío
- enviar notificaciones al cliente

Todo esto usando procesamiento asíncrono para no bloquear el flujo principal de compra.

---

Tecnologías utilizadas

Capa| Tecnología
Framework| FastAPI (Python)
Base de datos| PostgreSQL con Supabase
ORM| SQLAlchemy
Migraciones| Alembic
Driver DB| psycopg2-binary
Procesos async| BackgroundTasks

---

Contrato API

El contrato OpenAPI 3.0 completo se encuentra en:

docs/openapi.yaml

Puede visualizarse en Swagger Editor.

---

Responsabilidades del dominio

Este servicio sí maneja:

- creación de envíos
- generación de guía
- rastreo de paquetes
- actualización de estados
- notificaciones al cliente

Este servicio NO maneja:

- pagos (Equipo 4)
- carrito de compras (Equipo 3)
- inventario (Equipo 2)
- autenticación (Equipo 1)

---

Decisiones importantes

Procesamiento asíncrono

La generación de guías y envío de notificaciones no bloquean la confirmación de compra.

Validación de envíos

Antes de crear un envío se valida:

- existencia del pedido
- dirección válida
- disponibilidad de envío

Control de estados

No se permiten transiciones inválidas como:

Entregado → En proceso

Solo cambios coherentes.