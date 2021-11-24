---
title: Provisionner une application web avec le cache Azure pour Redis
description: Utilisez un modèle Azure Resource Manager pour déployer une application web avec le cache Azure pour Redis.
services: app-service
author: curib
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: cauribeg
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ca60db1da300bd0b9576a2f08f9cf28e5cf01b66
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298510"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Créer une application web avec un cache Azure pour Redis à l’aide d’un modèle

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application web Azure avec Azure Cache pour Redis. Vous découvrirez les informations suivantes sur les déploiements :

- comment définir les ressources à déployer ;
- comment définir les paramètres spécifiés lors de l’exécution du déploiement.

Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/templates/syntax.md). Pour en savoir plus sur les propriétés et la syntaxe JSON des types de ressources de cache, consultez [Types de ressources Microsoft.Cache](/azure/templates/microsoft.cache/allversions).

Pour obtenir l’intégralité du modèle, consultez la page [Application web avec un modèle de cache Azure pour Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer

Dans ce modèle, vous allez déployer les éléments suivants :

- Application web Azure
- Cache Azure pour Redis

Pour exécuter automatiquement le déploiement, sélectionnez le bouton ci-dessous :

[![Déployer sur Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fweb-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Paramètres à spécifier
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variables pour les noms
Ce modèle utilise des variables afin de créer des noms pour les ressources. Il utilise la fonction [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) pour construire une valeur basée sur l’identifiant du groupe de ressources.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Ressources à déployer
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Cache Azure pour Redis
Crée le cache Azure pour Redis utilisé avec l’application web. Le nom du cache est spécifié dans la variable **cacheName** .

Le modèle crée le cache au même emplacement que le groupe de ressources.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app-azure-cache-for-redis"></a>Application web (Azure Cache pour Redis)
Crée l’application web avec le nom spécifié dans la variable **webSiteName** .

Notez que l’application web est configurée selon des paramètres qui lui permettent d’utiliser le cache Azure pour Redis. Ces paramètres sont créés de façon dynamique en fonction des valeurs fournies lors du déploiement.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```


### <a name="web-app-redisenterprise"></a>Application web (RedisEnterprise)
Pour RedisEnterprise, étant donné que les types de ressources sont légèrement différents, la manière de créer des **listKeys** est différente :

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/RedisEnterprise/databases/', variables('cacheName'), "/default")]",
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/RedisEnterprise', variables('cacheName'), 'default'), '2020-03-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Commandes pour l’exécution du déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```