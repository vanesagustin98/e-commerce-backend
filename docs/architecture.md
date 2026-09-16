# Arquitectura del sistema — Marketplace E-commerce

Este documento reúne la documentación arquitectónica del proyecto, modelada con el enfoque C4 (Contexto, Contenedores, Componentes) más vistas complementarias de infraestructura, casos de uso, flujo transaccional y modelo de datos.

La documentación fue diseñada en [Isoflow](https://isoflow.io) y se mantiene en fase de diseño: las decisiones aún no definidas se marcan explícitamente como **"Decisión propuesta"** o **"Pendiente"**, en lugar de asumirse como ya implementadas.

## Índice de vistas

| Vista | Pregunta que responde |
|---|---|
| [01 — Contexto del sistema (C1)](#01--contexto-del-sistema-c1) | ¿Quién usa el sistema y para qué? |
| [02 — Contenedores (C2)](#02--contenedores-de-marketplace-platform-c2) | ¿Qué aplicaciones y bases de datos forman el sistema? |
| [03 — Componentes de Marketplace API (C3)](#03--componentes-de-marketplace-api-c3) | ¿Cómo está organizada internamente la API? |
| [04 — Infraestructura y despliegue](#04--infraestructura-y-despliegue) | ¿Cómo se ejecuta, prueba y despliega el proyecto? |
| [05 — Casos de uso](#05--casos-de-uso) | ¿Qué funcionalidades ofrece el sistema? |
| [06 — Flujo de compra y creación de orden](#06--flujo-de-compra-y-creación-de-orden) | ¿Cómo protegemos el inventario y creamos una orden válida? |
| [07 — Modelo de datos / ERD](#07--modelo-de-datos--erd) | ¿Cómo se estructura y relaciona la información? |

---

## Objetivo del proyecto

Marketplace de comercio electrónico que conecta compradores con vendedores mediante un catálogo de productos categorizado, gestión de inventario, carrito de compra y creación de órdenes, con roles de comprador, vendedor y administrador.

Proyecto de portafolio construido con Node.js, TypeScript, React, PostgreSQL, Prisma ORM, Docker y GitHub Actions. La arquitectura se mantiene deliberadamente simple: sin microservicios, sin colas de mensajes ni infraestructura adicional, salvo que exista una razón explícita para introducirla.

---

## 01 — Contexto del sistema (C1)

**Pregunta:** ¿Quién usa el sistema y para qué?

![Vista 01 — Contexto del sistema](./diagrams/01-contexto.png)[](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmtx679b701101qti83thu8rp/c4/views/ecc70903-ebb8-4c51-b554-53aea2f56c44)

**Elementos:** Comprador, Vendedor, Administrador, Marketplace Platform.

---

## 02 — Contenedores de Marketplace Platform (C2)

**Pregunta:** ¿Qué aplicaciones y bases de datos forman el sistema?

![Vista 02 — Contenedores](./diagrams/02-contenedores.png)[](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmtx679b701101qti83thu8rp/c4/views/ecc70903-ebb8-4c51-b554-53aea2f56c44)

**Elementos:** Frontend Web (React + TypeScript), Marketplace API (Node.js + TypeScript + Express), PostgreSQL Database.

---

## 03 — Componentes de Marketplace API (C3)

**Pregunta:** ¿Cómo está organizada internamente la API?

![Vista 03 — Componentes de la API](./diagrams/03-componentes-api.png)[](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmtx679b701101qti83thu8rp/c4/views/ecc70903-ebb8-4c51-b554-53aea2f56c44)

**Elementos:** HTTP API Layer, Authentication and Authorization, Catalog and Inventory, Cart and Orders, Persistence Layer, Shared Infrastructure.

---

## 04 — Infraestructura y despliegue

**Pregunta:** ¿Cómo se ejecuta, prueba y despliega el proyecto?

![Vista 04 — Infraestructura y despliegue](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmu273vuc018k1qti5bm1np9c/c4/views/2798f0c0-a4a8-466e-a39b-41e5dfea81b0)

**Zonas:**

- **Desarrollo local** — Entorno de ejecución local orquestado con Docker Compose, usado por la desarrolladora para levantar y probar el sistema completo (frontend, API y base de datos) antes de subir cambios al repositorio. [](./diagrams/04-componentes-api-desarrollo-local-p1.png) [](./diagrams/04-componentes-api-desarrollo-local-p2.png)
- **CI/CD** — Pipeline de integración y despliegue continuo ejecutado en GitHub Actions. Se dispara al recibir cambios en el repositorio, valida el código mediante lint y pruebas automatizadas, construye la imagen Docker y publica el artefacto listo para desplegar. [](./diagrams/04-componentes-api-ci-cd.png)
- **Producción** — Entorno donde se ejecuta la versión desplegada del sistema, accesible para los usuarios finales. Expone la API mediante un contenedor productivo con endpoints de salud (health check) y logs estructurados, respaldado por una base de datos PostgreSQL administrada. [](./diagrams/04-componentes-api-produccion.png)

**Pendiente:**
- ¿El despliegue a producción es manual o automático?
- ¿PostgreSQL administrado corre como servicio gestionado (RDS/Supabase/Railway) o como contenedor propio?

---

## 05 — Casos de uso

**Pregunta:** ¿Qué funcionalidades ofrece el sistema?

![Vista 05 — Casos de uso](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmu26v37c018h1qti9jlixj0y/c4/views/61ce1283-57ec-4cf0-9657-f5c5959a47aa)

**Comprador:** explorar productos, filtrar productos, administrar carrito, crear órdenes, consultar estado de compras. [](./diagrams/05-casos-de-uso-comprador.png)

**Vendedor:** publicar productos, administrar publicaciones, gestionar inventario, gestionar órdenes relacionadas con sus productos. [](./diagrams/05-casos-de-uso-vendedor.png)

**Administrador:** aprobar vendedores, administrar categorías, moderar publicaciones, consultar información operativa. [](./diagrams/05-casos-de-uso-administrador.png)

**Pendiente:** ¿Se debe incluir un caso de uso explícito de "Registro/Login", o se mantiene implícito como precondición (ya cubierto por Authentication and Authorization en la vista 03)?

---

## 06 — Flujo de compra y creación de orden

**Pregunta:** ¿Cómo protegemos el inventario y creamos una orden válida?

![Vista 06 — Flujo de compra](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmu4l5vbw01ej1qtijshylux3/c4/views/12572bc0-966e-4da0-869d-dd0a74fc34a4)
[Parte 1](./diagrams/06-flujo-compra-p1.png)
[Parte 2](./diagrams/06-flujo-compra-p2.png)

**Flujo:** Producto → Carrito → Validación → Transacción → (Orden + Actualización de inventario, en paralelo) → Confirmación.

La creación de la orden y la actualización del inventario ocurren dentro de la misma transacción, garantizando consistencia. Los detalles de implementación (locking, isolation level, etc.) se definirán cuando exista código real que los justifique.

---

## 07 — Modelo de datos / ERD

**Pregunta:** ¿Cómo se estructura y relaciona la información?

![Vista 07 — Modelo de datos](./diagrams/07-erd.png) [](https://isoflow.io/project/cmtvz1ugf00z81qtiyevopnbc/version/cmu4nln8x01et1qtipw4z1ppr/c4/views/12572bc0-966e-4da0-869d-dd0a74fc34a4)

**Entidades:** User, SellerProfile, Category, ProductListing, Cart, CartItem, Order, OrderItem.

**Decisiones propuestas (pendientes de confirmar):**
- `User`–`Cart`: cardinalidad 1 a 0..1 (un comprador tiene a lo sumo un carrito activo).
- `OrderItem` guarda un snapshot del precio al momento de la compra, para que cambios futuros de precio no alteren órdenes ya creadas.

**Pendiente:**
- ¿`SellerProfile` requiere un campo de estado (pendiente/aprobado/rechazado) para reflejar la aprobación del administrador?
- Posible vista futura (08) para el ciclo de vida / estados de una orden.

---

## Reglas de diseño

- No se introducen microservicios, colas de mensajes ni infraestructura adicional sin una razón explícita.
- Las decisiones no definidas se marcan como "Decisión propuesta" o "Pendiente", nunca se asumen silenciosamente.
- No se documenta a nivel de clases, métodos o queries concretas mientras el proyecto esté en fase de diseño (sin C4: Code todavía).
