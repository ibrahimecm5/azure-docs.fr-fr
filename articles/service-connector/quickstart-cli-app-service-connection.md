---
title: 'Démarrage rapide : Créer une connexion de service dans App Service avec Azure CLI'
description: Guide de démarrage rapide montrant comment créer une connexion de service dans App Service avec Azure CLI
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 595296bff928ff75f52a05a1fc5e54fb94a2ad67
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475366"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>Démarrage rapide : Créer une connexion de service dans App Service avec Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Ce démarrage rapide vous présente les options permettant de créer une instance Azure Web PubSub avec Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.22.0 ou une version ultérieure de l’interface Azure CLI est requise pour ce démarrage rapide. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Ce guide de démarrage rapide part du principe que vous disposez déjà d’au moins une instance App Service s’exécutant dans Azure. Si vous n’avez pas encore d’instance App Service, [créez-en une](../app-service/quickstart-dotnetcore.md).

## <a name="view-supported-target-service-types"></a>Afficher les types de services cibles pris en charge

Utilisez la commande Azure CLI [az webapp connection]() pour créer et gérer les connexions de service à App Service. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types
```

## <a name="create-a-service-connection"></a>Créer une connexion de service

Utilisez la commande Azure CLI [az webapp connection]() pour créer une connexion de service à un stockage blob, en fournissant les informations suivantes :

- **Nom du groupe de ressources du service de calcul source** : nom du groupe de ressources de l’instance App Service.
- **Nom App Service** : nom de votre instance App Service qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` provisionner un nouveau et vous connecter directement à votre service d'applications.

## <a name="view-connections"></a>Afficher les connexions

Utilisez la commande Azure CLI [az webapp connection]() pour lister les connexions à votre instance App Service, en fournissant les informations suivantes :

- **Nom du groupe de ressources du service de calcul source** : nom du groupe de ressources de l’instance App Service.
- **Nom App Service** : nom de votre instance App Service qui se connecte au service cible.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Tutoriel : WebApp + Stockage avec Azure CLI](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutoriel : WebApp + PostgreSQL avec Azure CLI](./tutorial-django-webapp-postgres-cli.md)
