---
title: Secret Azure Key Vault avec modèle
description: Montre comment passer une clé secrète à partir d’un coffre de clés en tant que paramètre lors du déploiement.
ms.topic: conceptual
ms.date: 06/18/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a5ee223c9cfa2dada3da4f6eb901550c9d4eec9d
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380713"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement

Au lieu de placer une valeur sécurisée (telle qu’un mot de passe) directement dans votre modèle ou fichier de paramètres, vous pouvez récupérer la valeur à partir d’un coffre [Azure Key Vault](../../key-vault/general/overview.md) pendant un déploiement. Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés.

> [!IMPORTANT]
> Cet article est consacré à la transmission d’une valeur sensible comme paramètre de modèle. Quand le secret est transmis comme paramètre, le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement. 
>
> Cet article ne traite pas de la définition d’une propriété de machine virtuelle sur l’URL d’un certificat dans un coffre de clés. Vous trouverez un modèle de démarrage rapide de ce scénario dans [Installer un certificat à partir d’Azure Key Vault sur une machine virtuelle](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Déployer des coffres de clés et des secrets

Pour accéder à un coffre de clés lors du déploiement de modèle, définissez `enabledForTemplateDeployment` sur le coffre de clés sur `true`.

Si vous disposez déjà d’un coffre Key Vault, vérifiez qu’il autorise les déploiements de modèles.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Pour créer un coffre Key Vault et ajouter un secret, utilisez :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

En tant que propriétaire du coffre de clés, vous avez automatiquement accès à la création de secrets. S’il vous faut permettre à un autre utilisateur de créer des secrets, utilisez :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Les stratégies d’accès ne sont pas nécessaires si l’utilisateur déploie un modèle qui récupère un secret. Ajoutez un utilisateur aux stratégies d’accès si et seulement si l’utilisateur doit directement utiliser les secrets. Les autorisations de déploiement sont définies dans la section suivante.

Pour plus d’informations sur la création de coffres de clés et l’ajout des secrets, consultez :

- [Définir et récupérer un secret à l'aide de l'interface de ligne de commande (CLI)](../../key-vault/secrets/quick-create-cli.md)
- [Définir et récupérer un secret à l’aide de PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Définir et récupérer un secret à l'aide du portail](../../key-vault/secrets/quick-create-portal.md)
- [Définir et récupérer un secret à l'aide de .NET](../../key-vault/secrets/quick-create-net.md)
- [Définir et récupérer un secret à l'aide de Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-deployment-access-to-the-secrets"></a>Accorder au déploiement un accès aux secrets

L’utilisateur qui déploie le modèle doit disposer de l’autorisation `Microsoft.KeyVault/vaults/deploy/action` pour l’étendue du groupe de ressources et du coffre de clés. En vérifiant cet accès, Azure Resource Manager empêche tout utilisateur non approuvé d’accéder au secret en transmettant l’ID de ressource pour le coffre de clés. Vous pouvez accorder au déploiement un accès aux utilisateurs sans accorder d’accès en écriture aux secrets.

Les rôles [propriétaire](../../role-based-access-control/built-in-roles.md#owner) et [contributeur](../../role-based-access-control/built-in-roles.md#contributor) accordent cet accès. Si vous avez créé le coffre de clés, vous êtes le propriétaire et vous avez donc l’autorisation.

Pour les autres utilisateurs, accordez l'autorisation `Microsoft.KeyVault/vaults/deploy/action`. La procédure suivante montre comment créer un rôle avec les permissions minimales et l’attribuer à un utilisateur.

1. Créez un fichier JSON de définition de rôle personnalisé :

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    Remplacez « 00000000-0000-0000-0000-000000000000 » par l’ID d’abonnement.

2. Créez le nouveau rôle à l’aide du fichier JSON :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    L’exemple attribue le rôle personnalisé à l’utilisateur au niveau du groupe de ressources.

Quand vous utilisez un coffre de clés avec le modèle pour une [Application managée](../managed-applications/overview.md), vous devez accorder l’accès au principal de service du **fournisseur de ressources d’appliance**. Pour plus d’informations, consultez [Accéder au secret de coffre de clés pendant le déploiement d’applications managées Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Référencement de secrets avec un ID statique

Avec cette approche, vous référencez le coffre de clés dans le fichier de paramètres, et non dans le modèle. L’illustration suivante montre comment le fichier de paramètres fait référence à la question secrète et passe cette valeur au modèle.

![Diagramme d'ID statique d'intégration d'un coffre de clés à Resource Manager](./media/key-vault-parameter/statickeyvault.png)

[Tutoriel : Intégrer Azure Key Vault à un déploiement de modèle Resource Manager](./template-tutorial-use-key-vault.md) utilise cette méthode.

Le modèle suivant déploie un serveur SQL qui comprend un mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée. Toutefois, le modèle ne spécifie pas d’où vient cette valeur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

À présent, créez un fichier de paramètres pour le modèle précédent. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le modèle. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez le secret en transmettant l'identificateur de ressource du coffre de clés et le nom du secret :

Dans le fichier de paramètres suivant, le secret du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

Si vous devez utiliser une version du secret autre que la version actuelle, utilisez la propriété `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Déployez le modèle et transmettez le fichier de paramètres :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Référencement de secrets avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés à partir du paramètre. Dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Ou bien, vous pouvez souhaiter transmettre des valeurs de paramètre au modèle au lieu de créer un paramètre de référence dans le fichier de paramètres. La solution est de générer dynamiquement l’ID de ressource pour un secret de coffre de clés à l’aide d’un modèle lié.

Vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier.

Dans votre modèle parent, ajoutez le modèle imbriqué et passez un paramètre contenant l’ID de la ressource généré dynamiquement. L’illustration suivante montre comment un paramètre dans le modèle lié fait référence au secret.

![ID dynamique](./media/key-vault-parameter/dynamickeyvault.png)

Le modèle suivant crée de façon dynamique l’ID du coffre de clés et le passe en paramètre.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Présentation d’Azure Key Vault](../../key-vault/general/overview.md)
- Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples) sur GitHub.
- Pour lire un module Microsoft Learn dans lequel est utilisée une valeur sécurisée issue d’un coffre de clés, consultez [Gérer des déploiements cloud complexes à l’aide des fonctionnalités avancées de modèle ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
