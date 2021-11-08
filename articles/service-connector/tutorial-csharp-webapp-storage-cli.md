---
title: 'Didacticiel : Déployer une application web connectée à Azure Storage Blob avec Service Connector'
description: Créez une application web connectée à Azure Storage Blob avec Service Connector.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6a1e887b9cbab6c8393dacf83a0e148ba257630d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452620"
---
# <a name="tutorial-deploy-web-application-connected-to-azure-storage-blob-with-service-connector"></a>Didacticiel : Déployer une application web connectée à Azure Storage Blob avec Service Connector

Découvrez comment accéder au stockage Azure pour le compte d’une application web (et non d’un utilisateur connecté) s’exécutant sur Azure App Service à l’aide d’identités managées. Ce tutoriel explique comment utiliser Azure CLI pour effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer votre environnement initial avec l’Azure CLI
> * Créer un compte de stockage et un conteneur de stockage d’objets blob Azure.
> * Déployer du code pour Azure App Service et vous connecter au stockage avec une identité gérée à l’aide de Service Connector

## <a name="1-set-up-your-initial-environment"></a>1. Configurer votre environnement initial

1. Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Installez l’interface <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 ou version ultérieure, avec laquelle vous exécutez des commandes dans n’importe quel interpréteur de commandes pour provisionner et configurer des ressources Azure.


Vérifiez également que votre version d’Azure CLI est la version 2.18.0 ou ultérieure :

```Azure CLI
az --version
```

Si vous devez effectuer une mise à niveau, essayez la commande `az upgrade` (nécessite la version 2.11+) ou consultez <a href="/cli/azure/install-azure-cli" target="_blank">Installer Azure CLI</a>.

Connectez-vous ensuite à Azure par le biais de l’interface CLI :

```Azure CLI
az login
```

Cette commande ouvre un navigateur pour recueillir vos informations d’identification. Une fois l’exécution de la commande terminée, elle affiche une sortie JSON contenant des informations sur vos abonnements.

Une fois connecté, vous pouvez exécuter des commandes Azure avec l’interface de ligne de commande Azure CLI pour utiliser des ressources de votre abonnement.

## <a name="2-clone-or-download-the-sample-app"></a>2. Cloner ou télécharger l’exemple d’application

Clonez l’exemple de dépôt :
```Bash
git clone https://github.com/Azure-Samples/serviceconnector-webapp-storageblob-dotnet.git
```

et accédez au dossier racine du référentiel :
```Bash
cd WebAppStorageMISample
```

## <a name="3-create-the-app-service-app"></a>3. Créer l’application App Service

Dans le terminal, vérifiez que vous êtes dans le dossier référentiel *WebAppStorageMISample* qui contient le code de l’application.

Créez une application App Service (processus hôte) avec la commande [`az webapp up`](/cli/azure/webapp#az_webapp_up) :

```Azure CLI
az webapp up --name <app-name> --sku B1 --location eastus --resource-group ServiceConnector-tutorial-rg
```

- Pour l’argument `--location`, vérifiez que vous utilisez l’emplacement [pris en charge par Service Connector](concept-region-support.md).
- **Remplacez** *\<app-name>* par un nom unique dans l’ensemble d’Azure (le point de terminaison de serveur est `https://<app-name>.azurewebsites.net`). Les caractères autorisés pour *\<app-name>* sont `A`-`Z`, `0`-`9` et `-`. Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.

## <a name="4-create-a-storage-account-and-blob-storage-container"></a>4. Créer un compte de stockage et un conteneur de Stockage Blob

Dans le terminal, exécutez la commande suivante pour créer un compte de stockage v2 universel et un conteneur de stockage Blob. **Remplacez** *\<storage-name>* par un nom unique. Le nom du conteneur doit être en minuscules, commencer par une lettre ou un chiffre, et peut comporter uniquement des lettres, des chiffres et des tirets (-).

```Azure CLI
az storage account create --name <storage-name> --resource-group ServiceConnector-tutorial-rg --sku Standard_RAGRS --https-only
```


## <a name="5-connect-app-service-app-to-blob-storage-container-with-managed-identity"></a>5. Connecter l’application App Service à un conteneur de Stockage Blob avec une identité managée

Dans le terminal, exécutez la commande suivante pour connecter votre application web au stockage Blob avec une identité managée.

```Azure CLI
az webapp connection create storage-blob -g ServiceConnector-tutorial-rg -n <app-name> --tg ServiceConnector-tutorial-rg --account <storage-name> --system-identity
```

- **Remplacez** *\<app-name>* par le nom de l’application web que vous avez utilisée à l’étape 3.
- **Remplacez** *\<storage-name>* par le nom de l’application de stockage que vous avez utilisée à l’étape 4.

> [!NOTE]
> Si vous voyez le message d’erreur « L’abonnement n’est pas inscrit pour utiliser Microsoft.ServiceLinker », exécutez `az provider register -n Microsoft.ServiceLinker` pour inscrire le fournisseur de ressources Service Connector et réexécutez la commande de connexion. 

## <a name="6-run-sample-code"></a>A6. Exécuter un exemple de code

Dans le terminal, exécutez la commande suivante pour ouvrir l’exemple d’application dans votre navigateur. Remplacez *\<app-name>* par le nom de l’application web que vous avez utilisée à l’étape 3.

```Azure CLI
az webapp browse --name <app-name> 
```

L’exemple de code est une application web. Chaque fois que vous actualisez la page d’index, elle crée ou met à jour un objet blob avec le texte `Hello Service Connector! Current is {UTC Time Now}` dans le conteneur de stockage et le lit pour l’afficher dans la page d’index.

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels ci-dessous pour en savoir plus sur Service Connector.

> [!div class="nextstepaction"]
> [En savoir plus sur les concepts Service Connector](./concept-service-connector-internals.md)
