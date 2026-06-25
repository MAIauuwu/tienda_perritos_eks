# tienda_perritos_eks

http://a349654cff2df4f6880eac574e49e712-1413638327.us-east-1.elb.amazonaws.com/
# 🐾 Tienda de Perritos EKS - Despliegue Automatizado (EP3)

Este repositorio contiene la configuración e infraestructura automatizada para el despliegue de la aplicación **Tienda de Perritos** en un clúster administrado de Kubernetes utilizando **Amazon EKS** y **GitHub Actions**.

## 🏗️ Arquitectura del Sistema
La infraestructura en la nube está diseñada para soportar alta disponibilidad, tolerancia a fallos y escalabilidad automática:
* **Orquestador:** Amazon EKS (Elastic Kubernetes Service).
* **Registro de Imágenes:** Amazon ECR (Elastic Container Registry).
* **Base de Datos:** MySQL integrada mediante contenedores con almacenamiento persistente.
* **Seguridad:** Gestión de credenciales críticas (como contraseñas de BD) centralizada mediante `Kubernetes Secrets` para evitar la exposición de datos sensibles.
* **Balanceo de Carga:** Servicio de tipo `LoadBalancer` que expone un punto de enlace (DNS público de AWS) para dirigir el tráfico hacia los Pods de Frontend.

## 📈 Autoscaling y Monitoreo (HPA)
Se implementó el componente **Metrics Server** en el clúster para habilitar el autoescalado horizontal (**Horizontal Pod Autoscaler - HPA**).
* **Tienda Backend HPA:** Escala dinámicamente entre **2 y 10 réplicas** cuando el uso promedio de CPU supera el **70%**.
* **Tienda Frontend HPA:** Escala dinámicamente entre **2 y 6 réplicas** cuando el uso promedio de CPU supera el **60%**.

*Umbral justificado para garantizar un margen operativo del 30% en picos de tráfico repentinos antes de levantar nuevas réplicas, evitando la degradación del servicio.*

## 🚀 Pipeline de CI/CD (GitHub Actions)
El archivo de flujo de trabajo `.github/workflows/blank.yml` automatiza el ciclo completo con los siguientes pasos:
1. **Checkout Code:** Descarga el código fuente del repositorio.
2. **AWS Credentials:** Configura de forma segura el acceso a AWS Academy utilizando secrets de GitHub.
3. **Login to Amazon ECR:** Autentica el entorno con el registro de contenedores de AWS.
4. **Build, Tag & Push:** Compila las imágenes de Docker para `frontend`, `backend` y `db`, asignándoles la etiqueta `latest` y subiéndolas a ECR.
5. **Kubernetes Deploy:** Aplica las configuraciones de la carpeta `/k8s` de forma ordenada mediante `kubectl apply -f k8s/`, asegurando que el clúster refleje los cambios automáticamente.
