# Fase 3 — Instalación y administración de Docker

## Objetivo

Instalar Docker Engine en la VM de AWS EC2 y demostrar su funcionamiento.

## Comandos de instalación

```bash
# Actualizar paquetes
sudo apt-get update

# Instalar dependencias
sudo apt-get install -y ca-certificates curl gnupg

# Agregar GPG key oficial de Docker
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Agregar repositorio Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Agregar usuario al grupo docker
sudo usermod -aG docker $USER
```

## Verificación del servicio

```bash
sudo systemctl status docker
docker --version
docker info
```

## Contenedor de prueba

```bash
docker run hello-world
```

## Evidencias requeridas

| # | Evidencia | Archivo |
|---|-----------|---------|
| 1 | Instalación Docker Engine | `evidencias/docker-install.png` |
| 2 | Verificación del servicio | `evidencias/docker-status.png` |
| 3 | Ejecución hello-world | `evidencias/docker-hello-world.png` |
| 4 | Arquitectura Docker | `evidencias/docker-arquitectura.png` |

## Análisis conceptual

### Cómo el SO administra recursos para contenedores

> 🔄 *Pendiente — completar tras la instalación*

### Diferencias entre contenedor y máquina virtual

> 🔄 *Pendiente — completar tras la instalación*

### Implicaciones para la escalabilidad

> 🔄 *Pendiente — completar tras la instalación*
