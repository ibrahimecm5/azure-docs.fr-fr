---
title: Déployer des fichiers Bicep à l’aide de GitHub Actions
description: Décrit comment déployer des fichiers Bicep à l’aide de GitHub Actions.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/02/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 20830623514d98bd7dc1e0606cf0cf79bb0e86b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087397"
---
# <a name="deploy-bicep-files-by-using-github-actions"></a>Déployer des fichiers Bicep à l’aide de GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) est une suite de fonctionnalités dans GitHub permettant d’automatiser vos flux de travail de développement logiciel.

Utilisez l’[action GitHub pour le déploiement Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) afin d’automatiser le déploiement d’un fichier Bicep sur Azure.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte GitHub. Si vous n’en avez pas, inscrivez-vous [gratuitement](https://github.com/join).

  - Un référentiel GitHub pour stocker vos fichiers Bicep et vos fichiers de workflow. Pour en créer un, consultez [Création d’un référentiel](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="workflow-file-overview"></a>Vue d’ensemble du fichier de workflow

Un workflow est défini par un fichier YAML (.yml) situé dans le chemin `/.github/workflows/` de votre dépôt. Cette définition contient les étapes et les paramètres qui composent le workflow.

Le fichier comporte deux sections :

|Section  |Tâches  |
|---------|---------|
|**Authentification** | 1. Définissez un principal de service. <br /> 2. Créez un secret GitHub. |
|**Déployer** | 1. Déployer le fichier Bicep. |

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

Vous pouvez créer un [principal de service](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dans [Azure CLI](/cli/azure/). Exécutez cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

Créez un groupe de ressources si vous n’en avez pas.

```azurecli-interactive
az group create -n {MyResourceGroup} -l {location}
```

Remplacez l’espace réservé `myApp` par le nom de votre application.

```azurecli-interactive
az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

Dans l’exemple ci-dessus, remplacez les espaces réservés par votre ID d’abonnement et le nom de votre groupe de ressources. La sortie correspond à un objet JSON avec les informations d’identification de l’attribution de rôle qui fournit l’accès à votre application App Service, similaire à ce qui suit. Copiez cet objet JSON pour une version ultérieure. Vous n’aurez besoin que des sections avec les valeurs `clientId`, `clientSecret`, `subscriptionId` et `tenantId`.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Il est toujours conseillé d’accorder un accès minimal. Dans l’exemple précédent, l’étendue se limite au groupe de ressources.

## <a name="configure-the-github-secrets"></a>Configurer les secrets GitHub

Vous devez créer des secrets pour vos informations d’identification, votre groupe de ressources et vos abonnements Azure.

1. Dans [GitHub](https://github.com/), accédez à votre référentiel.

1. Sélectionnez **Paramètres > Secrets > Nouveau secret**.

1. Collez l’intégralité de la sortie JSON de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret `AZURE_CREDENTIALS`.

1. Créez un autre secret nommé `AZURE_RG`. Ajoutez le nom de votre groupe de ressources au champ de valeur du secret (exemple : `myResourceGroup`).

1. Créez un autre secret nommé `AZURE_SUBSCRIPTION`. Ajoutez votre ID d’abonnement au champ de valeur du secret (exemple : `90fd3f9d-4c61-432d-99ba-1273f236afa2`).

## <a name="add-a-bicep-file"></a>Ajouter un fichier Bicep

Ajoutez un fichier Bicep à votre référentiel GitHub. Le fichier Bicep suivant crée un compte de stockage :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep" :::

Le fichier Bicep requiert un paramètre appelé **storagePrefix** avec 3 à 11 caractères.

Vous pouvez placer le fichier n’importe où dans le référentiel. L’exemple de workflow dans la section suivante suppose que le fichier de modèle est nommé **azuredeploy.bicep** et qu’il est stocké à la racine de votre dépôt.

## <a name="create-workflow"></a>Créer un workflow

Le fichier de workflow doit être stocké dans le dossier **.github/workflows** à la racine de votre référentiel. L’extension du fichier de workflow peut être **.yml** ou **.yaml**.

1. À partir de votre référentiel GitHub, sélectionnez **Actions** dans le menu supérieur.
1. Sélectionnez **Nouveau workflow**.
1. Sélectionnez **Configurer vous-même un workflow**.
1. Renommez le fichier de workflow si vous préférez utiliser un autre nom que **main.yml**. Par exemple : **deployBicepFile.yml**.
1. Remplacez le contenu du fichier yml par le code suivant :

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy Bicep file
        - name: deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.bicep
            parameters: storagePrefix=mystore
            failOnStdErr: false
    ```

    Remplacez `mystore` par votre propre préfixe de nom de compte de stockage.

    > [!NOTE]
    > À la place, vous pouvez spécifier un fichier de paramètres au format JSON dans l’action de déploiement ARM (exemple : `.azuredeploy.parameters.json`).

    La première section du fichier de workflow comprend les éléments suivants :

    - **nom** : Nom du workflow.
    - **on** : nom des événements GitHub qui déclenchent le workflow. Le workflow est déclenché quand il y a un événement push sur la branche primaire, qui modifie au moins l’un des deux fichiers spécifiés. Les deux fichiers sont le fichier de workflow et le fichier Bicep.

1. Sélectionnez **Démarrer la validation**.
1. Sélectionnez **Valider directement sur la branche primaire**.
1. Sélectionnez **Valider un nouveau fichier** (ou **Valider les modifications**).

La mise à jour du fichier de flux de travail ou du fichier bicep déclenche le flux de travail. Le flux de travail démarre juste après la validation des modifications.

## <a name="check-workflow-status"></a>Vérifier l’état du workflow

1. Sélectionnez l’onglet **Actions**. Un flux de travail **Create deployStorageAccount.yml** s’affiche. L’exécution du workflow prend 1 à 2 minutes.
1. Sélectionnez le workflow pour l’ouvrir.
1. Sélectionnez **Exécuter le déploiement ARM** dans le menu pour vérifier le déploiement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque votre groupe de ressource et référentiel ne sont plus nécessaires, nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources et votre référentiel GitHub.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Parcours d’apprentissage : Déployer des ressources Azure en utilisant Bicep et GitHub Actions](/learn/paths/bicep-github-actions)
