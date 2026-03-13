# Actividad Evaluativa Unidad 1
## Virtualización, Contenedores y Despliegue Distribuido en la Nube

**Maestría en Ciencia de Datos — Universidad Pontificia Bolivariana**  
**Curso:** Computación en la nube y Arquitecturas de Big Data  
**Docente:** Álvaro Ospina Sanjuan  
**Fecha de entrega:** Marzo 10 de 2026

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
│   └── README.md
├── fase3-docker/
│   ├── evidencias/
│   └── README.md
├── fase4-multicontenedor/
│   ├── docker-compose.yml
│   ├── evidencias/
│   └── README.md
├── fase5-operacion-distribuida/
│   ├── evidencias/
│   └── README.md
├── fase6-analisis/
│   └── README.md
└── README.md  ← estás aquí
```

---

## Fases del trabajo

| Fase | Descripción | Estado |
|------|-------------|--------|
| [Fase 1](#fase-1--fundamentación-conceptual) | Fundamentación conceptual | ✅ Completada |
| [Fase 2](#fase-2--infraestructura-en-la-nube-aws) | Infraestructura en la nube AWS | 🔄 En progreso |
| [Fase 3](#fase-3--instalación-y-administración-de-docker) | Instalación y administración de Docker | 🔄 En progreso |
| [Fase 4](#fase-4--despliegue-de-arquitectura-multicontenedor) | Despliegue de arquitectura multicontenedor | 🔄 En progreso |
| [Fase 5](#fase-5--evidencia-de-operación-distribuida) | Evidencia de operación distribuida | 🔄 En progreso |
| [Fase 6](#fase-6--análisis-técnico-y-reflexión) | Análisis técnico y reflexión | 🔄 En progreso |

---

## Fase 1 — Fundamentación conceptual

### Contenido

Esta fase responde a los cuatro puntos de fundamentación teórica exigidos:

1. **Mapa conceptual:** diferencia entre virtualización y contenedores
2. **Rol del SO, kernel y Docker Engine**
3. **Cómo la virtualización habilita la computación en la nube**
4. **Por qué los contenedores permiten portabilidad de aplicaciones**

### 1.1 Mapa conceptual: Virtualización vs. Contenedores

El mapa conceptual fue elaborado cubriendo los siguientes bloques temáticos:

- **Virtualización:** definición, hypervisores Tipo 1 y Tipo 2, máquina virtual (VM), tipos de virtualización (servidor, red/SDN, almacenamiento, escritorio/VDI), ventajas y desventajas.
- **Contenedores:** definición, mecanismos del kernel Linux (namespaces y cgroups), container runtimes (Docker, Podman, containerd), imagen vs. contenedor, Dockerfile, orquestación (Kubernetes, Swarm, Nomad).
- **Comparativa directa:** aislamiento, arranque, tamaño, portabilidad, seguridad y densidad.
- **Casos de uso:** cuándo usar VMs, cuándo usar contenedores.
- **Coexistencia:** patrón VMs + contenedores en AWS, Azure y GCP.

📄 Ver: [`fase1-fundamentacion/README.md`](./fase1-fundamentacion/README.md)  
🖼️ Mapa visual: [`fase1-fundamentacion/mapa-conceptual.png`](./fase1-fundamentacion/mapa-conceptual.png)

### 1.2 Rol del sistema operativo, el kernel y Docker Engine

El **sistema operativo** actúa como intermediario entre las aplicaciones y el hardware físico, gestionando memoria, procesos, sistema de archivos y dispositivos de E/S. En el contexto de la virtualización, el SO puede ser tanto anfitrión (host) — el que corre directamente sobre el hardware — como invitado (guest) — el que corre dentro de una máquina virtual.

El **kernel** es el núcleo del sistema operativo: el componente de más bajo nivel que gestiona directamente el hardware. Es el responsable de la planificación de procesos (scheduler), la gestión de memoria (paginación, segmentación), el sistema de archivos virtual (VFS) y los controladores de dispositivos. En el contexto de los contenedores, **todos los contenedores de un host comparten el mismo kernel**, siendo este el componente que provee los mecanismos de aislamiento mediante **namespaces** (aíslan la vista de recursos: PID, red, sistema de archivos, usuarios) y **cgroups** (limitan el consumo de recursos: CPU, RAM, I/O).

**Docker Engine** es la plataforma de contenedores más ampliamente adoptada. Su arquitectura se compone de tres capas:

- **Docker CLI:** interfaz de línea de comandos con la que el usuario interactúa (`docker build`, `docker run`, `docker ps`, etc.).
- **Docker Daemon (`dockerd`):** proceso en segundo plano que escucha las solicitudes de la CLI a través de la API REST de Docker. Es responsable de gestionar imágenes, contenedores, redes y volúmenes.
- **containerd:** runtime de bajo nivel (proyecto graduado de la CNCF) que se encarga de la ejecución real de los contenedores, incluyendo la gestión del ciclo de vida, snapshots del sistema de archivos y distribución de imágenes.

```
┌─────────────────────────────────────┐
│           Docker CLI                │  ← usuario
├─────────────────────────────────────┤
│        Docker Daemon (dockerd)      │  ← API REST
├─────────────────────────────────────┤
│           containerd                │  ← runtime CNCF
├─────────────────────────────────────┤
│         Kernel Linux                │  ← namespaces + cgroups
│     (namespaces + cgroups)          │
└─────────────────────────────────────┘
```

### 1.3 Cómo la virtualización habilita la computación en la nube

La computación en la nube se define como el acceso bajo demanda a recursos de cómputo compartidos (servidores, almacenamiento, redes, aplicaciones) a través de Internet, con mínima gestión por parte del usuario (NIST SP 800-145). La virtualización es la tecnología fundamental que hace esto posible por tres razones:

**Multi-tenancy:** la virtualización permite que múltiples clientes (tenants) compartan el mismo hardware físico de forma completamente aislada. Sin virtualización, cada cliente requeriría hardware dedicado, haciendo inviable el modelo económico de la nube.

**Elasticidad y aprovisionamiento dinámico:** los hypervisores permiten crear, clonar, migrar en caliente (live migration) y destruir VMs en segundos o minutos, sin intervención física en el hardware. Esto es lo que permite a AWS, Azure y GCP aprovisionar miles de instancias bajo demanda.

**Modelos de servicio (IaaS, PaaS, SaaS):** el modelo IaaS (Infrastructure as a Service) es la capa más directa: el proveedor virtualiza el hardware (cómputo, red, almacenamiento) y el cliente gestiona el SO y las aplicaciones. AWS EC2 es el ejemplo canónico. Sin virtualización, no existiría IaaS tal como lo conocemos.

**Utilización de hardware:** los centros de datos tradicionales operan servidores físicos con tasas de utilización del 5–15%. La virtualización eleva esa tasa al 60–80%, haciendo rentable el modelo de nube pública.

### 1.4 Por qué los contenedores permiten portabilidad de aplicaciones

La portabilidad es la capacidad de una aplicación de ejecutarse de forma consistente en diferentes entornos: laptop del desarrollador, servidor de staging, nube de producción (AWS, Azure, GCP) o infraestructura on-premise.

Los contenedores logran esto por tres mecanismos técnicos:

**Empaquetado de dependencias:** una imagen de contenedor incluye no solo el código de la aplicación, sino también el runtime (Python, Node.js, JVM), las bibliotecas del sistema, las variables de entorno y los archivos de configuración. Esto elimina el problema histórico de *"funciona en mi máquina"*, ya que el entorno de ejecución viaja junto con la aplicación.

**Estándar OCI (Open Container Initiative):** el formato de imagen y el protocolo de runtime están estandarizados por la Linux Foundation a través de la OCI. Cualquier plataforma compatible con OCI (Docker, Podman, Kubernetes, AWS ECS, Azure ACI) puede ejecutar la misma imagen sin modificaciones.

**Registries universales:** las imágenes se distribuyen a través de registries estandarizados (Docker Hub, Amazon ECR, Azure Container Registry, Google Artifact Registry). Una imagen construida una vez puede descargarse y ejecutarse en cualquier host con un container runtime compatible, independientemente del proveedor de nube o del hardware subyacente.

---

## Fase 2 — Infraestructura en la nube AWS

> 🔄 **En progreso** — se completará con las evidencias de la instancia EC2.

Ver detalle en: [`fase2-aws-ec2/README.md`](./fase2-aws-ec2/README.md)

---

## Fase 3 — Instalación y administración de Docker

> 🔄 **En progreso** — se completará con las evidencias de instalación y ejecución en la VM.

Ver detalle en: [`fase3-docker/README.md`](./fase3-docker/README.md)

---

## Fase 4 — Despliegue de arquitectura multicontenedor

> 🔄 **En progreso** — se completará con el `docker-compose.yml` y evidencias.

Ver detalle en: [`fase4-multicontenedor/README.md`](./fase4-multicontenedor/README.md)

---

## Fase 5 — Evidencia de operación distribuida

> 🔄 **En progreso** — se completará con las pruebas de conectividad y red.

Ver detalle en: [`fase5-operacion-distribuida/README.md`](./fase5-operacion-distribuida/README.md)

---

## Fase 6 — Análisis técnico y reflexión

> 🔄 **En progreso** — se completará con el análisis comparativo final.

Ver detalle en: [`fase6-analisis/README.md`](./fase6-analisis/README.md)

---

## Bibliografía

| # | Fuente | Tipo | URL |
|---|--------|------|-----|
| 1 | NIST SP 800-145 — The NIST Definition of Cloud Computing | Estándar oficial | https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-145.pdf |
| 2 | VMware — What is Virtualization? | Documentación oficial | https://www.vmware.com/topics/virtualization |
| 3 | Red Hat — What is a hypervisor? | Documentación oficial | https://www.redhat.com/en/topics/virtualization/what-is-a-hypervisor |
| 4 | IBM — What is a virtual machine? | Documentación oficial | https://www.ibm.com/topics/virtual-machines |
| 5 | Open Networking Foundation — SDN White Paper | Estándar técnico | https://opennetworking.org/wp-content/uploads/2013/02/white-paper-Apr-2012.pdf |
| 6 | CNCF — Cloud Native Definition v1.0 | Estándar oficial | https://github.com/cncf/toc/blob/main/DEFINITION.md |
| 7 | OCI / Linux Foundation — Runtime Specification v1.0.2 | Estándar oficial | https://github.com/opencontainers/runtime-spec/blob/main/spec.md |
| 8 | Linux Kernel — Namespaces documentation | Documentación oficial | https://lwn.net/Articles/531114/ |
| 9 | Linux Kernel — cgroups documentation | Documentación oficial | https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt |
| 10 | Docker, Inc. — What is a Container? | Documentación oficial | https://www.docker.com/resources/what-container/ |
| 11 | CNCF — containerd project | Proyecto oficial CNCF | https://containerd.io/ |
| 12 | Kubernetes / CNCF — Overview | Documentación oficial | https://kubernetes.io/docs/concepts/overview/ |
| 13 | NIST SP 800-190 — Application Container Security Guide | Estándar oficial | https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-190.pdf |
| 14 | AWS — What is Amazon EC2? | Documentación oficial | https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html |
| 15 | AWS — What is Amazon VPC? | Documentación oficial | https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html |
| 16 | Docker — Docker Engine overview | Documentación oficial | https://docs.docker.com/engine/ |
| 17 | Docker — Docker Compose overview | Documentación oficial | https://docs.docker.com/compose/ |

---

*©2026 — Maestría en Ciencia de Datos, UPB. Actividad Evaluativa Unidad 1.*
