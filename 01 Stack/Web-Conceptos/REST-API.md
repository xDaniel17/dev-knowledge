---
title: REST API
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Web-Conceptos/REST-API.md
---

# 📡 REST API

## ¿Qué es REST?

**REST** (Representational State Transfer) es un estilo arquitectónico para diseñar servicios web distribuidos.

## Principios de REST

### 1. Cliente-Servidor
- Separación clara entre cliente y servidor
- Pueden evolucionar de forma independiente

### 2. Stateless
- Cada solicitud contiene toda la información
- El servidor no almacena contexto del cliente
- Mejora escalabilidad

### 3. Cacheable
- Las respuestas deben indicar si son cacheables
- Reduce ancho de banda y latencia

### 4. Interfaz Uniforme
- Recursos identificados por URIs
- Manipulación de recursos a través de representaciones
- Mensajes autodescriptivos

### 5. Sistema en Capas
- La arquitectura puede estar compuesta de capas
- Cada capa solo conoce la capa adyacente

## Métodos HTTP

| Método | Operación | Seguro | Idempotente |
|--------|-----------|--------|-------------|
| **GET** | Obtener | ✅ | ✅ |
| **POST** | Crear | ❌ | ❌ |
| **PUT** | Actualizar (completo) | ❌ | ✅ |
| **PATCH** | Actualizar (parcial) | ❌ | ❌ |
| **DELETE** | Eliminar | ❌ | ✅ |

## Códigos de estado HTTP

### 2xx - Éxito
- `200 OK` - Solicitud exitosa
- `201 Created` - Recurso creado
- `204 No Content` - Sin contenido

### 3xx - Redirección
- `301 Moved Permanently` - Movido permanentemente
- `304 Not Modified` - No modificado

### 4xx - Error del cliente
- `400 Bad Request` - Solicitud inválida
- `401 Unauthorized` - No autorizado
- `404 Not Found` - No encontrado

### 5xx - Error del servidor
- `500 Internal Server Error` - Error del servidor
- `503 Service Unavailable` - Servicio no disponible

## Estructura de una API REST

```
GET /api/usuarios              # Obtener todos
GET /api/usuarios/1            # Obtener uno
POST /api/usuarios             # Crear
PUT /api/usuarios/1            # Actualizar
DELETE /api/usuarios/1         # Eliminar
```

## Buenas prácticas

1. **URLs descriptivas** - Usar sustantivos, no verbos
2. **Versionamiento** - `/api/v1/`, `/api/v2/`
3. **Paginación** - Para colecciones grandes
4. **Filtering** - `/api/usuarios?role=admin`
5. **CORS** - Permitir acceso desde otros orígenes
6. **Rate limiting** - Limitar solicitudes por cliente
7. **Documentación** - Swagger/OpenAPI

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documento inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
