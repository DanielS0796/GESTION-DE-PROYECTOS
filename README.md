# GESTION-DE-PROYECTOS

# 🔐 Biosecurity - Sistema de Control de Acceso Biométrico

Sistema de reconocimiento facial para control de acceso a instalaciones, desarrollado sobre arquitectura serverless en AWS.

---

## 🏗️ Arquitectura

![Arquitectura](arquitectura.png)

El sistema está construido sobre una arquitectura **100% serverless** en AWS, lo que elimina la necesidad de gestionar servidores, VPCs o infraestructura tradicional. AWS administra internamente la disponibilidad, escalabilidad y seguridad de la red.

---

## 🧩 Componentes del Sistema

### Frontend (PWA)
| Componente | Descripción | URL |
|---|---|---|
| Terminal de Acceso | Interfaz para trabajadores en la puerta | `/` |
| Panel RRHH | Registro de nuevos empleados (protegido) | `/rrhh/index.html` |
| Panel Auditoría | Visualización y exportación de accesos (protegido) | `/auditoria/index.html` |

### Servicios AWS
| Servicio | Uso |
|---|---|
| **CloudFront** | CDN global con HTTPS para servir el frontend |
| **S3** | Almacenamiento del frontend con cifrado KMS |
| **API Gateway** | 3 endpoints REST (público, RRHH, auditoría) |
| **Lambda** | 3 funciones serverless (validar, registrar, auditoría) |
| **Rekognition** | Motor de reconocimiento facial biométrico |
| **DynamoDB** | Base de datos NoSQL para empleados y registro de accesos |
| **Cognito** | Autenticación de usuarios para paneles protegidos |
| **KMS** | Cifrado de datos biométricos en reposo |
| **IAM** | Control de permisos mínimos por función |

---

## 🔄 Flujo del Sistema

### 1. Terminal de Acceso (Público)
- El trabajador abre la PWA en el celular dedicado para esto como terminal de entrada
- La cámara captura su rostro
- La imagen se envía al API Gateway público
- La Lambda consulta Rekognition con `SearchFacesByImage`
- Si hay coincidencia → Acceso Permitido + registro en DynamoDB
- Si no hay coincidencia → Acceso Denegado + registro en DynamoDB

### 2. Panel RRHH (Protegido con API Key + Cognito)
- RRHH ingresa con usuario y contraseña via Cognito
- Ingresa cédula, nombre y toma foto del empleado
- La Lambda usa `IndexFaces` para registrar el rostro en Rekognition
- Los datos del empleado se guardan en DynamoDB

### 3. Panel Auditoría (Protegido con API Key + Cognito)
- El administrador ingresa con contraseña
- Visualiza todos los accesos con fecha, hora, identificación y resultado
- Puede filtrar por rango de fechas
- Puede exportar el reporte en formato CSV

---

## 🔒 Seguridad

- **KMS** — Todas las fotos y datos en S3 cifrados con AES-256
- **API Keys** — Los endpoints de RRHH y auditoría requieren clave de acceso
- **Cognito** — Autenticación real con tokens JWT para paneles administrativos
- **IAM** — Cada Lambda tiene únicamente los permisos que necesita
- **HTTPS** — Todo el tráfico pasa por CloudFront con TLS
- **Aislamiento** — Los trabajadores no pueden acceder al registro ni a la auditoría

---

## 🛠️ Infraestructura como Código (Terraform)

El proyecto usa **Terraform** para aprovisionar toda la infraestructura de forma automática y reproducible.

### Recursos gestionados por Terraform
- S3 bucket con cifrado KMS y hosting estático
- 3 API Gateways con CORS configurado
- 3 funciones Lambda en Node.js 22.x
- 2 tablas DynamoDB con índices secundarios
- CloudFront distribution con HTTPS
- Cognito User Pool y cliente web
- KMS key con rotación automática anual
- IAM roles y políticas de mínimo privilegio
- Rekognition Collection

### Despliegue
```bash
git clone https://github.com/TU_USUARIO/biosecurity-rekognition
cd biosecurity-rekognition
terraform init
terraform plan
terraform apply
```

---

## 📡 Endpoints

| Endpoint | Método | Acceso | Descripción |
|---|---|---|---|
| `/best/validar` | POST | Público | Validación biométrica |
| `/prod/registrar` | POST | API Key | Registro de empleados |
| `/prod/reporte` | GET | API Key | Reporte de auditoría |

---

## 🖥️ URLs del Sistema

| Panel | URL |
|---|---|
| Terminal acceso | `https://d2oixn9k6l57iy.cloudfront.net` |
| Panel RRHH | `https://d2oixn9k6l57iy.cloudfront.net/rrhh/index.html` |
| Panel Auditoría | `https://d2oixn9k6l57iy.cloudfront.net/auditoria/index.html` |

---

## 👥 Tecnologías

![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazon-aws&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=flat&logo=terraform&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)

# 🏗️ ARQUITECTURA TERRAFORM

---
<img width="1201" height="2365" alt="arquitectura" src="https://github.com/user-attachments/assets/38194738-56a6-4390-8624-e5c10357d9ce" />


Se realizo la creación de tres endpoints funcionales totalmente

Terminal de acceso Trabajadores

https://d2oixn9k6l57iy.cloudfront.net

PANEL RRHH 

USUARIO: rrhh
CONTRASEÑA: Rrhh2024!
 
https://d2oixn9k6l57iy.cloudfront.net/rrhh/index.html

Panel de Auditoria o administración

Contraseña: Admin2024!

https://d2oixn9k6l57iy.cloudfront.net/auditoria/index.html


Adcionalmente agregamos capturas de nuestros endpoints y tambien de nuestros servicios de amazon utilizados

APIs utilizadas

<img width="1302" height="440" alt="imagen" src="https://github.com/user-attachments/assets/331f8e35-7f17-42cb-9d19-3695e44378d4" />

ROLES UTILIZADOS 

<img width="1359" height="431" alt="imagen" src="https://github.com/user-attachments/assets/ce9ca782-5a32-4cf8-a9f7-43f60c852680" />

BUCKETS CREADOS 

<img width="762" height="262" alt="imagen" src="https://github.com/user-attachments/assets/b9dab45e-3c7a-492d-a22d-03a5b208b7af" />

OBJETOS CREADOS EN EL BUCKET BIO SECURITY

<img width="1131" height="421" alt="imagen" src="https://github.com/user-attachments/assets/86294ff6-884b-4090-b6b4-99f1696258b6" />

LAMBDAS UTILIZADAS

<img width="1131" height="224" alt="imagen" src="https://github.com/user-attachments/assets/3b754248-a915-4ed7-a0e8-a10d294af5e9" />







