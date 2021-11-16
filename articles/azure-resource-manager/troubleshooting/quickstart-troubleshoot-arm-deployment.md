---
title: Résoudre les problèmes des déploiements de modèles ARM
description: Découvrez comment résoudre les problèmes rencontrés lors du déploiement d’un modèle Azure Resource Manager (modèle ARM).
ms.date: 11/08/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce9bf9ee097a41b3e3ecbc0cd515dd58cf30ea82
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062046"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Démarrage rapide : Résolution des problèmes de déploiement de modèles ARM

Ce guide de démarrage rapide explique comment résoudre les erreurs de déploiement des modèles Azure Resource Manager (ARM). Vous allez configurer un modèle avec des erreurs et apprendre à corriger les erreurs.

Il existe trois types d’erreurs liées à un déploiement :

- Les **erreurs de validation** se produisent avant le début d’un déploiement et sont provoquées par des erreurs de syntaxe dans votre fichier. Votre éditeur peut identifier ces erreurs.
- Des **erreurs de validation préalable** se produisent lorsqu’une commande de déploiement est exécutée, mais que les ressources ne sont pas déployées. Ces erreurs sont détectées sans commencer le déploiement. Par exemple, si une valeur de paramètre est incorrecte, l’erreur est détectée lors de la validation préalable.
- Les **erreurs de déploiement** se produisent pendant le processus de déploiement et ne peuvent être détectées qu’en évaluant la progression du déploiement.

Tous les types d’erreurs retournent un code d’erreur que vous utilisez pour résoudre les problèmes de déploiement. Les erreurs de validation et les erreurs de vérification préalable sont affichées dans le journal d’activité, mais n’apparaissent pas dans votre historique de déploiement.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez disposer des éléments suivants :

- Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Visual Studio Code](https://code.visualstudio.com/) avec la dernière version de l’extension [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
- Installez la version la plus récente d’[Azure PowerShell](/powershell/azure/install-az-ps) ou [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-template-with-errors"></a>Création d’un modèle comportant des erreurs

Copiez le modèle suivant et enregistrez-le localement. Vous allez utiliser ce fichier pour résoudre une erreur de validation, une erreur validation préalable et une erreur de déploiement. Ce guide de démarrage rapide part du principe que vous avez nommé le fichier _troubleshoot.json_, mais vous pouvez lui attribuer n’importe quel nom.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameterss": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    },
    "vnetResult": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
    }
  }
}
```

## <a name="fix-validation-error"></a>Corriger une erreur de validation

Ouvrez le fichier dans Visual Studio Code. La ligne ondulée sous `parameterss:` indique une erreur. Pour afficher l’erreur de validation, pointez sur l’erreur.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-error.png" alt-text="Capture d’écran d’une erreur de validation de modèle dans Visual Studio Code.":::

Vous remarquerez que `variables` et `resources` présentent des erreurs liées à une _référence de paramètre non définie_. Pour afficher les erreurs de validation du modèle, sélectionnez **Afficher** > **Problèmes**.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/validation-undefined-parameter.png" alt-text="Capture d’écran de Visual Studio Code qui montre des erreurs de référence de paramètre non définie.":::

Toutes les erreurs sont dues à l’orthographe incorrecte d’un nom d’élément.

```json
"parameterss": {
```

Le message d’erreur indique que _Échec de la validation du modèle : le membre « parameterss » est introuvable sur l’objet de type « Template ». Chemin « parameterss », ligne 4, position 16_.

La syntaxe du modèle ARM pour [parameters](../templates/syntax.md#parameters) indique que `parameters` est le nom d’élément correct.

Pour corriger l’erreur de validation et les erreurs de _référence de paramètre non définie_, corrigez l’orthographe et enregistrez le fichier.

```json
"parameters": {
```

## <a name="fix-preflight-error"></a>Corriger une erreur de validation préalable

Pour créer une erreur de validation préalable, vous affecterez une valeur incorrecte au paramètre `prefixName`.

Ce guide de démarrage rapide utilise _troubleshootRG_ comme nom du groupe de ressources, mais vous pouvez utiliser n’importe quel nom.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=long!!StoragePrefix
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName long!!StoragePrefix
```

---

La validation préalable du modèle échoue et le déploiement n’est pas exécuté. Le `prefixName` comporte plus de 11 caractères, et contient des caractères spéciaux et des lettres majuscules.

Les noms de stockage doivent avoir entre 3 et 24 caractères de type lettres minuscules et chiffres. La valeur de préfixe a créé un nom de stockage non valide. Pour plus d’informations, voir [Résoudre les erreurs liées aux noms de compte de stockage](error-storage-account-name.md). Pour corriger l’erreur de vérification préalable, utilisez un préfixe de 11 caractères ou moins et ne contenant que des lettres minuscules ou des chiffres.

Étant donné que le déploiement n’a pas été exécuté, il n’y a pas d’historique de déploiement.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-no-deploy.png" alt-text="Capture d’écran de la vue d’ensemble du groupe de ressources qui n’affiche aucun déploiement pour l’erreur de vérification préalable.":::

Le journal d’activité affiche l’erreur de vérification préalable. Sélectionnez le journal pour afficher les détails de l’erreur.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/preflight-activity-log.png" alt-text="Capture d’écran du journal d’activité du groupe de ressources avec une erreur de vérification préalable.":::

## <a name="fix-deployment-error"></a>Corriger une erreur de déploiement

Exécutez le déploiement avec une valeur de préfixe valide, comme `storage`.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.json \
  --parameters prefixName=storage
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.json `
  -prefixName storage
```

---

Le déploiement commence et est visible dans l’historique des déploiements. Le déploiement échoue car `outputs` référence un réseau virtuel qui n’existe pas dans le groupe de ressources. Toutefois, aucune erreur n’a été détectée pour le compte de stockage. La ressource est donc déployée. L’historique des déploiements montre un échec de déploiement.

:::image type="content" source="media/quickstart-troubleshoot-arm-deployment/deployment-failed.png" alt-text="Capture d’écran de la vue d’ensemble du groupe de ressources qui montre un échec de déploiement.":::

Pour résoudre l’erreur de déploiement, vous devez modifier la fonction de référence de façon à utiliser une ressource valide. Pour plus d’informations, consultez [Résoudre les erreurs liées à des ressources introuvables](error-not-found.md). Pour ce guide de démarrage rapide, supprimez la virgule qui précède `vnetResult` et tout `vnetResult`. Enregistrez le fichier et réexécutez le déploiement.

```json
"vnetResult": {
  "type": "object",
  "value": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'doesnotexist'), '2021-03-01', 'Full')]"
}
```

Une fois les erreurs de validation, de vérification préalable et de déploiement corrigées, le modèle suivant déploie un compte de stockage. L’historique des déploiements et le journal d’activité affichent un déploiement réussi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "prefixName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('prefixName'), uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, supprimez le groupe de ressources.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name troubleshootRG
```

---

Pour supprimer le groupe de ressources à partir du portail, procédez comme suit :

1. Dans le portail Azure, entrez **Groupes de ressources** dans la zone de recherche.
1. Dans le champ **Filtrer par nom**, entrez le nom du groupe de ressources.
1. Sélectionnez le nom du groupe de ressources.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Pour confirmer la suppression, entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à résoudre les erreurs de déploiement des modèles ARM.

> [!div class="nextstepaction"]
> [Résolution des erreurs courantes de déploiement Azure](common-deployment-errors.md)
