# Nubestock Workflows - Workflows Reutilizables

Este repositorio contiene workflows reutilizables de GitHub Actions para desplegar aplicaciones de Nubestock a Azure.

## Workflows Disponibles

### `functions-deploy-action.yml`

Workflow reutilizable para desplegar Azure Functions con soporte para múltiples ambientes (DEV, UAT, PRD).

**Características:**
- Soporte para múltiples ambientes mediante GitHub Environments
- Validación de secrets antes del despliegue
- Sincronización automática de variables de entorno a Azure Function App
- Build y deploy automatizado

## Uso Rápido

### 1. En tu repositorio del backend, crea `.github/workflows/deploy.yml`:

```yaml
name: Deploy Backend to Azure Functions

on:
  push:
    branches:
      - develop
      - uat
      - master

jobs:
  deploy:
    uses: tu-org/nubestock-workflows/.github/workflows/functions-deploy-action.yml@main
    with:
      environment: ${{ github.ref_name == 'develop' && 'NUBESTOCK_DEV' || github.ref_name == 'uat' && 'NUBESTOCK_UAT' || github.ref_name == 'master' && 'NUBESTOCK_PRD' || 'NUBESTOCK_DEV' }}
    secrets:
      AZ_CLIENT_ID: ${{ secrets.AZ_CLIENT_ID }}
      AZ_TENANT_ID: ${{ secrets.AZ_TENANT_ID }}
      AZ_SUBSCRIPTION_ID: ${{ secrets.AZ_SUBSCRIPTION_ID }}
      AZ_FUNCTION_APP_NAME: ${{ secrets.AZ_FUNCTION_APP_NAME }}
      AZ_RESOURCE_GROUP: ${{ secrets.AZ_RESOURCE_GROUP }}
      DATABASE_HOSTNAME: ${{ secrets.DATABASE_HOSTNAME }}
      DATABASE_PORT: ${{ secrets.DATABASE_PORT }}
      DATABASE_USERNAME: ${{ secrets.DATABASE_USERNAME }}
      DATABASE_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
      DATABASE_NAME: ${{ secrets.DATABASE_NAME }}
```

### 2. Configura los secrets en tu repositorio del backend

Los secrets deben estar configurados por environment en tu repositorio del backend:
- `NUBESTOCK_DEV` (para rama `develop`)
- `NUBESTOCK_UAT` (para rama `uat`)
- `NUBESTOCK_PRD` (para rama `master`)

Ver [ENVIRONMENTS_SETUP.md](.github/ENVIRONMENTS_SETUP.md) para más detalles.

## 📚 Documentación

- [Guía de Configuración de Environments](.github/ENVIRONMENTS_SETUP.md) - Configuración detallada de secrets y environments
- [Ejemplo de Workflow](.github/workflows/example-backend-deploy.yml) - Ejemplo completo de cómo usar el workflow

## Secrets Requeridos

Cada environment debe tener los siguientes secrets configurados:

### Azure (Autenticación)
- `AZ_CLIENT_ID`
- `AZ_TENANT_ID`
- `AZ_SUBSCRIPTION_ID`

### Azure (Recursos)
- `AZ_FUNCTION_APP_NAME`
- `AZ_RESOURCE_GROUP`

### Base de Datos
- `DATABASE_HOSTNAME`
- `DATABASE_PORT`
- `DATABASE_USERNAME`
- `DATABASE_PASSWORD`
- `DATABASE_NAME`

## Mapeo de Ramas a Environments

- `develop` → `NUBESTOCK_DEV`
- `uat` → `NUBESTOCK_UAT`
- `master` → `NUBESTOCK_PRD`

## ⚙️ Configuración

1. **Permisos del Repositorio**: El repositorio que llama al workflow debe tener permisos para leer este repositorio de workflows.

2. **Environments**: Los environments deben estar configurados en el repositorio que llama al workflow, no en este repositorio.

3. **Secrets**: Todos los secrets deben estar configurados en el repositorio del backend, organizados por environment.

## Notas

- Este es un workflow **reutilizable**, no se ejecuta directamente desde este repositorio
- Los secrets se resuelven desde el repositorio que **llama** al workflow
- Cada environment tiene sus propios secrets aislados para mayor seguridad

