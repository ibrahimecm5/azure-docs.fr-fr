---
title: Créer des fichiers Bicep - Visual Studio Code
description: Utiliser Visual Studio Code et l’extension Bicep avec des fichiers Bicep pour déployer des ressources Azure
ms.date: 11/09/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f5950a143510b85ea6de8f7af4e08c7d4b5ed293
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136230"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code

Ce démarrage rapide vous guide tout au long de la procédure de création d’un [fichier Bicep](overview.md) avec Visual Studio Code. Vous créerez ensuite un compte de stockage et un réseau virtuel. Vous découvrirez également comment l’extension Bicep simplifie le développement en fournissant la cohérence des types, la validation de la syntaxe et la saisie semi-automatique.

## <a name="prerequisites"></a>Prérequis

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Pour configurer votre environnement pour le développement Bicep, consultez [Installer les outils bicep](install.md). Au terme de cette procédure, vous disposerez de [Visual Studio Code](https://code.visualstudio.com/) et de l'[extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Vous disposez également de la dernière version d’[Azure CLI](/cli/azure/) ou du dernier [module Azure PowerShell](/powershell/azure/new-azureps-module-az).

## <a name="add-resource-snippet"></a>Ajouter un extrait de code de ressource

Lancez Visual Studio Code et créez un nouveau fichier nommé *main.bicep*.

VS Code avec l’extension Bicep simplifie le développement en fournissant des extraits de code prédéfinis. Dans ce démarrage rapide, vous allez ajouter un extrait de code qui crée un réseau virtuel.

Dans *main.bicep*, entrez **vnet**. Sélectionnez **res-vnet** dans la liste, puis appuyez sur Tab ou entrée.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-snippet.png" alt-text="Ajouter un extrait de code pour le réseau virtuel":::

> [!TIP]
> Si vous ne voyez pas ces options Intellisense dans VS Code, assurez-vous que vous avez installé l’extension Bicep comme indiqué dans [Prérequis](#prerequisites). Si vous avez installé l’extension, laissez au service de langage Bicep un peu de temps pour démarrer après l’ouverture de votre fichier Bicep. Le service démarre généralement rapidement, mais vous ne verrez pas d’options IntelliSense tant qu’il n’aura pas démarré. Une notification dans le coin inférieur droit indique que le service est en cours de démarrage. Lorsque cette notification disparaît, le service est en cours d’exécution.

Votre fichier Bicep contient maintenant le code suivant :

```bicep
resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'name'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}
```

Cet extrait de code contient toutes les valeurs dont vous avez besoin pour définir un réseau virtuel. Cela étant, vous pouvez modifier ce code pour répondre à vos besoins. Par exemple, `name` n’est pas un nom très adapté pour le réseau virtuel. Remplacez la propriété `name` par `examplevnet`.

```bicep
name: 'examplevnet'
```

Vous pouvez déployer ce fichier Bicep, mais nous allons ajouter un paramètre et un compte de stockage avant le déploiement.

## <a name="add-parameter"></a>Ajout du paramètre

Nous ajoutons maintenant un paramètre pour le nom du compte de stockage. En haut du fichier, ajoutez :

```bicep
param storageName
```

Lorsque vous ajoutez un espace après **storageName**, notez qu’IntelliSense propose les types de données disponibles pour le paramètre. Sélectionnez **Chaîne**.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-param.png" alt-text="Ajouter un type de chaîne au paramètre":::

Vous disposez du paramètre suivant :

```bicep
param storageName string
```

Ce paramètre fonctionne correctement, mais les comptes de stockage présentent des limites en termes de longueur de nom. Le nom doit contenir entre 3 et 24 caractères. Vous pouvez spécifier ces exigences en ajoutant des éléments décoratifs au paramètre.

Ajoutez une ligne au-dessus du paramètre et entrez **@** . Les éléments décoratifs disponibles s’affichent. Notez qu’il existe des éléments décoratifs pour **minLength** et **maxLength**.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/add-decorators.png" alt-text="Ajouter des éléments décoratifs au paramètre":::

Ajoutez les deux éléments décoratifs et spécifiez les limites de caractères, comme indiqué ci-dessous :

```bicep
@minLength(3)
@maxLength(24)
param storageName string
```

Vous pouvez également ajouter une description pour le paramètre. Incluez des informations susceptibles d’aider les utilisateurs qui déploient le fichier Bicep à comprendre la valeur à fournir.

```bicep
@minLength(3)
@maxLength(24)
@description('Provide a name for the storage account. Use only lower case letters and numbers. The name must be unique across Azure.')
param storageName string
```

Votre paramètre est prêt à être utilisé.

## <a name="add-resource"></a>Ajouter une ressource

Plutôt que d’utiliser un extrait de code afin de définir le compte de stockage, nous allons utiliser IntelliSense pour définir les valeurs. IntelliSense facilite cette étape en vous évitant de devoir entrer manuellement les valeurs.

Pour définir une ressource, utilisez le mot clé `resource`.  Sous votre réseau virtuel, entrez **ressource exampleStorage** :

```bicep
resource exampleStorage
```

**exampleStorage** est un nom symbolique pour la ressource que vous déployez. Vous pouvez utiliser ce nom pour référencer la ressource dans d’autres parties de votre fichier Bicep.

Lorsque vous ajoutez un espace après le nom symbolique, une liste de types de ressources s’affiche. Continuez d’entrer **storage** jusqu’à ce que vous puissiez le sélectionner dans les options disponibles.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-resource-type.png" alt-text="Sélectionner les comptes de stockage pour le type de ressource":::

Après avoir sélectionné **Microsoft.Stockage/storageAccounts**, les versions d’API disponibles s’affichent. Sélectionnez **2021-02-01**.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-api-version.png" alt-text="Sélectionner la version d’API pour le type de ressource":::

Après le guillemet simple pour le type de ressource, ajoutez `=` et un espace. Les options permettant d’ajouter des propriétés à la ressource s’affichent. Sélectionnez **required-properties**.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/select-required-properties.png" alt-text="Ajouter les propriétés requises":::

Cette option ajoute toutes les propriétés correspondant au type de ressource requis pour le déploiement. Après avoir sélectionné cette option, votre compte de stockage dispose des propriétés suivantes :

```bicep
resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name:
  location:
  sku: {
    name:
  }
  kind:

}
```

Vous avez presque terminé. Fournissez simplement des valeurs pour ces propriétés.

Encore une fois, IntelliSense peut vous y aider. Définissez `name` sur `storageName`. Il s’agit du paramètre qui contient un nom pour le compte de stockage. Pour `location` , affectez-lui la valeur `'eastus'`. Lors de l’ajout d’un nom et d’un type de référence SKU, IntelliSense présente les options valides.

Lorsque vous avez terminé, vous disposez des éléments suivants :

```bicep
@minLength(3)
@maxLength(24)
param storageName string

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2019-11-01' = {
  name: 'examplevnet'
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
    subnets: [
      {
        name: 'Subnet-1'
        properties: {
          addressPrefix: '10.0.0.0/24'
        }
      }
      {
        name: 'Subnet-2'
        properties: {
          addressPrefix: '10.0.1.0/24'
        }
      }
    ]
  }
}

resource exampleStorage 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageName
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}
```

Pour plus d’informations sur la syntaxe Bicep, consultez [Structure bicep](./file.md).

## <a name="visualize-resources"></a>Visualiser les ressources

Vous pouvez afficher une représentation des ressources dans votre fichier.

Dans l’angle supérieur gauche, sélectionnez le bouton du visualiseur pour ouvrir le visualiseur Bicep.

:::image type="content" source="./media/quickstart-create-bicep-use-visual-studio-code/bicep-visualizer.png" alt-text="Visualiseur Bicep":::

Le visualiseur affiche les ressources définies dans le fichier Bicep avec les informations de dépendance des ressources. Les deux ressources définies dans ce démarrage rapide ne présentent aucune relation de dépendance et donc, vous ne voyez pas aucun connecteur entre elles.

## <a name="deploy-the-bicep-file"></a>Déployer le fichier Bicep

Pour déployer le fichier que vous avez créé, ouvrez PowerShell ou Azure CLI. Si vous souhaitez utiliser le terminal Visual Studio Code intégré, sélectionnez la combinaison de touches `ctrl` + ```` ` ````. Remplacez le répertoire actif par le répertoire où se trouve le fichier Bicep.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az group create --name exampleRG --location eastus

az deployment group create --resource-group exampleRG --template-file main.bicep --parameters storageName=uniquename
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name exampleRG -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName exampleRG -TemplateFile ./main.bicep -storageName "uniquename"
```

---

> [!NOTE]
> Remplacez **uniquename** par un nom de compte de stockage unique. Si vous recevez un message d’erreur indiquant que le compte de stockage est déjà utilisé, cela signifie que nom de stockage que vous avez fourni est déjà utilisé. Fournissez un nom plus susceptible d’être unique.

Une fois le déploiement terminé, un message doit s’afficher pour indiquer que le déploiement a réussi.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, utilisez Azure CLI ou le module Azure PowerShell afin de supprimer le groupe de ressources qui a été utilisé pour ce guide de démarrage rapide.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Bicep dans Microsoft Learn](learn-bicep.md)
