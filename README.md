# Actividad Evaluativa Unidad 1
## Virtualización, Contenedores y Despliegue Distribuido en la Nube

**Maestría en Ciencia de Datos — Universidad Pontificia Bolivariana**  
**Curso:** Computación en la Nube y Arquitecturas de Big Data  
**Docente:** Álvaro Ospina Sanjuan  
**Estudiante:** Sebastian Velasco  
**Fecha de entrega:** Marzo 2026  
**Repositorio:** https://github.com/velascocafe23/Entrega-1-Computacion-en-la-Nube

---

## Estructura del repositorio

```
.
├── fase1-fundamentacion/
│   ├── mapa-conceptual.png
│   ├── mapa-conceptual.html
│   ├── referencia-bibliografica.docx
│   └── README.md
├── fase2-aws-ec2/
│   ├── evidencias/
│   │   ├── IP_PUBLICA.png
│   │   ├── IP_PUBLICA_Y_PRIVADA.png
│   │   ├── Red_y_VPC.png
│   │   ├── SecurityGroup.png
│   │   ├── Interfaces_de_red_IP_privada_y_mascara.png
│   │   ├── Gateway_y_tabla_de_rutas.png
│   │   └── Conectividad_a_Internet.png
│   └── README.md
├── fase3-docker/
│   ├── evidencias/
│   │   ├── Actualizar_paquetes.png
│   │   ├── Iniciar_y_habilitar_el_servicio.png
│   │   ├── Status_Docker.png
│   │   └── Contendero_de_Prueba.png
│   └── README.md
├── fase4-multicontenedor/
│   ├── docker-compose.yml
│   ├── evidencias/
│   │   ├── Archivo_Partes.png
│   │   ├── VerificacionArchivo.png
│   │   └── Levantamiento.png
│   └── README.md
├── fase5-operacion-distribuida/
│   ├── evidencias/
│   │   ├── IPContenedores.png
│   │   ├── ComunicacionContenedores.png
│   │   ├── Red_Docker.png
│   │   └── Nginx_Welcome.png
│   └── README.md
├── fase6-analisis/
│   └── README.md
├── Informe_Tecnico_Unidad1_Velasco.docx
└── README.md  ← estás aquí
```

---

## Fases del trabajo

| Fase | Descripción | Estado |
|------|-------------|--------|
| [Fase 1](#fase-1--fundamentación-conceptual) | Fundamentación conceptual | ✅ Completada |
| [Fase 2](#fase-2--infraestructura-en-la-nube-aws) | Infraestructura en la nube AWS | ✅ Completada |
| [Fase 3](#fase-3--instalación-y-administración-de-docker) | Instalación y administración de Docker | ✅ Completada |
| [Fase 4](#fase-4--despliegue-de-arquitectura-multicontenedor) | Despliegue de arquitectura multicontenedor | ✅ Completada |
| [Fase 5](#fase-5--evidencia-de-operación-distribuida) | Evidencia de operación distribuida | ✅ Completada |
| [Fase 6](#fase-6--análisis-técnico-y-reflexión) | Análisis técnico y reflexión | ✅ Completada |

---

## Fase 1 — Fundamentación conceptual

### 1.1 Mapa conceptual: Virtualización vs. Contenedores

El mapa conceptual cubre los siguientes bloques temáticos:

- **Virtualización:** definición, hypervisores Tipo 1 y Tipo 2, máquina virtual (VM), tipos de virtualización (servidor, red/SDN, almacenamiento, escritorio/VDI), ventajas y desventajas.
- **Contenedores:** definición, mecanismos del kernel Linux (namespaces y cgroups), container runtimes (Docker, Podman, containerd), imagen vs. contenedor, Dockerfile, orquestación (Kubernetes, Swarm, Nomad).
- **Comparativa directa:** aislamiento, arranque, tamaño, portabilidad, seguridad y densidad.
- **Casos de uso:** cuándo usar VMs, cuándo usar contenedores.
- **Coexistencia:** patrón VMs + contenedores en AWS, Azure y GCP.

📄 Ver: [`fase1-fundamentacion/README.md`](./fase1-fundamentacion/README.md)  
🖼️ Mapa visual: [`fase1-fundamentacion/mapa-conceptual.png`](./fase1-fundamentacion/mapa-conceptual.png)

### 1.2 Rol del sistema operativo, el kernel y Docker Engine

El **sistema operativo** actúa como intermediario entre las aplicaciones y el hardware físico, gestionando memoria, procesos, sistema de archivos y dispositivos de E/S. En el contexto de la virtualización, el SO puede ser anfitrión (host) o invitado (guest).

El **kernel** es el núcleo del SO: gestiona directamente el hardware mediante planificación de procesos, gestión de memoria, VFS y controladores de dispositivos. En contenedores, todos los contenedores de un host **comparten el mismo kernel**, que provee los mecanismos de aislamiento:

- **Namespaces:** aíslan la vista de recursos por proceso (PID, red, sistema de archivos, usuarios)
- **cgroups:** limitan y contabilizan el consumo de recursos (CPU, RAM, I/O)

**Docker Engine** se compone de tres capas:

```
┌─────────────────────────────────────┐
│           Docker CLI                │  ← usuario
├─────────────────────────────────────┤
│        Docker Daemon (dockerd)      │  ← API REST
├─────────────────────────────────────┤
│           containerd                │  ← runtime CNCF
├─────────────────────────────────────┤
│         Kernel Linux                │
│     (namespaces + cgroups)          │
└─────────────────────────────────────┘
```

### 1.3 Cómo la virtualización habilita la computación en la nube

La computación en la nube (NIST SP 800-145) es posible gracias a la virtualización por tres razones fundamentales:

- **Multi-tenancy:** múltiples clientes comparten el mismo hardware físico con aislamiento completo entre ellos
- **Elasticidad:** los hypervisores permiten crear, clonar, migrar en caliente y destruir VMs en segundos sin intervención física
- **Eficiencia:** la utilización de hardware pasa del 5–15% en servidores físicos al 60–80% con virtualización, haciendo rentable el modelo de nube pública

### 1.4 Por qué los contenedores permiten portabilidad de aplicaciones

Los contenedores logran portabilidad universal mediante tres mecanismos:

- **Empaquetado de dependencias:** la imagen incluye código, runtime, bibliotecas y configuración. Elimina el problema de *"funciona en mi máquina"*
- **Estándar OCI:** el formato de imagen y runtime está estandarizado por la Linux Foundation. Cualquier plataforma OCI-compatible ejecuta la misma imagen sin modificaciones
- **Registries universales:** Docker Hub, Amazon ECR, Azure ACR permiten distribuir imágenes a cualquier host con un container runtime compatible

---

## Fase 2 — Infraestructura en la nube AWS

### Instancia EC2 aprovisionada

| Parámetro | Valor |
|-----------|-------|
| **Instance ID** | `i-0d11c11856fc13d30` |
| **Nombre** | `vm-computacion-nube-upb` |
| **AMI** | Ubuntu Server 24.04 LTS (HVM) |
| **Tipo** | t2.micro (1 vCPU, 1 GB RAM) — Free Tier |
| **Zona** | us-east-1a (Norte de Virginia) |
| **VPC** | `vpc-09fba6709ef341e18` |
| **Subred** | `subnet-05cf434a99aa756d4` |
| **Security Group** | `launch-wizard-3` — puertos 22 (SSH) y 80 (HTTP) |

### Configuración de red verificada

| Dato | Valor | Comando |
|------|-------|---------|
| IP pública | `54.226.183.158` | `curl ifconfig.me` |
| IP privada | `172.31.20.85` | `ip addr show` |
| Máscara de red | `/20` (255.255.240.0) | `ip addr show` |
| Gateway VPC | `172.31.16.1` | `ip route show` |
| Broadcast | `172.31.31.255` | `ip addr show` |
| Interfaz | `enX0` | `ip addr show` |
| Conectividad | 4/4 paquetes, 0% pérdida, 1.512 ms | `ping -c 4 google.com` |

### Evidencias

| Evidencia | Archivo |
|-----------|---------|
| IP pública y privada | [`evidencias/IP_PUBLICA_Y_PRIVADA.png`](./evidencias/IP_PUBLICA_Y_PRIVADA.png) |
| Red y VPC | [`evidencias/Red_y_VPC.png`](./evidencias/Red_y_VPC.png) |
| Security Group (puertos 22 y 80) | [`evidencias/SecurityGroup.png`](./evidencias/SecurityGroup.png) |
| Interfaces de red y máscara | [`evidencias/Interfaces_de_red_IP_privada_y_mascara.png`](./evidencias/Interfaces_de_red_IP_privada_y_mascara.png) |
| Gateway y tabla de rutas | [`evidencias/Gateway_y_tabla_de_rutas.png`](./evidencias/Gateway_y_tabla_de_rutas.png) |
| Conectividad a Internet | [`evidencias/Conectividad_a_Internet.png`](./evidencias/Conectividad_a_Internet.png) |

📄 Ver detalle: [`fase2-aws-ec2/README.md`](./fase2-aws-ec2/README.md)

---

## Fase 3 — Instalación y administración de Docker

### Proceso de instalación en Ubuntu 24.04

```bash
# 1. Actualizar índices de paquetes
sudo apt-get update

# 2. Instalar Docker Engine
sudo apt-get install -y docker.io

# 3. Habilitar e iniciar el servicio
sudo systemctl enable docker
sudo systemctl start docker

# 4. Instalar Docker Compose
sudo apt-get install -y docker-compose
```

### Verificación del servicio

| Parámetro | Valor verificado |
|-----------|-----------------|
| Estado | `active (running)` |
| Versión | Docker 28.2.2-0ubuntu1~24.04.1 |
| Inicio del servicio | Fri 2026-03-13 15:24:06 UTC |
| PID principal | 2639 (dockerd) |
| Memoria | 20.0 MB |
| Docker Compose | v1.29.2 |

### Contenedor de prueba

```bash
sudo docker run hello-world
# Resultado: Hello from Docker!
```

### Evidencias

| Evidencia | Archivo |
|-----------|---------|
| Actualización de paquetes | [`evidencias/Actualizar_paquetes.png`](./evidencias/Actualizar_paquetes.png) |
| Habilitar e iniciar servicio | [`evidencias/Iniciar_y_habilitar_el_servicio.png`](./evidencias/Iniciar_y_habilitar_el_servicio.png) |
| Status Docker activo | [`evidencias/Status_Docker.png`](./evidencias/Status_Docker.png) |
| Contenedor hello-world | [`evidencias/Contendero_de_Prueba.png`](./evidencias/Contendero_de_Prueba.png) |

📄 Ver detalle: [`fase3-docker/README.md`](./fase3-docker/README.md)

---

## Fase 4 — Despliegue de arquitectura multicontenedor

### docker-compose.yml

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

### Resultado del despliegue

```bash
sudo docker-compose up -d

# Creating network "ubuntu_app-network" with driver "bridge"
# Creating volume "ubuntu_db-data" with default driver
# Pulling app (nginx:alpine)...   → Downloaded newer image
# Pulling db (postgres:15-alpine)... → Downloaded newer image
# Creating app-service ... done
# Creating db-service  ... done
```

### Evidencias

| Evidencia | Archivo |
|-----------|---------|
| Creación del archivo compose | [`evidencias/Archivo_Partes.png`](./evidencias/Archivo_Partes.png) |
| Verificación del archivo | [`evidencias/VerificacionArchivo.png`](./evidencias/VerificacionArchivo.png) |
| Levantamiento de contenedores | [`evidencias/Levantamiento.png`](./evidencias/Levantamiento.png) |

📄 Ver detalle: [`fase4-multicontenedor/README.md`](./fase4-multicontenedor/README.md)

---

## Fase 5 — Evidencia de operación distribuida

### Contenedores en ejecución

```
CONTAINER ID   IMAGE              STATUS         PORTS                  NAMES
8a476d5622c2   nginx:alpine       Up             0.0.0.0:80->80/tcp     app-service
831b1242fab3   postgres:15-alpine Up             5432/tcp               db-service
```

### Red Docker interna

| Contenedor | IP interna | Red |
|------------|------------|-----|
| `app-service` | `172.18.0.3` | `ubuntu_app-network` |
| `db-service` | `172.18.0.2` | `ubuntu_app-network` |
| Gateway virtual | `172.18.0.1` | bridge driver |

### Comunicación entre contenedores

```bash
sudo docker exec app-service ping -c 4 db-service

# PING db-service (172.18.0.2): 56 data bytes
# 4 packets transmitted, 4 received, 0% packet loss
# rtt min/avg/max = 0.074/0.084/0.094 ms
```

### Acceso desde Internet

Nginx accesible en: **http://54.226.183.158** → `Welcome to nginx!` ✅

### Evidencias

| Evidencia | Archivo |
|-----------|---------|
| IPs de contenedores (docker ps + inspect) | [`evidencias/IPContenedores.png`](./evidencias/IPContenedores.png) |
| Comunicación inter-contenedor (ping) | [`evidencias/ComunicacionContenedores.png`](./evidencias/ComunicacionContenedores.png) |
| Red Docker (network inspect) | [`evidencias/Red_Docker.png`](./evidencias/Red_Docker.png) |
| Nginx accesible desde navegador | [`evidencias/Nginx_Welcome.png`](./evidencias/Nginx_Welcome.png) |

📄 Ver detalle: [`fase5-operacion-distribuida/README.md`](./fase5-operacion-distribuida/README.md)

---

## Fase 6 — Análisis técnico y reflexión

### Comparativa VM vs. Contenedor

| Dimensión | Máquina Virtual (EC2) | Contenedor (Docker) |
|-----------|----------------------|---------------------|
| **Aislamiento** | Total — kernel propio | Parcial — kernel compartido |
| **Arranque** | 1–3 minutos | Milisegundos |
| **Tamaño de imagen** | 2–20 GB | 50–500 MB |
| **Portabilidad** | Limitada (ligada al hypervisor) | Alta (estándar OCI) |
| **Seguridad** | Mayor | Menor |
| **Densidad por host** | Decenas de VMs | Cientos de contenedores |
| **Consumo de RAM** | Alto (SO invitado activo) | Bajo (sin SO invitado) |
| **Casos de uso** | Apps legacy, máx. seguridad | Microservicios, CI/CD |

### Evidencias numéricas del entorno implementado

| Métrica | VM EC2 | Contenedores |
|---------|--------|--------------|
| Tiempo de arranque | ~90 segundos | < 3 segundos |
| Latencia red externa | 1.512 ms (Google) | — |
| Latencia red interna | — | 0.084 ms (inter-contenedor) |
| Tamaño nginx:alpine | — | ~50 MB |
| Tamaño postgres:15-alpine | — | ~250 MB |

### Conclusiones

1. **La virtualización es el fundamento de la nube.** La VM EC2 (KVM Tipo 1) provee el kernel Linux y el aislamiento de hardware que hace posible Docker y el multi-tenancy de AWS.
2. **Contenedores y VMs son complementarios.** El patrón industria — VMs como nodos de infraestructura + contenedores como unidades de despliegue — maximiza el aislamiento de la VM y la agilidad del contenedor.
3. **Docker abstrae la complejidad del kernel.** Namespaces y cgroups son los mecanismos reales de aislamiento; Docker los expone en conceptos de alto nivel (imagen, contenedor, red, volumen).
4. **Red en capas = seguridad cloud-native.** VPC controla el tráfico externo, `app-network` controla la comunicación interna. El puerto 5432 de postgres nunca estuvo expuesto a Internet.
5. **El entorno es un sistema distribuido funcional.** Dos procesos con IPs propias, comunicándose por DNS interno, con persistencia desacoplada del ciclo de vida — todos los atributos de un sistema distribuido.

📄 Ver análisis completo: [`fase6-analisis/README.md`](./fase6-analisis/README.md)

---

## Informe técnico

El informe técnico completo en formato Word está disponible en:  
📄 [`Informe_Tecnico_Unidad1_Velasco.docx`](./Informe_Tecnico_Unidad1_Velasco.docx)

---

## Bibliografía

| # | Fuente | URL |
|---|--------|-----|
| 1 | NIST SP 800-145 — The NIST Definition of Cloud Computing | https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf |
| 2 | VMware — What is Virtualization? | https://www.vmware.com/topics/virtualization |
| 3 | Red Hat — What is a hypervisor? | https://www.redhat.com/en/topics/virtualization/what-is-a-hypervisor |
| 4 | IBM — What is a virtual machine? | https://www.ibm.com/topics/virtual-machines |
| 5 | CNCF — Cloud Native Definition v1.0 | https://github.com/cncf/toc/blob/main/DEFINITION.md |
| 6 | OCI — Runtime Specification | https://github.com/opencontainers/runtime-spec/blob/main/spec.md |
| 7 | Linux Kernel — Namespaces documentation | https://lwn.net/Articles/531114/ |
| 8 | Linux Kernel — cgroups documentation | https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt |
| 9 | Docker, Inc. — What is a Container? | https://www.docker.com/resources/what-container/ |
| 10 | CNCF — containerd project | https://containerd.io/ |
| 11 | Kubernetes — Overview | https://kubernetes.io/docs/concepts/overview/ |
| 12 | NIST SP 800-190 — Application Container Security Guide | https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-190.pdf |
| 13 | AWS — What is Amazon EC2? | https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html |
| 14 | AWS — What is Amazon VPC? | https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html |
| 15 | Docker — Docker Engine overview | https://docs.docker.com/engine/ |
| 16 | Docker — Docker Compose overview | https://docs.docker.com/compose/ |
| 17 | OCI — About the Open Container Initiative | https://opencontainers.org/about/overview/ |

---

*© 2026 — Maestría en Ciencia de Datos, UPB. Actividad Evaluativa Unidad 1.*
