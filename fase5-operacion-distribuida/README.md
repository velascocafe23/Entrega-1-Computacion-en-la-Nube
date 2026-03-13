# Fase 5 — Evidencia de operación distribuida

## Objetivo

Demostrar técnicamente la conectividad entre la VM y los contenedores, la comunicación entre contenedores y el uso de red en el entorno desplegado.

## Comandos de verificación de red

```bash
# Ver IPs asignadas a los contenedores
docker inspect app-service | grep IPAddress
docker inspect db-service  | grep IPAddress

# Ver redes Docker
docker network ls
docker network inspect fase4-multicontenedor_app-network

# Verificar conectividad entre contenedores
docker exec app-service ping db-service

# Resolución de nombres (DNS interno Docker)
docker exec app-service nslookup db-service

# Desde la VM hacia los contenedores
curl http://localhost:80

# Ver tablas de enrutamiento
ip route
netstat -rn
```

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | IPs de contenedores | `evidencias/ips-contenedores.png` |
| 2 | Red Docker inspeccionada | `evidencias/docker-network.png` |
| 3 | Ping entre contenedores | `evidencias/ping-contenedores.png` |
| 4 | Resolución DNS interna | `evidencias/dns-interno.png` |
| 5 | Acceso desde VM | `evidencias/acceso-vm.png` |

## Explicación técnica

### Por qué el entorno corresponde a un sistema distribuido

> 🔄 *Pendiente — completar tras las pruebas*

### Relación entre virtualización, red y despliegue de servicios

> 🔄 *Pendiente — completar tras las pruebas*
