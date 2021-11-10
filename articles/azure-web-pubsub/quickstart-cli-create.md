---
title: 'Démarrage rapide : créer une instance Web PubSub avec Azure CLI'
description: Démarrage rapide montrant comment créer une instance Web PubSub à partir d’Azure CLI
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 426b77d1e1a79c3a9818e028edcc79bcad5ef2ec
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998073"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>Démarrage rapide : créer une instance Web PubSub avec Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Ce démarrage rapide vous présente les options permettant de créer une instance Azure Web PubSub avec Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.22.0 ou une version ultérieure de l’interface Azure CLI est requise pour ce démarrage rapide. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Créer une instance Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>Essayer l’instance nouvellement créée

> [!div class="nextstepaction"]
> [Essayer l’instance nouvellement créée à l’aide de l’interface CLI](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [Essayez l’instance nouvellement créée à partir du navigateur](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans les applications réelles, vous pouvez utiliser des kits de développement logiciel (SDK) dans différents langages pour générer votre propre application. Nous fournissons également des extensions de fonction qui vous permettent de créer facilement des applications serverless.

[!INCLUDE [next step](includes/include-next-step.md)]
