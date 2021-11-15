---
title: Guide pratique pour utiliser le stockage persistant intégré dans Azure Spring Cloud | Microsoft Docs
description: Comment utiliser le stockage persistant intégré dans Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b56932b9638ede2b3db36050310a06921d115103
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479113"
---
# <a name="use-built-in-persistent-storage-in-azure-spring-cloud"></a>Utiliser le stockage persistant intégré dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ✔️ C#

Azure Spring Cloud fournit deux types de stockage intégré pour votre application : persistant et temporaire.

Par défaut, Azure Spring Cloud fournit un stockage temporaire pour chaque instance d’application. Le stockage temporaire est limité à 5 Go par instance avec le chemin de montage par défaut /tmp.

> [!WARNING]
> Si vous redémarrez une instance d’application, le stockage temporaire associé est définitivement supprimé.

Le stockage persistant est un conteneur de partage de fichiers géré par Azure et alloué par application. Les données stockées dans le stockage persistant sont partagées par toutes les instances d’une application. Une instance Azure Spring Cloud peut avoir un maximum de 10 applications avec un stockage persistant activé. 50 Go de stockage persistant sont alloués à chaque application. Le chemin de montage par défaut pour le stockage persistant est /persistent.

> [!WARNING]
> Si vous désactivez le stockage persistant d’une application, l’ensemble de ce stockage est libéré et toutes les données stockées sont perdues.

## <a name="enable-or-disable-built-in-persistent-storage"></a>Activer ou désactiver le stockage persistant intégré

Vous pouvez modifier l’état du stockage persistant intégré à l’aide du Portail Azure ou à l’aide d’Azure CLI.

#### <a name="portal"></a>[Portail](#tab/azure-portal)
## <a name="enable-or-disable-built-in-persistent-storage-with-the-portal"></a>Activer ou désactiver le stockage persistant intégré avec le portail
Le portail peut être utilisé pour activer ou désactiver le stockage persistant intégré.

1. Dans la page **d’accueil** de votre portail Azure, sélectionnez **Toutes les ressources**.

    >![Localiser l’icône Toutes les ressources](media/portal-all-resources.jpg)

1. Sélectionnez la ressource Azure Spring Cloud qui nécessite un stockage persistant. Dans notre exemple, l’application sélectionnée est appelée **upspring**.

    > ![Sélectionnez votre application](media/select-service.jpg)

1. Sous l’en-tête **Paramètres**, sélectionnez **Applications**.

1. Vos services Azure Spring Cloud apparaissent dans un tableau.  Sélectionnez le service auquel vous souhaitez ajouter un stockage persistant. Dans cet exemple, le service **passerelle** est sélectionné.

    > ![Sélectionner votre service](media/select-gateway.jpg)

1. Dans la page de configuration du service, sélectionnez **Configuration**

1. Sélectionnez l’onglet **Stockage persistant** et sélectionnez **Activer** pour activer le stockage persistant, ou sélectionnez **Désactiver** pour désactiver le stockage persistant.

    > ![Activer le stockage persistant](media/enable-persistent-storage.jpg)

Une fois le stockage persistant activé, sa taille et son chemin d’accès sont affichés dans l’onglet **Stockage persistant**.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
## <a name="use-the-azure-cli-to-enable-or-disable-built-in-persistent-storage"></a>Utiliser Azure CLI pour activer ou désactiver le stockage persistant intégré
Si nécessaire, installez l’extension Spring Cloud pour l’interface de ligne de commande Azure à l’aide de cette commande :

```azurecli
az extension add --name spring-cloud
```

Autres opérations :

* Pour créer une application avec un stockage persistant intégré activé :

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Pour activer le stockage persistant intégré pour une application existante :

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Pour désactiver le stockage persistant intégré dans une application existante :

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```
---
> [!WARNING]
> Si vous désactivez le stockage persistant d’une application, l’ensemble de ce stockage est libéré et toutes les données stockées sont perdues de façon permanente.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [quotas d’applications et de services](./quotas.md).
* Découvrez comment [mettre à l’échelle manuellement votre application](./how-to-scale-manual.md).
