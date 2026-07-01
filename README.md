
  GNU nano 9.0                                                                                        README.md                                                                                         Modified







# Bitácora de Despliegue: Infraestructura del Gimnasio

Este repositorio contiene la documentación técnica y los archivos de configuración de la infraestructura para la aplicación del gimnasio. El proyecto sigue una arquitectura separada (Poly-repo), manteniendo la configuración de DevOps independiente del código fuente de la aplicación.

---

## Fase 1: Aprovisionamiento y Configuración de Acceso

### 1. Preparación del Entorno Local
* **Acción:** Abrir terminal Git Bash.
* **Comando:** cd ~
* **Explicación:** Nos situamos en el directorio raíz del usuario local para mantener organizadas las llaves de acceso.

### 2. Generación de Credenciales Criptográficas (SSH)
* **Acción:** Crear un par de llaves asimétricas para la autenticación sin contraseña.
* **Comando:** ssh-keygen -t rsa -b 4096
* **Explicación:** Se genera una llave RSA de 4096 bits para asegurar que el canal de comunicación sea inmune a ataques de fuerza bruta.

### 3. Extracción de Llave Pública
* **Acción:** Mostrar el contenido de la llave pública para su autorización en la nube.
* **Comando:** cat ~/.ssh/id_rsa.pub
* **Explicación:** Este comando imprime el candado digital que se debe inyectar en el servidor remoto.

### 4. Provisionamiento del Servidor en DigitalOcean
* **Acción:** Creación de una instancia virtual (Droplet).
* **Configuración del Servidor:**
  * **Región:** Nueva York (NYC1)
  * **Sistema Operativo:** Ubuntu 24.04 (LTS) x64
  * **Plan:** Regular (Opción básica de $4/mes)
  * **Autenticación:** SSH Key (Pegando la llave pública obtenida en el punto 3)
  * **Hostname:** gimnasio-prod

### 5. Verificación de Conectividad Remota
* **Acción:** Establecer el primer túnel seguro hacia la IP pública.
* **Comando:** ssh root@159.65.223.239
* **Resultado:** Acceso exitoso al shell de administración root@gimnasio-prod.

---

## Fase 2: Intalación del Motor de Orquestación (Docker)

En esta fase se preparó el servidor remoto en DigitalOcean, transformándolo de una máquina virtual vacía a un entorno listo para ejecutar contenedores de manera aislada y automatizada.

### 1. Sincronización del Catálogo de Paquetes
* **Comando:** apt update
* **¿Qué se hizo?:** Se descargó la lista actualizada de los paquetes de software disponibles en los repositorios de Ubuntu para asegurar la descarga de las versiones más recientes.

### 2. Configuración de Seguridad y Repositorio Oficial de Docker
* **Comandos:**
  apt install apt-transport-https ca-certificates curl software-properties-common -y
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
### 3. Instalación de Docker Core y Dependencias de Bajo Nivel
* **Comando:** apt update && apt install docker-ce docker-ce-cli containerd.io -y
* **¿Qué se hizo?:** Se instaló el motor principal de Docker (docker-ce), la interfaz de comandos para el administrador (docker-ce-cli) y el obrero interno que maneja el ciclo de vida y aislamiento de los contenedores (containerd.io).

### 4. Verificación del Estado del Servicio de Docker
* **Comando:** systemctl status docker
* **¿Qué se hizo?:** Se auditó el estado del demonio de Docker en el sistema, confirmando que está en estado Active: active (running) y configurado en enabled para arrancar automáticamente si el servidor físico se reinicia.

### 5. Instalación del Orquestador de Planos (Docker Compose)
* **Comando:** apt install docker-compose-plugin -y
* **¿Qué se hizo?:** Se instaló la extensión oficial que permite a Docker interpretar y ejecutar archivos de configuración en formato YAML (docker-compose.yml), habilitando la infraestructura como código.
* **Verificación:** docker compose version (Arrojó exitosamente la versión v5.2.0).















































^G Help          ^O Write Out     ^F Where Is      ^K Cut           ^T Execute       ^C Location      M-U Undo         M-A Set Mark     M-] To Bracket   M-B Previous     ◂ Back           ^◂ Prev Word


