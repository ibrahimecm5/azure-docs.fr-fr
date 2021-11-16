---
title: Résoudre les problèmes de déploiement de fichiers Bicep
description: Découvrez comment superviser des déploiements de fichiers Bicep et résoudre les problèmes associés. Affiche les journaux d’activité et l’historique des déploiements.
ms.date: 11/04/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31d212ef608b5fbabb4430b5320ae033ae2be325
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849441"
---
# <a name="quickstart-troubleshoot-bicep-file-deployments"></a>Démarrage rapide : Résoudre les problèmes de déploiement de fichiers Bicep

Ce guide de démarrage rapide explique comment résoudre les erreurs de déploiement de fichier Bicep. Vous allez créer un fichier comportant des erreurs et apprendre à les corriger.

Il existe trois types d’erreurs liées à un déploiement :

- Les **erreurs de validation** se produisent avant le début d’un déploiement et sont provoquées par des erreurs de syntaxe dans votre fichier. Votre éditeur peut identifier ces erreurs.
- Des **erreurs de validation préalable** se produisent lorsqu’une commande de déploiement est exécutée, mais que les ressources ne sont pas déployées. Ces erreurs sont détectées sans commencer le déploiement. Par exemple, si une valeur de paramètre est incorrecte, l’erreur est détectée lors de la validation préalable.
- Les **erreurs de déploiement** se produisent pendant le processus de déploiement et ne peuvent être détectées qu’en évaluant la progression du déploiement.

Tous les types d’erreurs retournent un code d’erreur que vous utilisez pour résoudre les problèmes de déploiement. Les erreurs de validation et les erreurs de vérification préalable sont affichées dans le journal d’activité, mais n’apparaissent pas dans votre historique de déploiement.nt pas dans votre historique de déploiement. Un fichier Bicep avec des erreurs de syntaxe ne se compile pas en JSON et n’est pas affiché dans le journal d’activité.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, vous devez disposer des éléments suivants :

- Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Visual Studio Code](https://code.visualstudio.com) avec la dernière [extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).
- La version la plus récente d’[Azure PowerShell](/powershell/azure/install-az-ps) ou d’[Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-bicep-file-with-errors"></a>Créer un fichier Bicep comportant des erreurs

Copiez le fichier Bicep suivant, puis enregistrez-le localement. Vous allez utiliser ce fichier pour résoudre une erreur de validation, une erreur validation préalable et une erreur de déploiement. Ce guide de démarrage rapide suppose que vous avez nommé le fichier **troubleshoot.bicep**, mais vous pouvez lui attribuer n’importe quel nom.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

resource existingVNet 'Microsoft.Network/virtualNetworks@2021-03-01' existing = {
  name: 'doesnotexist'
}

output storageAccountName string = storageAccountName
output vnetResult object = existingVNet
```

## <a name="fix-validation-error"></a>Corriger une erreur de validation

Ouvrez le fichier dans Visual Studio Code. Vous remarquerez que Visual Studio Code identifie une erreur de syntaxe. La déclaration du premier paramètre est marquée avec des soulignements ondulés rouges pour indiquer une erreur.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/show-visual-studio-code-error.png" alt-text="Capture d’écran de Visual Studio Code montrant une erreur dans la syntaxe.":::

Les lignes signalées comme contenant une erreur sont les suivantes :

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'
```

Lorsque vous passez la souris sur `parameter`, un message d’erreur s’affiche.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/declaration-not-recognized.png" alt-text="Capture d’écran du message d’erreur dans Visual Studio Code.":::

Le message indique : « This declaration type is not recognized. Specify a parameter, variable, resource, or output declaration. » (Ce type de déclaration n’est pas reconnu. Spécifiez un paramètre, une variable, une ressource ou une déclaration de sortie.) Si vous tentez de déployer ce fichier, la commande de déploiement affiche ce même message d’erreur.

Si vous recherchez une [déclaration de paramètre](../bicep/parameters.md) dans la documentation, vous voyez que le mot clé est en fait `param`. Lorsque vous changez cette syntaxe, l’erreur de validation disparaît. L’élément décoratif `@allowed` était également marqué comme constituant une erreur, mais cette erreur est également résolue en changeant la déclaration du paramètre. L’élément décoratif a été marqué comme constituant une erreur, car il attend une déclaration de paramètre après lui. Cette condition n’était pas remplie lorsque la déclaration était incorrecte.

La ligne corrigée se présente ainsi :

```bicep
param storageAccountType string = 'Standard_LRS'
```

## <a name="fix-preflight-error"></a>Corriger une erreur de validation préalable

Maintenant que vous avez corrigé l’erreur de validation, il est temps de déployer le fichier. Toutefois, vous allez fournir une valeur de paramètre incorrecte pour voir une erreur de validation préalable.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=longNamewith!!Charactersthatarenotallowed
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName longNamewith!!Charactersthatarenotallowed
```

---

Azure Resource Manager détermine que le nom du compte de stockage contient des caractères qui ne sont pas autorisés. Il ne tente pas le déploiement.

Un message d’erreur indiquant que la validation préalable a échoué s’affiche. Vous obtenez également un message indiquant que le nom du compte de stockage doit comprendre entre 3 et 24 caractères, et être composé uniquement de lettres minuscules et de nombres. Le préfixe que vous avez fourni ne répondait pas à cet impératif. Pour plus d’informations sur ce code d’erreur, consultez [Résoudre les erreurs liées aux noms de compte de stockage](error-storage-account-name.md).

Étant donné que l’erreur a été interceptée lors de la validation préalable, aucun déploiement ne figure dans l’historique.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/no-deployment.png" alt-text="Capture d’écran du portail sans aucun déploiement dans l’historique.":::

Toutefois, le déploiement ayant échoué figure dans le journal d’activité.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/preflight-activity-log.png" alt-text="Capture d’écran du journal d’activité contenant l’erreur.":::

Vous pouvez ouvrir les détails de l’entrée de journal pour voir le message d’erreur.

## <a name="fix-deployment-error"></a>Corriger une erreur de déploiement

Vous allez redéployer le fichier et fournir une valeur autorisée pour le paramètre de préfixe de nom.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create --name troubleshootRG --location westus
az deployment group create \
  --resource-group troubleshootRG \
  --template-file troubleshoot.bicep \
  --parameters prefixName=stg
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup -Name troubleshootRG -Location westus
New-AzResourceGroupDeployment `
  -ResourceGroupName troubleshootRG `
  -TemplateFile troubleshoot.bicep `
  -prefixName stg
```

---

Le déploiement commence mais échoue avec un message indiquant que le réseau virtuel est introuvable. En général, vous corrigez cette erreur en changeant la référence à la ressource. Dans ce guide de démarrage rapide, vous allez supprimer la référence. Pour plus d’informations sur ce code d’erreur, consultez [Résoudre les erreurs liées à des ressources introuvables](error-not-found.md).

Dans le portail, notez que le déploiement figure dans l’historique.

:::image type="content" source="media/quickstart-troubleshoot-bicep-deployment/view-deployment-history.png" alt-text="Capture d’écran de l’historique des déploiements dans le portail.":::

Vous pouvez ouvrir l’entrée dans l’historique des déploiements pour obtenir des détails sur l’erreur. L’erreur figure également dans le journal d’activité.

Le fichier Bicep tente de référencer un réseau virtuel qui n’existe pas dans votre groupe de ressources. Supprimez la référence au réseau virtuel existant pour corriger l’erreur.

```bicep
@description('SKU for the storage account')
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
parameter storageAccountType string = 'Standard_LRS'

@description('Prefix for storage name.')
param prefixName string

var storageAccountName = '${prefixName}${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-06-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: storageAccountType
  }
  kind: 'StorageV2'
  properties: {}
}

output storageAccountName string = storageAccountName
```

Vous pouvez déployer ce fichier Bicep qui ne comporte aucune erreur.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, supprimez le groupe de ressources. Vous pouvez le faire à partir de Cloud Shell ou du portail Azure.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name troubleshootRG
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Remplacez `<rgname>` (crochets inclus) par le nom de votre groupe de ressources.

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

Dans ce guide de démarrage rapide, vous avez appris à résoudre les erreurs de déploiement de fichier Bicep.

> [!div class="nextstepaction"]
> [Résoudre les erreurs courantes de déploiement Azure](common-deployment-errors.md)
