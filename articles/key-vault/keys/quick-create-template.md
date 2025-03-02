---
title: Démarrage rapide Azure – Créer un coffre de clés Azure et une clé à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Guide de démarrage rapide montrant comment créer des coffres de clés Azure Key Vault et leur ajouter une clé à l’aide d’un modèle Azure Resource Manager (modèle ARM).
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs, devx-track-azurepowershell
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 8e8a0e931a2c33883bf18ad92a367110d9be093e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572960"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Démarrage rapide : Créer un coffre de clés Azure et une clé à l’aide d’un modèle ARM 

[Azure Key Vault](../general/overview.md) est un service cloud qui propose un magasin sécurisé pour les secrets, comme les clés, les mots de passe, les certificats et autres secrets. Ce guide de démarrage rapide porte essentiellement sur le déploiement d’un modèle Azure Resource Manager (modèle ARM) en vue de créer un coffre de clés et une clé.


## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article :

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Un rôle intégré Azure doit être attribué à l’utilisateur. Rôle recommandé : **Contributeur**. [En savoir plus ici](../../role-based-access-control/role-assignments-portal.md)
- Le modèle requiert votre ID objet utilisateur Azure AD pour la configuration des autorisations. La procédure suivante permet d’obtenir l’ID de l’objet (GUID).

    1. Exécutez la commande Azure PowerShell ou Azure CLI ci-dessous en sélectionnant **Essayer**, puis collez le script dans le volet de l’interpréteur de commandes. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller**.

        # <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. Notez l’ID de l’objet. Vous en aurez besoin dans la prochaine section de ce guide de démarrage rapide.

## <a name="review-the-template"></a>Vérifier le modèle

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

Deux ressources sont définies dans le modèle :

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

Vous trouverez d’autres exemples de modèles Azure Key Vault dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="parameters-and-definitions"></a>Paramètres et définitions

|Paramètre  |Définition  |
|---------|---------|
|**keyOps**  | Spécifie les opérations qui peuvent être effectuées à l’aide de la clé. Si vous ne spécifiez pas ce paramètre, toutes les opérations peuvent être effectuées. Les valeurs acceptables pour ce paramètre figurent dans une liste séparée par des virgules des opérations clés définies par la spécification de la [clé web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41) : <br> `["sign", "verify", "encrypt", "decrypt", " wrapKey", "unwrapKey"]` |
|**CurveName**  |  Nom de la courbe elliptique pour le type de clé EC. Consultez [JsonWebKeyCurveName](/rest/api/keyvault/createkey/createkey#jsonwebkeycurvename) |
|**Kty**  |  Type de clé à créer. Pour connaître les valeurs valides, consultez [JsonWebKeyType](/rest/api/keyvault/createkey/createkey#jsonwebkeytype) |
|**Balises** | Métadonnées spécifiques d’application sous la forme de paires clé/valeur.  |
|**nbf**  |  Spécifie l’heure, sous la forme d’un objet DateTime, avant laquelle la clé ne peut pas être utilisée. Le format est l’horodatage UNIX (le nombre de secondes après Unix Epoch le 1er janvier 1970, UTC).  |
|**exp**  |  Spécifie l’heure d’expiration, sous la forme d’un objet DateTime. Le format est l’horodatage UNIX (le nombre de secondes après Unix Epoch le 1er janvier 1970, UTC). |

## <a name="deploy-the-template"></a>Déployer le modèle
Vous pouvez utiliser le [portail Azure](../../azure-resource-manager/templates/deploy-portal.md), Azure PowerShell, Azure CLI, ou l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier le coffre de clés et la clé, ou utiliser le script Azure CLI ou Azure PowerShell ci-dessous pour lister la clé créée.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres démarrages rapides et didacticiels sur les coffres de clés reposent sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le coffre de clés et les ressources associées. Pour supprimer le groupe de ressources avec Azure CLI ou Azure PowerShell :

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et une clé en utilisant un modèle ARM, puis vous avez validé le déploiement. Pour en savoir plus sur Key Vault et Azure Resource Manager, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- En savoir plus sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
