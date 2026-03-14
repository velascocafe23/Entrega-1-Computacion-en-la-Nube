# Fase 4 — Despliegue de arquitectura multicontenedor

## Objetivo

Implementar una arquitectura de servicios en contenedores con aplicación, base de datos, volumen persistente y mapeo de puertos.

## Arquitectura implementada
```
Internet
    │
    │ Puerto 80
    ▼
┌─────────────────────────────────────────────────────┐
│                VM EC2 (Ubuntu 24.04)                │
│                IP pública: 54.226.183.158           │
│                                                     │
│  ┌─────────────────────┐   ┌─────────────────────┐ │
│  │    app-service      │   │    db-service        │ │
│  │    nginx:alpine     │──▶│  postgres:15-alpine  │ │
│  │    172.18.0.3       │   │    172.18.0.2        │ │
│  │    Puerto 80:80     │   │    Puerto 5432        │ │
│  └─────────────────────┘   └──────────┬──────────┘ │
│           │                           │             │
│           └──────── app-network ──────┘             │
│                    (bridge driver)                  │
│                                       │             │
│                              ┌────────▼──────────┐  │
│                              │  volumen: db-data  │  │
│                              │  (persistencia PG) │  │
│                              └───────────────────┘  │
└─────────────────────────────────────────────────────┘
```

## Archivo docker-compose.yml

Ver: [`docker-compose.yml`](./docker-compose.yml)
```yaml
version: '3.8'
services:
  app:
    image: nginx:alpine
    container_name: app-service
    ports:
      - "80:80"
    networks:
      - app-network
    restart: unless-stopped
  db:
    image: postgres:15-alpine
    container_name: db-service
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppassword
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - app-network
    restart: unless-stopped
volumes:
  db-data:
networks:
  app-network:
    driver: bridge
```

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | Creación del archivo docker-compose.yml | [Archivo Partes.png](./evidencias/Archivo%20Partes.png) |
| 2 | Verificación del archivo | [VerificacionArchivo.png](./evidencias/VerificacionArchivo.png) |
| 3 | Levantamiento de contenedores | [Levantamiento.png](./evidencias/Levantamiento.png) |

## Demostración

### Comunicación entre contenedores

Los contenedores `app-service` y `db-service` se comunican a través de la red virtual `app-network` (driver bridge). Docker provee resolución DNS interna por nombre de servicio: `app-service` puede alcanzar a `db-service` usando directamente el nombre `db-service` como hostname, sin necesidad de conocer su IP.

Evidencia verificada mediante:
```bash
sudo docker exec app-service ping -c 4 db-service
# PING db-service (172.18.0.2): 56 data bytes
# 4 packets transmitted, 4 received, 0% packet loss
# rtt min/avg/max = 0.074/0.084/0.094 ms
```

La latencia de 0.084 ms confirma que la comunicación ocurre completamente dentro del host sin salir a la red física, tres órdenes de magnitud menor que la latencia con Internet (1.512 ms).

### Acceso al servicio desde el exterior

El mapeo de puertos `0.0.0.0:80->80/tcp` en `app-service` expone el servidor nginx al exterior a través de la IP pública de la VM. El flujo completo verificado es:
```
Usuario (navegador) → http://54.226.183.158
    → Security Group AWS (puerto 80 abierto)
    → VM EC2 (puerto 80 del host)
    → Docker port mapping
    → contenedor app-service (nginx, puerto 80 interno)
    → Respuesta: "Welcome to nginx!" ✅
```

### Aislamiento entre servicios

El aislamiento se implementa en dos niveles:

**Red:** la red `app-network` (bridge) es una red virtual privada dentro del host. Ningún tráfico externo puede llegar directamente a `db-service`. Solo `app-service`, por estar en la misma red, puede conectarse al puerto 5432 de PostgreSQL. El puerto 5432 nunca fue expuesto al exterior, lo que protege la base de datos de accesos no autorizados desde Internet.

**Sistema de archivos y datos:** cada contenedor tiene su propio sistema de archivos raíz basado en su imagen. Los datos de PostgreSQL se almacenan en el volumen `db-data`, que es independiente del ciclo de vida del contenedor — si `db-service` se destruye y se vuelve a crear, los datos persisten en el volumen sin pérdida de información.
