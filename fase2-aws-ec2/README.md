# Fase 2 — Infraestructura en la nube AWS

## Objetivo

Implementar una máquina virtual Linux en AWS EC2 y documentar su configuración de red y acceso remoto.

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | Dirección IP pública y privada | [IP PUBLICA Y PRIVADA.png](./evidencias/IP%20PUBLICA%20Y%20PRIVADA.png) |
| 2 | Máscara de red | [Interfaces_de_red.png](./evidencias/Interfaces_de_red.png.png) |
| 3 | Gateway | [Gateway y tabla de rutas.png](./evidencias/Gateway%20y%20tabla%20de%20rutas.png) |
| 4 | Acceso remoto mediante SSH | [IP_PUBLICA.png](./evidencias/IP_PUBLICA.png) |
| 5 | Configuración de reglas de seguridad | [SecurityGroup.png](./evidencias/SecurityGroup.png) |
| 6 | Conectividad a Internet | [Conectividad a Internet.png](./evidencias/Conectividad%20a%20Internet.png) |

## Explicación técnica

### Por qué EC2 corresponde a un modelo IaaS

Amazon EC2 (Elastic Compute Cloud) es el ejemplo canónico de **Infrastructure as a Service (IaaS)** según la clasificación del NIST SP 800-145. En este modelo, el proveedor de nube (AWS) gestiona los recursos de infraestructura — hardware físico, red, almacenamiento y virtualización — mientras que el cliente conserva el control total sobre el sistema operativo, el middleware y las aplicaciones.

En la instancia implementada esto se evidencia con claridad: AWS gestionó el hardware físico del centro de datos en us-east-1, el hypervisor KVM y la red subyacente (VPC, subred, Internet Gateway). El equipo, por su parte, tuvo control completo sobre el SO Ubuntu 24.04, instaló Docker, configuró los contenedores y definió las reglas del Security Group. Esta división de responsabilidades distingue el modelo IaaS del PaaS (donde el proveedor también gestiona el runtime y el middleware) y del SaaS (donde el proveedor gestiona todo, incluida la aplicación).

### Relación entre virtualización y computación en la nube

La virtualización es la tecnología fundamental que hace posible la computación en la nube. La instancia EC2 `t2.micro` no corre sobre hardware dedicado: es una máquina virtual sobre un servidor físico de AWS que simultáneamente aloja decenas de instancias de diferentes clientes, completamente aisladas entre sí gracias al hypervisor KVM (Kernel-based Virtual Machine) de Tipo 1.

La virtualización habilita dos características esenciales de la nube: la **elasticidad** (aprovisionar o liberar recursos en segundos, sin intervención física) y la **eficiencia de utilización** (los servidores físicos de AWS operan al 60–80% de capacidad, frente al 5–15% de los servidores dedicados tradicionales). Ambas características hacen económicamente viable el modelo de pago por uso de la nube pública.

### Función de la red virtual (VPC)

La **Virtual Private Cloud (VPC)** `vpc-09fba6709ef341e18` es una red virtual aislada lógicamente dentro de la infraestructura de AWS. En el entorno implementado cumple tres funciones específicas y verificables:

**Aislamiento:** la instancia `172.31.20.85` pertenece a un espacio de direccionamiento privado (`172.31.0.0/16`) inaccesible desde Internet de forma directa. Solo el tráfico explícitamente permitido por el Security Group puede entrar o salir.

**Control de tráfico:** el grupo `launch-wizard-3` actúa como firewall stateful a nivel de instancia. Solo los puertos 22 (SSH) y 80 (HTTP) están abiertos al exterior. El puerto 5432 de PostgreSQL nunca fue expuesto a Internet — solo es accesible dentro de la red Docker interna.

**Enrutamiento:** el gateway `172.31.16.1` gestiona el tráfico entre la instancia y el Internet Gateway de AWS, permitiendo conectividad de salida verificada con `ping -c 4 google.com` (4/4 paquetes, 0% pérdida, 1.512 ms) y de entrada a través de la IP pública `54.226.183.158`.
