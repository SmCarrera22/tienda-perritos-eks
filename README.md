# Tienda Perritos - AWS EKS (EP3)

## Descripción del Proyecto

Este proyecto implementa una arquitectura completa en AWS utilizando **Amazon EKS (Elastic Kubernetes Service)** para la orquestación de contenedores de una aplicación web tipo “Tienda Perritos”.

La solución incluye:
- Frontend web (Nginx)
- Backend API (Node.js / servicio REST)
- Base de datos MySQL
- Despliegue en Kubernetes sobre EKS
- Imágenes en Amazon ECR
- CI/CD automatizado con GitHub Actions
- Autoscaling con Horizontal Pod Autoscaler (HPA)

---

## Arquitectura

La arquitectura implementada es la siguiente:

- **Frontend**: expuesto públicamente mediante `LoadBalancer`
- **Backend**: servicio interno `ClusterIP`
- **Base de datos MySQL**: servicio interno `ClusterIP`
- Comunicación:
- Frontend → Backend mediante proxy Nginx (`/api → backend-service`)
- Contenedores almacenados en **Amazon ECR**
- Orquestación en **Amazon EKS**

---

## Servicios AWS utilizados

- Amazon EKS
- Amazon ECR
- EC2 Node Groups (SPOT instances)
- VPC + Subnets públicas/privadas
- Security Groups
- IAM Roles (ClusterRole, NodeRole)
- CloudWatch (logs opcionales)
- Load Balancer (AWS ELB)

---

## Despliegue en Kubernetes

### Namespace

    kubectl apply -f k8s/namespace.yaml

### Aplicar todos los recursos

    kubectl apply -f k8s/

### Ver recursos

    kubectl get all -n tienda

## Acceso a la aplicación

Frontend público:

    http://a20dcbc6baaff4c14ae1a336ff1e6f1c-1140613308.us-east-1.elb.amazonaws.com

## CI/CD (GitHub Actions)

El pipeline automatiza:

1. Build de imágenes Docker
2. Push a Amazon ECR
3. Deploy automático en EKS

Flujo:

    GitHub Push → GitHub Actions → Docker Build → ECR Push → kubectl deploy → EKS

## Autoscaling (HPA)

Se implementó Horizontal Pod Autoscaler:

* CPU target: 50%
* Min replicas: 1
* Max replicas: 3

Ver HPA:

    kubectl get hpa -n tienda

## Métricas y monitoreo

Ver CPU / memoria:

    kubectl top pods -n tienda
    kubectl top nodes

## Comunicación entre servicios

* Frontend utiliza Nginx como reverse proxy:

    location /api/ {proxy_pass http://tienda-backend:3001;}

* Backend accesible solo dentro del cluster mediante DNS Kubernetes:

    http://tienda-backend.tienda.svc.cluster.local

