[![Deploy to Amazon ECS](https://github.com/amurpo/desafio3-devops/actions/workflows/ecs.yml/badge.svg)](https://github.com/amurpo/desafio3-devops/actions/workflows/ecs.yml)

# Desafío 3 - DevOps

Este es un proyecto que implementa un servidor web utilizando **Node.js** y **Express**. El código está desplegado en una instancia de **ECS** en AWS.

## Requisitos

- Docker instalado.
- Cuenta de AWS con permisos para ECS, ECR y IAM.
- Node.js y npm instalados localmente.
- Git configurado.

Configurar los secretos necesarios para GitHub Actions:
   - **AWS_ACCESS_KEY_ID**: La clave de acceso de AWS.
   - **AWS_SECRET_ACCESS_KEY**: La clave secreta de AWS.
   - **ECR_REPOSITORY**: El nombre del repositorio ECR.
   - **ECS_CLUSTER**: El nombre del clúster ECS.
   - **ECS_SERVICE_NAME**: El nombre del servicio ECS.

   Estos valores se pueden configurar en la sección de **Settings > Secrets and variables > Actions** del repositorio en GitHub.

## Flujo de trabajo de GitHub Actions

El proyecto incluye un flujo de trabajo en `.github/workflows/ecs.yml` que:

1. Construye la imagen Docker.
2. La sube al repositorio de Amazon ECR.
3. Actualiza el servicio en Amazon ECS con la nueva imagen.

### Pasos principales

1. **Inicio de sesión en ECR:**
   El flujo de trabajo usa `aws-actions/amazon-ecr-login` para autenticar en ECR.

2. **Construcción de la imagen:**
   La imagen se construye y etiqueta usando el hash del commit de GitHub:
   ```bash
   docker build -t ${{ secrets.ECR_REPOSITORY }}:${{ github.sha }} .
   ```

3. **Push a ECR:**
   ```bash
   docker push ${{ secrets.ECR_REPOSITORY }}:${{ github.sha }}
   ```

4. **Despliegue en ECS:**
   Se utiliza `ecs-deploy` para actualizar el servicio ECS:
   ```bash
   ecs-deploy \
     --cluster ${{ secrets.ECS_CLUSTER }} \
     --service-name ${{ secrets.ECS_SERVICE_NAME }} \
     --image ${{ secrets.ECR_REPOSITORY }}:${{ github.sha }}
   ```

## Acceso al servidor

Una vez desplegado, el servidor estará disponible en la URL configurada.
