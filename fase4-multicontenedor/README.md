# Fase 4 — Despliegue de arquitectura multicontenedor

## Objetivo

Implementar una arquitectura de servicios en contenedores con aplicación, base de datos, volumen persistente y mapeo de puertos.

## Arquitectura implementada

```
┌─────────────────────────────────────────────────┐
│                  Docker Host (EC2)               │
│                                                 │
│  ┌──────────────────┐   ┌──────────────────┐   │
│  │  app (puerto 80) │──▶│  db (postgres)   │   │
│  │  contenedor app  │   │  contenedor db   │   │
│  └──────────────────┘   └────────┬─────────┘   │
│                                  │              │
│                         ┌────────▼─────────┐   │
│                         │  volumen: db-data │   │
│                         │  (persistencia)   │   │
│                         └──────────────────┘   │
└─────────────────────────────────────────────────┘
         ▲
    Puerto 80 expuesto
    al exterior (Internet)
```

## Archivo docker-compose.yml

Ver: [`docker-compose.yml`](./docker-compose.yml)

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | `docker-compose up` ejecutado | `evidencias/compose-up.png` |
| 2 | Comunicación entre contenedores | `evidencias/comunicacion-contenedores.png` |
| 3 | Acceso al servicio desde exterior | `evidencias/acceso-exterior.png` |
| 4 | Aislamiento entre servicios | `evidencias/aislamiento.png` |

## Demostración

### Comunicación entre contenedores

> 🔄 *Pendiente — completar tras el despliegue*

### Acceso al servicio desde el exterior

> 🔄 *Pendiente — completar tras el despliegue*

### Aislamiento entre servicios

> 🔄 *Pendiente — completar tras el despliegue*
