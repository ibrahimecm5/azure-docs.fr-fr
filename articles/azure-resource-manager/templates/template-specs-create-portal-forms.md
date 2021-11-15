---
title: Créer des formulaires de portail pour le spec de modèle
description: Découvrez comment créer des formulaires qui s’affichent dans les formulaires Portail Azure. Utilisez le formulaire pour déployer un spec de modèle
ms.topic: tutorial
ms.date: 11/02/2021
ms.openlocfilehash: 3175264b8d3a5b9fd1698c8ccd1522ddb1f54b63
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478976"
---
# <a name="tutorial-create-azure-portal-forms-for-a-template-spec"></a>Tutoriel : Créer des formulaires Portail Azure pour un spec de modèle

Pour aider les utilisateurs à déployer un [spec de modèle](template-specs.md), vous pouvez créer un formulaire qui s’affiche dans le portail Azure. Le formulaire permet aux utilisateurs de fournir des valeurs qui sont transmises au spec de modèle en tant que paramètres.

Lorsque vous créez le spec de modèle, vous empaquetez le formulaire et le modèle Azure Resource Manager (modèle ARM) ensemble. Le déploiement du spec de modèle via le portail lance automatiquement le formulaire.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Capture d’écran du formulaire permettant de fournir des valeurs pour le spec de modèle":::

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Pour Azure PowerShell, utilisez la [version 6.0.0 ou ultérieure](/powershell/azure/install-az-ps). Pour Azure CLI, utilisez la [version 2.24.0 ou ultérieure](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Créer un modèle

Pour montrer les différents éléments du portail qui sont disponibles dans un formulaire, vous utiliserez un modèle ARM avec plusieurs paramètres. Le modèle suivant crée un coffre de clés, configure les autorisations sur le coffre de clés pour un utilisateur et ajoute un secret.

Copiez ce fichier et enregistrez-le localement. Ce tutoriel part du principe que vous l’avez nommé **keyvault.json**, mais vous pouvez lui donner n’importe quel nom.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Specifies the Azure location where the key vault should be created."
      }
    },
    "enabledForDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Virtual Machines are permitted to retrieve certificates stored as secrets from the key vault."
      }
    },
    "enabledForDiskEncryption": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Disk Encryption is permitted to retrieve secrets from the vault and unwrap keys."
      }
    },
    "enabledForTemplateDeployment": {
      "type": "bool",
      "defaultValue": false,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies whether Azure Resource Manager is permitted to retrieve secrets from the key vault."
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue": "[subscription().tenantId]",
      "metadata": {
        "description": "Specifies the Azure Active Directory tenant ID that should be used for authenticating requests to the key vault. Get it by using Get-AzSubscription cmdlet."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
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
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the secret that you want to create."
      }
    },
    "secretValue": {
      "type": "securestring",
      "metadata": {
        "description": "Specifies the value of the secret that you want to create."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "enabledForDeployment": "[parameters('enabledForDeployment')]",
        "enabledForDiskEncryption": "[parameters('enabledForDiskEncryption')]",
        "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "objectId": "[parameters('objectId')]",
            "tenantId": "[parameters('tenantId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
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
      "type": "Microsoft.KeyVault/vaults/secrets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "properties": {
        "value": "[parameters('secretValue')]"
      }
    }
  ]
}
```

## <a name="create-default-form"></a>Créer un formulaire par défaut

Le portail Azure fournit un bac à sable pour la création et la prévisualisation de formulaires. Ce bac à sable peut générer un formulaire à partir d’un modèle ARM existant. Vous utiliserez ce formulaire par défaut pour commencer à créer un formulaire pour votre spec de modèle.

1. Ouvrez le [bac à sable d’affichage de formulaire](https://aka.ms/form/sandbox).

1. Définissez **Type de package** sur **CustomTemplate**.

   :::image type="content" source="./media/template-specs-create-portal-forms/package-type.png" alt-text="Capture d’écran de la définition du type de package sur un modèle personnalisé":::

1. Sélectionnez l’icône pour ouvrir un modèle existant.

   :::image type="content" source="./media/template-specs-create-portal-forms/open-template.png" alt-text="Capture d’écran de l’icône permettant d’ouvrir un fichier":::

1. Accédez au modèle de coffre de clés que vous avez enregistré localement. Sélectionnez-le, puis sélectionnez **Ouvrir**.
1. Lorsque vous y êtes invité, si vous souhaitez remplacer les modifications actuelles, sélectionnez **Oui**.
1. Le formulaire généré automatiquement s’affiche dans la fenêtre de code. Pour voir qu’il fonctionne sans aucune modification, sélectionnez **Aperçu**.

   :::image type="content" source="./media/template-specs-create-portal-forms/preview-form.png" alt-text="Capture d’écran de la sélection du bouton Aperçu":::

1. Le bac à sable affiche le formulaire. Il comporte des champs pour sélectionner un abonnement, un groupe de ressources et une région. Il comporte également des champs pour tous les paramètres du modèle.

   La plupart des champs sont des zones de texte, mais certains champs sont spécifiques au type de paramètre. Lorsque votre modèle comprend des valeurs autorisées pour un paramètre, le formulaire généré automatiquement utilise une liste déroulante. La liste déroulante est préremplie avec les valeurs autorisées.

   > [!WARNING]
   > Ne sélectionnez pas **Créer**, car cela lancera un déploiement réel. Vous aurez la possibilité de déployer le spec du modèle plus loin dans ce tutoriel.

## <a name="customize-form"></a>Personnaliser le formulaire

Le formulaire par défaut est un bon point de départ pour comprendre les formulaires, mais il est généralement préférable de le personnaliser. Vous pouvez le modifier dans le bac à sable ou dans Visual Studio Code. L’option d’aperçu est uniquement disponible dans le bac à sable.

1. Définissons le schéma correct. Remplacez le texte du schéma par :

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-2" highlight="2" :::

1. Donnez au formulaire un **titre** qui décrit son utilisation.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" range="1-6" highlight="6" :::

1. Dans votre formulaire par défaut, tous les champs de votre modèle ont été regroupés en une seule étape appelée **Basics**. Pour aider les utilisateurs à comprendre les valeurs qu’ils fournissent, divisez le formulaire en étapes. Chaque étape contient des champs associés à une partie logique de la solution à déployer.

   Recherchez l’étape étiquetée **Basics**. Vous allez conserver cette étape et en ajouter d’autres en dessous. Les nouvelles étapes porteront sur la configuration du coffre de clés, la définition des autorisations utilisateur et la spécification du secret. Veillez à ajouter une virgule après l’étape Basics.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/steps.json" highlight="15-32" :::

   > [!IMPORTANT]
   > Les propriétés du formulaire respectent la casse. Veillez à utiliser la casse indiquée dans les exemples.

1. Sélectionnez **Aperçu**. Vous verrez les étapes, mais la plupart d’entre elles n’ont pas d’éléments.

   :::image type="content" source="./media/template-specs-create-portal-forms/view-steps.png" alt-text="Capture d’écran des étapes du formulaire":::

1. À présent, déplacez les éléments vers les étapes appropriées. Commencez par les éléments étiquetés **Secret Name** et **Secret Value**. Supprimez ces éléments de l’étape **Basics** et ajoutez-les à l’étape **Secret**.

   ```json
   {
     "name": "secret",
     "label": "Secret",
     "elements": [
       {
         "name": "secretName",
         "type": "Microsoft.Common.TextBox",
         "label": "Secret Name",
         "defaultValue": "",
         "toolTip": "Specifies the name of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "visible": true
       },
       {
         "name": "secretValue",
         "type": "Microsoft.Common.PasswordBox",
         "label": {
           "password": "Secret Value",
           "confirmPassword": "Confirm password"
         },
         "toolTip": "Specifies the value of the secret that you want to create.",
         "constraints": {
           "required": true,
           "regex": "",
           "validationMessage": ""
         },
         "options": {
           "hideConfirmation": true
         },
         "visible": true
       }
     ]
   }
   ```

1. Lorsque vous déplacez des éléments, vous devez corriger la section `outputs`. Actuellement, la section outputs fait référence à ces éléments comme s’ils étaient toujours dans l’étape basics. Corrigez la syntaxe de manière à ce qu’elle fasse référence aux éléments de l’étape `secret`.

   ```json
   "outputs": {
     "parameters": {
       ...
       "secretName": "[steps('secret').secretName]",
       "secretValue": "[steps('secret').secretValue]"
     }
   ```

1. Continuez à déplacer les éléments vers les étapes appropriées. Au lieu de parcourir chacune d’entre elles, jetez un coup d’œil au formulaire mis à jour.

   ::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform.json" :::

Enregistrez ce fichier localement sous le nom **keyvaultform.json**.

## <a name="create-template-spec"></a>Créer une spec de modèle

Lorsque vous créez le spec de modèle, fournissez les deux fichiers.

Pour PowerShell, utilisez [New-AzTemplateSpec](/powershell/module/az.resources/new-aztemplatespec) et fournissez le formulaire dans le paramètre `-UIFormDefinitionFile`.

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 1 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Pour Azure CLI, utilisez [az ts create](/cli/azure/ts#az_ts_create) et fournissez le formulaire dans le paramètre `--ui-form-definition`.

```azurecli
az ts create \
  --name keyvaultspec \
  --version 1 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

## <a name="deploy-through-portal"></a>Déployer via le portail

Pour tester le formulaire, rendez-vous sur le portail et accédez à votre spec de modèle. Sélectionnez **Déployer**.

:::image type="content" source="./media/template-specs-create-portal-forms/deploy-template-spec.png" alt-text="Capture d’écran de la vue d’ensemble du spec de modèle avec l’option Déployer":::

Vous verrez le formulaire que vous avez créé. Suivez les étapes et fournissez des valeurs pour les champs.

À l’étape **Basics**, vous verrez un champ pour **Region**. Ce champ est utilisé pour l’emplacement du groupe de ressources. À l’étape **Key Vault**, vous verrez un champ pour **Location**. Ce champ est utilisé pour l’emplacement du coffre de clés.

À l’étape **Permissions**, vous pouvez fournir votre propre identifiant utilisateur pour l’ID d’objet. Utilisez la valeur par défaut (`["list"]`) pour les autorisations de clé et de secret. Vous allez améliorer cette option dans la section suivante.

Lorsque vous avez fini de fournir les valeurs, sélectionnez **Créer** pour déployer le spec de modèle.

## <a name="improve-the-form"></a>Améliorer le formulaire

Dans la section précédente, vous avez ajouté des étapes et déplacé des éléments, mais vous n’avez modifié aucun des comportements par défaut. Dans cette section, vous allez apporter des modifications qui amélioreront l’expérience des utilisateurs de votre spec de modèle.

Auparavant, les deux champs d’autorisations étaient des zones de texte. À présent, vous utiliserez une liste déroulante. Définissez le type sur `Microsoft.Common.DropDown`.

Mettez à jour `keysPermissions` :

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-171" highlight="3" :::

Et `secretsPermissions` :

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-255" highlight="3" :::

Ces champs doivent transmettre un tableau au modèle. Une liste déroulante classique ne fonctionnera pas, car elle ne permet de sélectionner qu’une seule valeur. Pour sélectionner plus d’une valeur et les transmettre sous forme de tableau, ajoutez le champ `multiselect` et définissez-le sur `true`.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-173" highlight="5" :::

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="253-257" highlight="5" :::

Enfin, vous devez spécifier les valeurs autorisées pour la liste déroulante et une valeur par défaut.

::: code language="json" source="~/azure-docs-json-samples/azure-resource-manager/ui-forms/keyvaultform2.json" range="169-304" highlight="6-8,12-81,90-92,96-133" :::

Créez une nouvelle version du spec de modèle.

Avec PowerShell :

```azurepowershell
New-AzTemplateSpec `
  -name keyvaultspec `
  -version 2 `
  -ResourceGroupName templateSpecRG `
  -location westus2 `
  -templatefile keyvault.json `
  -UIFormDefinitionFile keyvaultform.json
```

Ou Azure CLI :

```azurecli
az ts create \
  --name keyvaultspec \
  --version 2 \
  --resource-group templatespecRG \
  --location westus2 \
  --template-file keyvault.json \
  --ui-form-definition keyvaultform.json
```

Redéployez votre spec de modèle avec le formulaire de portail amélioré.

:::image type="content" source="./media/template-specs-create-portal-forms/view-portal.png" alt-text="Capture d’écran du formulaire permettant de fournir des valeurs pour le spec de modèle":::

Notez que vos champs d’autorisation sont désormais des listes déroulantes qui autorisent plusieurs valeurs.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir le déploiement d’une spec de modèle en tant que modèle lié, consultez [Tutoriel : Déployer une spec de modèle en tant que modèle lié](template-specs-deploy-linked-template.md).
