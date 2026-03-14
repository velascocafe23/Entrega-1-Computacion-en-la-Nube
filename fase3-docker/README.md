# Fase 3 — Instalación y administración de Docker

## Objetivo

Instalar Docker Engine en la VM de AWS EC2 y demostrar su funcionamiento.

## Comandos de instalación
```bash
# Actualizar paquetes
sudo apt-get update

# Instalar Docker Engine
sudo apt-get install -y docker.io

# Habilitar el servicio al inicio del sistema
sudo systemctl enable docker

# Iniciar el servicio
sudo systemctl start docker

# Instalar Docker Compose
sudo apt-get install -y docker-compose
```

## Verificación del servicio
```bash
sudo systemctl status docker
docker --version
docker-compose --version
```

## Contenedor de prueba
```bash
sudo docker run hello-world
```

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | Actualización de paquetes | [Actualizar paquetes.png](./evidencias/Actualizar%20paquetes.png) |
| 2 | Habilitar e iniciar el servicio | [Iniciar y habilitar el servicio.png](./evidencias/Iniciar%20y%20habilitar%20el%20servicio.png) |
| 3 | Verificación del servicio activo | [Status Docker.png](./evidencias/Status%20Docker.png) |
| 4 | Ejecución hello-world | [Contendero de Prueba.png](./evidencias/Contendero%20de%20Prueba.png) |

## Análisis conceptual

### Cómo el SO administra recursos para los contenedores

El sistema operativo Ubuntu 24.04 de la VM EC2 provee los mecanismos de kernel que hacen posibles los contenedores sin necesidad de virtualizar hardware adicional. Específicamente utiliza dos características del kernel Linux:

**Namespaces:** permiten que cada contenedor perciba un entorno aislado e independiente. El namespace de red asigna a cada contenedor su propia interfaz de red y dirección IP — verificado en la Fase 5: `app-service` en `172.18.0.3` y `db-service` en `172.18.0.2`. El namespace de PID hace que cada contenedor vea únicamente sus propios procesos. El namespace de sistema de archivos le asigna a cada contenedor su propio sistema de archivos raíz basado en la imagen.

**cgroups (Control Groups):** limitan y contabilizan el consumo de recursos por grupo de procesos. En la VM `t2.micro` (1 vCPU, 1 GB RAM), los cgroups evitan que un contenedor monopolice todos los recursos, garantizando que `app-service` y `db-service` coexistan sin interferirse. Docker Engine usa cgroups para implementar los límites de memoria y CPU configurables en el `docker-compose.yml`.

Docker Engine actúa como la interfaz entre el usuario y estos mecanismos del kernel: la CLI (`docker run`, `docker ps`) envía instrucciones al daemon `dockerd`, que las delega a `containerd` (runtime de bajo nivel certificado por la CNCF), el cual invoca las llamadas al sistema del kernel para crear los namespaces y cgroups correspondientes.

### Diferencias entre contenedor y máquina virtual

La diferencia fundamental es el nivel de abstracción: una **máquina virtual** virtualiza hardware completo (CPU, RAM, disco, red) y ejecuta un sistema operativo invitado completo sobre ese hardware virtual. Un **contenedor** no virtualiza hardware: comparte el kernel del host y usa namespaces y cgroups para aislar procesos.

Consecuencias prácticas verificadas en esta implementación:

| Dimensión | VM EC2 (Ubuntu 24.04) | Contenedor (nginx:alpine) |
|-----------|----------------------|--------------------------|
| Tiempo de arranque | ~90 segundos (boot del SO) | < 3 segundos |
| Tamaño | 2–4 GB (SO completo) | ~50 MB (solo diferencias) |
| Aislamiento | Total — kernel propio | Parcial — kernel compartido |
| Portabilidad | Ligada al hypervisor | Estándar OCI universal |

### Implicaciones para la escalabilidad

La arquitectura Docker sobre EC2 implementada tiene implicaciones directas para el escalado horizontal:

**Escalado rápido:** dado que los contenedores arrancan en milisegundos, un orquestador como Kubernetes puede agregar réplicas de `app-service` en segundos ante picos de tráfico. Con VMs, el mismo proceso requeriría varios minutos de boot completo.

**Escalado granular:** es posible escalar únicamente el servicio bajo presión. Si `app-service` recibe más tráfico, se escala ese contenedor específicamente sin tocar `db-service`, optimizando el costo. Con VMs monolíticas se escalaría toda la instancia aunque el cuello de botella sea solo un componente.

**Alta densidad:** la VM `t2.micro` (1 GB RAM) ejecuta simultáneamente `app-service` (~5 MB RAM) y `db-service` (~30 MB RAM). En una instancia de producción podrían correr decenas de contenedores equivalentes, maximizando la utilización del hardware y reduciendo el costo por servicio.
