---
title: Exemples de modèles Azure Resource Manager pour les cibles et les fonctionnalités dans Azure Chaos Studio
description: Exemples de modèles Azure Resource Manager pour intégrer des ressources à Azure Chaos Studio en utilisant des cibles et des fonctionnalités
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: 6e5c6b0610d2a2d48523a5d2a7a95c28cd8bae59
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404365"
---
# <a name="resource-manager-template-samples-for-targets-and-capabilities-in-azure-chaos-studio"></a>Exemples de modèles Resource Manager pour les cibles et les fonctionnalités dans Azure Chaos Studio
Cet article contient des exemples de [modèle Azure Resource Manager](../azure-resource-manager/templates/syntax.md) pour créer des [cibles et des fonctionnalités](chaos-studio-targets-capabilities.md) afin d’intégrer une ressource à Azure Chaos Studio. Chaque exemple comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

## <a name="onboard-service-direct-target-and-capabilities-single-capability"></a>Intégrer une cible et des fonctionnalités de service direct (fonctionnalité unique)

Dans cet exemple, nous intégrons une instance d’Azure Cosmos DB avec [des cibles et des fonctionnalités](chaos-studio-targets-capabilities.md). Le modèle peut être modifié pour toute cible et fonctionnalité de service direct en référençant la [bibliothèque d’erreurs](chaos-studio-fault-library.md).

### <a name="template-file"></a>Fichier de modèle

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.DocumentDB/databaseAccounts/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-CosmosDB/Failover-1.0')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.DocumentDB/databaseAccounts', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-CosmosDB')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-cosmos-db"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="onboard-service-direct-target-and-capabilities-multiple-capabilities"></a>Intégrer une cible et des fonctionnalités de service direct (plusieurs fonctionnalités)

Dans cet exemple, nous intégrons un cluster Azure Kubernetes Service avec [des cibles et des fonctionnalités](chaos-studio-targets-capabilities.md).

### <a name="template-file"></a>Fichier de modèle

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource being enabled."
      }
    },
    "resourceGroup": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group where the resource being enabled is located."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location"
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh')]",
      "location": "[parameters('location')]",
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/NetworkChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/PodChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/StressChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/IOChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/TimeChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/KernelChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/DNSChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    },
    {
      "type": "Microsoft.ContainerService/managedClusters/providers/targets/capabilities",
      "apiVersion": "2021-09-15-preview",
      "name": "[concat(parameters('resourceName'), '/', 'Microsoft.Chaos/Microsoft-AzureKubernetesServiceChaosMesh/HTTPChaos-2.1')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat(resourceId('Microsoft.ContainerService/managedClusters', parameters('resourceName'), parameters('resourceGroup')), '/', 'providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh')]"
      ],
      "properties": {}
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "resourceName": {
        "value": "my-aks-cluster"
      },
      "resourceGroup": {
        "value": "my-rg"
      }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez-en plus sur Chaos Studio](chaos-studio-overview.md).
* [Découvrez-en plus sur les cibles et les fonctionnalités](chaos-studio-targets-capabilities.md).
