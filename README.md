# Evaluacion 2

## Arquitectura y Tecnologías
* **Base de Datos:** MySQL 8.0
* **Documentación:** Swagger / OpenAPI 3.0
* **Orquestación:** Docker Compose

## Contenedorización de Microservicios
Siguiendo las mejores prácticas de DevOps y los requisitos de la pauta de evaluación, ambos microservicios implementan:

1. **Multi-stage Build:** Se utiliza una etapa de compilación con `maven:3.8.5-openjdk-17` y una etapa final de ejecución ligera con `eclipse-temurin:17-jre-alpine`, reduciendo significativamente la superficie de ataque y el peso de la imagen.
2. **Seguridad (Non-root user):** Se ha configurado un usuario de sistema dedicado (`spring`) para ejecutar el archivo JAR. El contenedor no utiliza privilegios de root, cumpliendo con el principio de mínimo privilegio.

## CI/CD y Despliegue en AWS (Red Privada)
El pipeline de automatización implementado en GitHub Actions gestiona un despliegue complejo hacia una instancia EC2 situada en una subred protegida.

**Características del Pipeline:**
* **Trigger:** Se activa únicamente mediante `push` a la rama `deploy`.
* **Bastion Host (Proxy):** Debido a que el backend reside en una subred privada, el pipeline utiliza la instancia del Frontend como un **Proxy SSH** para realizar el despliegue de forma segura.
* **Automatización:** Realiza el build de ambas imágenes (Ventas y Despachos), las publica en Docker Hub y ejecuta el despliegue en caliente mediante `docker-compose up -d --force-recreate`.

## Configuración de Infraestructura (Docker Compose)
Para garantizar la estabilidad en instancias de recursos limitados (AWS Tier Gratuito), se han aplicado:
* **Límites de Memoria:** Restricción de RAM por contenedor para evitar el colapso del sistema.
* **Persistencia de Datos:** Uso de volúmenes nombrados para la base de datos MySQL, asegurando que la información de Innovatech Chile sea persistente ante reinicios.
* **Redes Aisladas:** Comunicación interna entre microservicios sin exponer la base de datos a internet.

## Despliegue
Si deseas desplegar los servicios sigue estos pasos:
1. Una vez estas conectado al frontend en la terminal, conectarte al backend utilizando la ip privada de EC2
2. Ingresar a la carpeta backend utilizando: cd backend
3. Es recomendable ejecutar los servicios uno por uno con estos comandos:

  * docker-compose up -d db
  
  * docker-compose up -d ventas
  
  * docker-compose up -d despachos


## 📁 Estructura del Proyecto
```text
├── back-Ventas_SpringBoot/     # Microservicio de Gestión de Ventas
├── back-Despachos_SpringBoot/  # Microservicio de Gestión de Despachos
├── .github/workflows/          # Pipeline de despliegue vía Bastion
└── docker-compose.yml          # Orquestación completa de servicios
