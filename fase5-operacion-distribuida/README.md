# Fase 5 — Evidencia de operación distribuida

## Objetivo

Demostrar técnicamente la conectividad entre la VM y los contenedores, la comunicación entre contenedores y el uso de red en el entorno desplegado.

## Comandos de verificación de red
```bash
# Ver contenedores en ejecución
sudo docker ps

# Ver IPs asignadas a los contenedores
sudo docker inspect app-service | grep IPAddress
sudo docker inspect db-service  | grep IPAddress

# Ver redes Docker
sudo docker network ls
sudo docker network inspect ubuntu_app-network

# Verificar conectividad entre contenedores
sudo docker exec app-service ping -c 4 db-service
```

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | Contenedores en ejecución (docker ps) | [IPContenedores.png](./evidencias/IPContenedores.png) |
| 2 | Ping entre contenedores | [ComunicacionContenedores.png](./evidencias/ComunicacionContenedores.png) |
| 3 | Red Docker inspeccionada | [RedDocker.png](./evidencias/RedDocker.png) |
| 4 | Nginx accesible desde navegador | [Nginx.png](./evidencias/Nginx.png) |

## Resultados obtenidos

| Verificación | Resultado |
|-------------|-----------|
| IP app-service | `172.18.0.3` |
| IP db-service | `172.18.0.2` |
| Red Docker | `ubuntu_app-network` (bridge) |
| Ping entre contenedores | 4/4 paquetes, 0% pérdida, 0.084 ms |
| Nginx desde Internet | ✅ `Welcome to nginx!` en `http://54.226.183.158` |

## Explicación técnica

### Por qué el entorno corresponde a un sistema distribuido

Un sistema distribuido se define como un conjunto de componentes de software que se ejecutan en nodos independientes, se comunican a través de una red y cooperan para ofrecer un servicio. El entorno implementado cumple todos estos criterios de forma verificable:

**Nodos independientes:** `app-service` (IP `172.18.0.3`) y `db-service` (IP `172.18.0.2`) son procesos completamente separados con su propio sistema de archivos, espacio de red y ciclo de vida independiente gracias a los namespaces de Linux. A efectos de red, son dos nodos diferentes dentro del mismo host físico.

**Comunicación por red:** los contenedores se comunican exclusivamente a través de la red virtual `app-network`. No comparten memoria ni variables de proceso — toda interacción entre el servidor web y la base de datos ocurre mediante el protocolo TCP sobre el puerto 5432, exactamente igual a como ocurriría si estuvieran en servidores físicos separados. El ping entre contenedores (latencia 0.084 ms) confirma esta comunicación de red.

**Cooperación para ofrecer un servicio:** `app-service` maneja las peticiones HTTP del exterior y delega la persistencia de datos a `db-service`. Ninguno de los dos puede ofrecer el servicio completo de forma independiente — la cooperación entre ambos es lo que constituye la aplicación funcional.

**Persistencia desacoplada:** el volumen `db-data` almacena los datos de PostgreSQL de forma independiente al ciclo de vida de los contenedores, garantizando que el estado del sistema se mantiene incluso si los nodos se reinician.

### Relación entre virtualización, red y despliegue de servicios

El entorno implementado ilustra cómo tres capas de abstracción de red se apilan para construir la arquitectura distribuida:

**Capa 1 — Red física de AWS:** el hardware de red del centro de datos us-east-1 conecta los servidores físicos. Esta capa es completamente transparente para el usuario.

**Capa 2 — Red virtual VPC:** la VPC `vpc-09fba6709ef341e18` provee una red privada virtual (`172.31.0.0/16`) para la VM EC2. El Security Group filtra el tráfico externo permitiendo solo los puertos 22 y 80. El Internet Gateway de AWS enruta el tráfico entre la IP pública `54.226.183.158` y la IP privada `172.31.20.85` de la instancia.

**Capa 3 — Red virtual Docker:** dentro de la VM, Docker crea la red bridge `app-network` (`172.18.0.0/16`). Esta red es completamente interna al host — el tráfico entre `app-service` y `db-service` nunca sale de la VM ni pasa por la VPC. Docker actúa como un switch virtual que conecta los contenedores entre sí con resolución DNS interna por nombre de servicio.

Esta arquitectura de red en capas implementa el principio de mínima exposición en cada nivel: Internet solo ve el puerto 80 y 22 de la VM; la VPC gestiona el enrutamiento interno; Docker mantiene la base de datos completamente aislada del exterior.
