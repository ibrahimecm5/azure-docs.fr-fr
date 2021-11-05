---
title: Résoudre les problèmes des déploiements de modèles ARM
description: Découvrez comment superviser les déploiements de modèle Azure Resource Manager (modèle ARM), et résoudre les problèmes de déploiement éventuels. Affiche les journaux d’activité et l’historique des déploiements.
ms.date: 11/01/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b2dae6d4f90410b8ce1f8fc455aa95ede0ab0d3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096779"
---
# <a name="quickstart-troubleshoot-arm-template-deployments"></a>Démarrage rapide : Résolution des problèmes de déploiement de modèles ARM

Ce guide de démarrage rapide explique comment résoudre les erreurs de déploiement des modèles Azure Resource Manager (ARM). Vous allez configurer un modèle comportant deux erreurs et apprendre à utiliser les journaux d’activité et l’historique de déploiement pour les corriger.

Il existe deux types d’erreurs liées au déploiement d’un modèle :

- Les **erreurs de validation** se produisent avant le début du déploiement. Elles sont provoquées par des erreurs de syntaxe dans le fichier.
- Les **erreurs de déploiement** se produisent pendant le processus de déploiement. Elles peuvent être provoquées par une valeur incorrecte, par exemple une version d’API.

Les deux types d’erreurs retournent un code d’erreur qui vous permet de résoudre les problèmes liés au déploiement. Les deux types d’erreurs apparaissent dans le journal d’activité. Les erreurs de validation n’apparaissent pas dans l’historique de déploiement, car le déploiement n’a jamais démarré.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, il vous faut les éléments suivants :

- Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Visual Studio Code](https://code.visualstudio.com/) avec la dernière version de l’extension [Outils Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
- Passez en revue la procédure de déploiement d’un modèle local avec [Azure Cloud Shell](../templates/deploy-cloud-shell.md#deploy-local-template).

## <a name="create-a-template-with-errors"></a>Création d’un modèle comportant des erreurs

Le modèle suivant contient une erreur de validation et une erreur de déploiement. Vous allez détecter et corriger les erreurs du modèle pour pouvoir déployer le compte de stockage.

Les deux erreurs de l’exemple de modèle se trouvent sur la ligne suivante :

```json
"apiVersion1": "2018-07-02",
```

1. Ouvrez Visual Studio Code et sélectionnez **Fichier** > **Nouveau fichier**.
1. Sélectionnez **Copier** pour copier le modèle, puis collez-le dans le nouveau fichier.
1. Sélectionnez **Fichier** > **Enregistrer sous** pour nommer le fichier _azuredeploy.json_ et l’enregistrer sur votre ordinateur local.

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
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion1": "2018-07-02",
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

## <a name="deploy-the-template"></a>Déployer le modèle

Chargez le modèle sur Cloud Shell et déployez-le avec Azure CLI ou Azure PowerShell.

1. Connectez-vous à [Cloud Shell](https://shell.azure.com).
1. Sélectionnez **Bash** ou **PowerShell** en haut à gauche.

    :::image type="content" source="media/quickstart-troubleshoot-arm-deployment/cloud-shell-upload.png" alt-text="Capture d’écran de la sélection d’un interpréteur de commandes et du chargement d’un fichier dans Azure Cloud Shell.":::

1. Sélectionnez **Charger/télécharger des fichiers** et chargez votre fichier _azuredeploy.json_.
1. Pour déployer le modèle, copiez et collez les commandes suivantes dans la fenêtre de l’interpréteur de commandes.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    New-AzResourceGroup -Name "$resourceGroupName" -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    echo "Enter a resource group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file $HOME/azuredeploy.json &&
    echo "Press [ENTER] to continue ..."
    ```

    ---

    > [!NOTE]
    > Utilisez le même nom et le même emplacement de groupe de ressources à mesure que vous exécutez les déploiements. Lorsqu’il vous est demandé de mettre à jour le groupe de ressources, sélectionnez **Oui**.

## <a name="troubleshoot-the-validation-error"></a>Résoudre l’erreur de validation

L’erreur de validation affiche un message d’erreur accompagné du code d’erreur `InvalidRequestContent` dans l’interpréteur de commandes.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:49:10 PM - Error: Code=InvalidRequestContent; Message=The request content
was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type
'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 34, position 24.'.

New-AzResourceGroupDeployment: The deployment validation failed
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"error":{"code":"InvalidRequestContent","message":"The request content was invalid and could not be
deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'.
Path 'properties.template.resources[0].apiVersion1', line 32, position 20.'."}}
```

---

### <a name="fix-the-validation-error"></a>Corriger l’erreur de validation

L’erreur est due au fait que la valeur `apiVersion1` est incorrecte. Utilisez Visual Studio Code pour remplacer `apiVersion1` par `apiVersion`, puis enregistrez le modèle. Suivez la procédure [Déploiement du modèle](#deploy-the-template) pour charger le modèle et réexécuter le déploiement.

## <a name="troubleshoot-the-deployment-error"></a>Résoudre l’erreur de déploiement

L’erreur de déploiement affiche un message d’erreur accompagné du code d’erreur `NoRegisteredProviderFound` dans l’interpréteur de commandes. Vous pouvez également voir les erreurs dans les sections **Déploiements** et **Journal d’activité** du groupe de ressources.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```Output
New-AzResourceGroupDeployment: 6:51:48 PM - The deployment 'azuredeploy' failed with error(s).
Showing 1 out of 1 error(s).
Status Message: No registered resource provider found for location 'centralus' and API version '2018-07-02'
for type 'storageAccounts'. The supported api-versions are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01,
2021-01-01, 2020-08-01-preview, 2019-06-01, 2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview,
2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.
(Code:NoRegisteredProviderFound)
CorrelationId: 11111111-1111-1111-1111-111111111111
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```Output
{"status":"Failed","error":{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage
details.", "details":[{"code":"BadRequest","message":"{\r\n  \"error\": {\r\n
\"code\": \"NoRegisteredProviderFound\", \r\n    \"message\": \"No registered resource provider found for
location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions
are '2021-06-01, 2021-05-01, 2021-04-01, 2021-02-01, 2021-01-01, 2020-08-01-preview, 2019-06-01,
2019-04-01, 2018-11-01, 2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01,
2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'.
The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast,
japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast,
australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus,
uksouth, ukwest, koreacentral, koreasouth, francecentral, australiacentral, southafricanorth, uaenorth,
switzerlandnorth, germanywestcentral, norwayeast, westus3, jioindiawest'.\"\r\n  }\r\n}"}]}}
```

---

### <a name="deployments"></a>Déploiements

Pour afficher l’erreur de déploiement sur le Portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Entrez _Groupes de ressources_ dans la zone de recherche, puis sélectionnez **Groupes de ressources**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/search-box.png" alt-text="Capture d’écran de la zone de recherche du Portail Azure.":::

1. Sélectionnez le nom du groupe de ressources du déploiement.
1. Accédez à **Vue d’ensemble** et sélectionnez le lien **1 échec**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error.png" alt-text="Capture d’écran mettant en évidence le lien de l’échec de déploiement.":::

1. Dans **Déploiements**, sélectionner **Détails de l’erreur**.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-details.png" alt-text="Capture d’écran du lien des échecs de déploiement qui pointe vers les détails de l’erreur.":::

    Le message d’erreur est le même que la sortie de la commande de déploiement :

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-error-summary.png" alt-text="Capture d’écran des détails de l’erreur de déploiement.":::

### <a name="activity-log"></a>Journal d’activité

L’erreur se trouve également dans les journaux d’activité du groupe de ressources. Il faut quelques minutes pour que le journal d’activité affiche les dernières informations de déploiement en date.

1. Dans le groupe de ressources, sélectionnez **Journal d’activité**.
1. Utilisez les filtres pour rechercher le journal que vous souhaitez afficher, puis sélectionnez-le.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log.png" alt-text="Capture d’écran du journal d’activité du groupe de ressources mettant en évidence un échec de déploiement.":::

1. Une fois le journal sélectionné, les détails s’affichent.

    :::image type="content" source="./media/quickstart-troubleshoot-arm-deployment/arm-template-deployment-activity-log-details.png" alt-text="Capture d’écran des détails du journal d’activité indiquant le message d’erreur d’un échec de déploiement.":::

### <a name="fix-the-deployment-error"></a>Correction de l’erreur de déploiement

L’erreur de déploiement **Aucun fournisseur de ressources inscrit n’a été trouvé** est provoquée par une version d’API incorrecte. Utilisez Visual Studio Code pour remplacer la version de l’API par une valeur valide (par exemple `2021-04-01`), puis enregistrez le modèle. Suivez la procédure [Déploiement du modèle](#deploy-the-template) pour charger le modèle et réexécuter le déploiement.

Une fois les erreurs de validation et de déploiement corrigées, le compte de stockage est créé. Accédez à la page **Vue d’ensemble** du groupe de ressources pour afficher la ressource. Les sections **Déploiements** et **Journal d’activité** indiquent un déploiement réussi.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, supprimez le groupe de ressources. Vous pouvez le faire dans Cloud Shell ou sur le Portail Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Remplacez `<rgname>`, crochets pointus inclus, par le nom de votre groupe de ressources.

```azurepowershell
Remove-AzResourceGroup -Name <rgname>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Remplacez `<rgname>`, crochets pointus inclus, par le nom de votre groupe de ressources.

```azurecli
az group delete --name <rgname>
```

---

Pour supprimer le groupe de ressources sur le portail, procédez comme suit :

1. Dans le portail Azure, entrez **Groupes de ressources** dans la zone de recherche.
1. Dans le champ **Filtrer par nom**, entrez le nom du groupe de ressources.
1. Sélectionnez le nom du groupe de ressources.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Pour confirmer la suppression, entrez le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à résoudre les erreurs de déploiement des modèles ARM.

> [!div class="nextstepaction"]
> [Résolution des erreurs courantes de déploiement Azure](common-deployment-errors.md)
