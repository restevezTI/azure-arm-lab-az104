Automatización de discos administrados usando plantillas ARM exportadas del portal

Este laboratorio muestra cómo crear un disco administrado en Azure, exportar su plantilla ARM, entender su estructura y reutilizarla para desplegar nuevos discos modificando parámetros como el tamaño, SKU o nombre. Es un ejercicio fundamental para dominar Infraestructura como Código (IaC) en el AZ-104.

📌 Objetivos del laboratorio
Crear un Managed Disk desde el portal de Azure.

Exportar la ARM Template generada automáticamente.

Analizar la estructura de la plantilla.

Crear nuevos discos reutilizando la plantilla.

Modificar parámetros clave:

Tamaño (GB)

SKU (StandardSSD, PremiumSSD, etc.)

Nombre del disco

Validar el SKU y propiedades del disco creado.

🧰 Prerrequisitos
Suscripción activa de Azure.

Visual Studio Code o editor de texto.

Azure CLI o Cloud Shell.

Conocimientos básicos de ARM Templates.

🏗️ 1. Crear el Resource Group
Portal de Azure
Accede a https://portal.azure.com.

Ve a Resource groups → Create.

Configura:

Name: MILABARM01

Region: West Europe

Pulsa Review + Create → Create

💽 2. Crear un disco administrado (disco base)
Este disco será el que exportaremos como plantilla.

Portal de Azure
Create a resource → busca Managed Disk.

Configura:

Resource group: MILABARM

Disk name: disk-base-01

Region: West Europe

Source type: None (disco vacío)

Size: 32 GiB

SKU: StandardSSD_LRS

Pulsa Review + Create → Create

📤 3. Exportar la plantilla ARM del disco
Ve a Resource groups → MILABARM01.

Selecciona el disco disk-base-01.

En el menú izquierdo, pulsa Automation\Export template.

Descarga:

azuredeploy.json

azuredeploy.parameters.json (si aparece)

Esta plantilla describe exactamente cómo Azure creó el disco.

🧩 4. Entender la plantilla ARM del disco
La plantilla suele incluir:

4.1 Parámetros
json
"parameters": {
  "diskName": {
    "type": "string"
  },
  "diskSizeGB": {
    "type": "int",
    "defaultValue": 32
  },
  "skuName": {
    "type": "string",
    "defaultValue": "StandardSSD_LRS"
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
4.2 Recurso del disco
json
"resources": [
  {
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2023-01-02",
    "name": "[parameters('diskName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "[parameters('skuName')]"
    },
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": "[parameters('diskSizeGB')]"
    }
  }
]
📝 5. Crear archivo de parámetros para nuevos discos
Ejemplo de azuredeploy.parameters.json:

json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "value": "disk-new-01"
    },
    "diskSizeGB": {
      "value": 64
    },
    "skuName": {
      "value": "PremiumSSD_LRS"
    },
    "location": {
      "value": "westeurope"
    }
  }
}
Con este archivo puedes crear discos con diferentes tamaños, nombres y SKUs sin tocar la plantilla. Se pueden anadir parametros mas avanzados en el AZURE ARM TEMPLATE, buscando el recurso especifico a tratar con su correspondiente proveedor, EJ. "Microsoft.Compute/disks"

🚀 6. Desplegar nuevos discos usando la plantilla ARM
6.1 Azure CLI
bash
az deployment group create \
  --resource-group rg-disk-arm \
  --template-file azuredeploy.json \
  --parameters @azuredeploy.parameters.json
Azure CLI
bash
az disk show \
  --resource-group rg-disk-arm \
  --name disk-new-01 \
  --query "sku"
PowerShell
powershell
(Get-AzDisk -ResourceGroupName "rg-disk-arm" -DiskName "disk-new-01").Sku
También puedes verificar:

bash
az disk show --resource-group rg-disk-arm --name disk-new-01
🧹 8. Limpieza del laboratorio
Azure CLI
bash
az group delete \
  --name rg-disk-arm \
  --yes --no-wait
PowerShell
powershell
Remove-AzResourceGroup `
  -Name "rg-disk-arm" `
  -Force
  
📝 Autor
Roberto — Ingeniero en Telecomunicaciones y Electrónica  
Preparando AZ-104 y documentando laboratorios para la comunidad técnica.
