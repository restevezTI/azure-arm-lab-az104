Laboratorio ARM – Creación y Replicación de Discos en Azure usando ARM Templates + Azure CLI
Este laboratorio muestra cómo crear discos administrados en Azure utilizando ARM Templates y desplegarlos exclusivamente con Azure CLI. El flujo consiste en:

Crear un disco manualmente en Azure Portal.

Exportar su plantilla ARM (Plantilla_disco.json).

Modificar esa plantilla para generar nuevos discos (LAB01.json).

Ajustar parámetros como nombre, tamaño, SKU, zona, etc.

Desplegar la plantilla con Azure CLI.

Verificar el SKU y propiedades del disco creado.

📁 Archivos del laboratorio
1. Plantilla_disco.json
Plantilla ARM exportada desde Azure Portal.
Contiene un disco de 32 GB, SKU Premium_LRS, tier P4, zona 1.

Fragmento real del archivo:

"diskSizeGB": 32  
"sku": { "name": "Premium_LRS", "tier": "Premium" }

2. LAB01.json
Plantilla ARM creada a partir de la anterior.
Contiene un disco de 16 GB, mismo SKU y tier, con parámetro renombrado.

Fragmento real del archivo:

"diskSizeGB": 16  
"name": "[parameters('disks_name')]"

🎯 Objetivo del laboratorio
Comprender cómo Azure genera una plantilla ARM al exportar un disco.

Modificar esa plantilla para crear nuevos discos.

Ajustar parámetros clave:

Nombre del disco

Tamaño (GB)

SKU

Zona

Tier

Desplegar la plantilla solo con Azure CLI.

Validar el SKU del disco creado.

🧩 Estructura de la plantilla ARM
Ambas plantillas comparten la misma estructura:

Parámetros
json
"parameters": {
    "disks_name": {
        "defaultValue": "MILAB01ARM",
        "type": "String"
    }
}
Recurso del disco
json
"type": "Microsoft.Compute/disks",
"apiVersion": "2025-01-02",
"location": "westeurope",
"sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
},
"zones": ["1"],
"properties": {
    "creationData": { "createOption": "Empty" },
    "diskSizeGB": 16 | 32,
    "diskIOPSReadWrite": 120,
    "diskMBpsReadWrite": 25,
    "encryption": { "type": "EncryptionAtRestWithPlatformKey" },
    "tier": "P4"
}
📝 Archivo de parámetros para Azure CLI
Ejemplo de params.json:

json
{
  "parameters": {
    "disks_name": {
      "value": "DISK-NUEVO-CLI"
    }
  }
}
Si quieres cambiar el tamaño del disco, edítalo directamente en la plantilla:

json
"diskSizeGB": 64
Si quieres cambiar el SKU:

json
"sku": {
  "name": "StandardSSD_LRS",
  "tier": "Standard"
}
🚀 Despliegue con Azure CLI
1. Crear el Resource Group
bash
az group create \
  --name rg-arm-disk \
  --location westeurope
2. Desplegar la plantilla ARM
Usando la plantilla modificada (LAB01.json):

bash
az deployment group create \
  --resource-group rg-arm-disk \
  --template-file LAB01.json \
  --parameters @params.json
Si quieres desplegar la plantilla original:

bash
az deployment group create \
  --resource-group rg-arm-disk \
  --template-file Plantilla_disco.json \
  --parameters @params.json
🔍 Verificar el SKU del disco creado
Azure CLI
bash
az disk show \
  --resource-group rg-arm-disk \
  --name DISK-NUEVO-CLI \
  --query "sku"
Ver propiedades completas:

bash
az disk show \
  --resource-group rg-arm-disk \
  --name DISK-NUEVO-CLI
🧹 Eliminar el laboratorio
bash
az group delete \
  --name rg-arm-disk \
  --yes --no-wait
📚 Conclusiones
Exportar plantillas ARM es una forma excelente de aprender IaC.

Las plantillas de discos son simples y fáciles de modificar.

Azure CLI permite desplegar discos de forma repetible y automatizada.

Cambiar tamaño, SKU o nombre es tan simple como editar un parámetro.

 
📝 Autor
Roberto — Ingeniero en Telecomunicaciones y Electrónica  
Preparando AZ-104 y documentando laboratorios para la comunidad técnica.
