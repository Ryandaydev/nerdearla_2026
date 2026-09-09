# ✈ NERDEARLA 2026 - APIs asíncronas y MCP con Python ✈

Este repositorio contiene el código utilizado en mi charla de **NERDEARLA 2026**.

El proyecto utiliza datos de viajes aéreos para demostrar cómo se puede usar Python asíncrono para construir servicios de datos modernos, incluyendo:

- una API REST asíncrona con FastAPI
- un SDK/cliente asíncrono en Python
- un servidor MCP construido con FastMCP
- flujos HTTP asíncronos compartidos usando HTTPX
- acceso a datos respaldado por PostgreSQL

El objetivo del proyecto no es construir una plataforma completa de viajes aéreos lista para producción. En cambio, proporciona un ejemplo realista para explorar cómo encaja la programación asíncrona en una arquitectura de API y MCP.

Mientras desarrollo proyectos como este, escribo sobre ellos en mi newsletter Tip Sheet:
[suscríbete a la newsletter Tip Sheet](https://tips.handsonapibook.com/).

Si quieres una introducción a la creación de APIs con FastAPI para IA y ciencia de datos en Python, consulta:
[Hands-on APIs for AI and Data Science: Python Development with FastAPI](https://handsonapibook.com).

---

## Temas de la charla

- Por qué la programación asíncrona es importante para cargas de trabajo de APIs e IA
- Cómo usar `async` / `await` en Python
- Cómo construir endpoints asíncronos con FastAPI
- Cómo llamar APIs de forma asíncrona con HTTPX
- Cómo reutilizar funcionalidad asíncrona entre diferentes capas de una aplicación
- Cómo exponer funcionalidad a agentes de IA mediante un servidor MCP
- Cómo entender dónde la concurrencia mejora el rendimiento y dónde no

---

## Descripción general de la arquitectura

![Arquitectura de NERDEARLA 2026](nerdearla_architecture.png)

El proyecto sigue una arquitectura por capas construida alrededor de una fuente compartida de datos de viajes aéreos.

La **Flights API** proporciona endpoints REST asíncronos sobre los datos subyacentes.

El **Air Travel SDK** proporciona funcionalidad reutilizable de cliente asíncrono para los consumidores de la API.

El **Air Travel MCP Server** reutiliza ese SDK para que los agentes de programación y asistentes de IA puedan acceder a la misma funcionalidad mediante herramientas MCP sin duplicar la lógica de integración con la API.

---

## Tecnologías utilizadas

- **Python** - lenguaje de programación principal
- **FastAPI** - framework para APIs asíncronas
- **HTTPX** - cliente HTTP asíncrono
- **FastMCP** - framework para construir servidores MCP
- **PostgreSQL** - base de datos relacional
- **asyncpg** - driver asíncrono para PostgreSQL
- **SQLAlchemy** - capa de acceso a datos
- **Docker** - entorno local de PostgreSQL

---

# Componentes principales

## Flights API

Una aplicación FastAPI que expone datos de viajes aéreos mediante endpoints REST.

La API demuestra el manejo asíncrono de solicitudes y el acceso asíncrono a la base de datos.

**Ruta en el repositorio:**

[`flights-api/`](./flights-api)

---

## Air Travel SDK

Un cliente Python reutilizable para la Flights API.

El cliente asíncrono demuestra cómo las aplicaciones pueden llamar APIs REST sin bloquear la ejecución mientras esperan operaciones de red.

El SDK también proporciona una capa de integración compartida que puede reutilizarse en otros componentes del proyecto.

**Ruta en el repositorio:**

[`sdk/`](./sdk)

---

## Air Travel MCP Server

Un servidor MCP (Model Context Protocol) construido con FastMCP.

El servidor MCP permite que asistentes y agentes de programación con IA interactúen con la API de viajes aéreos mediante herramientas MCP.

En lugar de duplicar la lógica de integración con la API REST, el servidor MCP reutiliza el SDK asíncrono compartido.

**Ruta en el repositorio:**

[`mcp/`](./mcp)

---

## Base de datos PostgreSQL

Una base de datos PostgreSQL que contiene datos operacionales de aerolíneas utilizados por la Flights API.

La base de datos proporciona una fuente de datos realista para demostrar el acceso asíncrono a bases de datos.

**Ruta en el repositorio:**

[`database/`](./database)

---

# Arquitectura asíncrona

Un flujo de solicitud simplificado se ve así:

```text
Agente de IA
   |
   v
Servidor MCP
   |
   v
SDK asíncrono / HTTPX
   |
   v
FastAPI
   |
   v
SQLAlchemy asíncrono / asyncpg
   |
   v
PostgreSQL
```

Cada capa puede pasar tiempo esperando operaciones de entrada/salida:

- el servidor MCP espera respuestas de la API
- el SDK espera respuestas HTTP
- FastAPI espera operaciones de base de datos
- el driver de base de datos espera a PostgreSQL

El uso de Python asíncrono permite que la aplicación realice otro trabajo mientras esas operaciones de entrada/salida están esperando.

Esto hace que esta arquitectura sea útil para demostrar dónde la programación asíncrona puede mejorar el throughput y la utilización de recursos.

---

# Fuente de datos

El proyecto utiliza datos públicos sobre operaciones de aerolíneas del
Bureau of Transportation Statistics (BTS) del Departamento de Transporte de Estados Unidos.

Portal de datos de BTS:

https://www.transtats.bts.gov/DL_SelectFields.aspx?gnoyr_VQ=FGK&QO_fu146_anzr=b0-gvzr

---

# Cómo empezar

Clona el repositorio:

```bash
git clone <repository-url>
cd <repository-name>
```

Las principales áreas para explorar son:

- `flights-api/` - servicio FastAPI asíncrono
- `sdk/` - cliente Python asíncrono reutilizable
- `mcp/` - servidor FastMCP
- `database/` - configuración y datos de PostgreSQL

Cada componente contiene su propia configuración y archivos de soporte.

---

# Qué demuestra este repositorio

La idea principal de la demostración de NERDEARLA 2026 es que **async es una técnica de arquitectura, no solamente una característica de sintaxis de una API**.

El mismo modelo de programación asíncrona puede aplicarse en múltiples capas:

- acceso a bases de datos
- endpoints REST
- clientes HTTP
- SDKs
- herramientas MCP

Al mantener estas capas de forma asíncrona, las aplicaciones pueden manejar de manera eficiente cargas de trabajo que pasan una cantidad significativa de tiempo esperando operaciones de red o de base de datos.

---

# Acerca del Anchor Project original

Este repositorio se deriva de mi proyecto más amplio **Air Travel Anchor Project**, que utilizo para explorar y demostrar técnicas de ciencia de datos, APIs e ingeniería de IA.

Para NERDEARLA 2026, el repositorio se ha reducido intencionalmente para enfocarse en Python asíncrono y su uso para construir una API y un servidor MCP.
