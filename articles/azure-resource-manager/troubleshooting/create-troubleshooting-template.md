---
title: Création d’un modèle de résolution des problèmes
description: Décrit comment créer un modèle pour dépanner des ressources Azure déployées avec des modèles Azure Resource Manager (modèles ARM) ou des fichiers Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 0c9b1a6ebd35a6ebe58b568a1a56e44c0395b630
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478970"
---
# <a name="create-a-troubleshooting-template"></a>Création d’un modèle de résolution des problèmes

Dans certains cas, la meilleure façon de dépanner votre modèle consiste à isoler et à tester des parties spécifiques de celui-ci. Vous pouvez créer un modèle de résolution des problèmes qui se concentre sur la ressource que vous pensez être à l’origine de l’erreur.

Par exemple, une erreur se produit lorsque votre modèle de déploiement fait référence à une ressource existante. Au lieu d’évaluer un modèle de déploiement entier, créez un modèle de résolution des problèmes qui retourne les données relatives à la ressource. Il peut vous aider à déterminer si vous transmettez les paramètres appropriés à l’aide de fonctions de modèle et si vous obtenez la ressource que vous attendez.

## <a name="deploy-a-troubleshooting-template"></a>Déploiement d’un modèle de résolution des problèmes

Le modèle ARM et le fichier Bicep suivants obtiennent des informations à partir d’un compte de stockage existant. Exécutez le déploiement avec l’instance [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Azure PowerShell ou l’instance [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) Azure CLI. Spécifiez le nom du compte de stockage et du groupe de ressources. La sortie est un objet avec les noms et les valeurs des propriétés du compte de stockage.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Dans Bicep, utilisez le mot clé `existing` et exécutez le déploiement à partir du groupe de ressources où se trouve le compte de stockage. Utilisez `scope` pour accéder à une ressource dans un groupe de ressources différent. Pour plus d’informations, consultez les [ressources existantes](../bicep/resource-declaration.md#existing-resources).

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

## <a name="alternate-troubleshooting-method"></a>Autre méthode de résolution des problèmes

Si vous pensez que les erreurs de déploiement sont provoquées par des dépendances incorrectes, vous pouvez exécuter des tests en scindant le modèle en modèles simplifiés. Commencez par créer un modèle déployant une seule ressource (comme un serveur SQL Server). Lorsque vous êtes sûr que le déploiement des ressources est correct, ajoutez une ressource qui en dépend (comme un SQL Database). Une fois ces deux ressources correctement définies, ajoutez les autres ressources dépendantes (telles que les stratégies d’audit). Supprimez le groupe de ressources entre chaque déploiement de test afin de tester correctement les dépendances.

## <a name="next-steps"></a>Étapes suivantes

- [Erreurs de déploiement courantes](common-deployment-errors.md)
- [Trouver les codes d’erreur](find-error-code.md)
- [Activer l’enregistrement du débogage](enable-debug-logging.md)
