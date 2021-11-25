---
title: Démarrer, arrêter et supprimer une application dans Azure Spring Cloud | Microsoft Docs
description: Guide pratique pour démarrer, arrêter et supprimer une application dans Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 0e27f7ac841635f33bc40db739df4cf2240e2149
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484759"
---
# <a name="start-stop-and-delete-an-application-in-azure-spring-cloud"></a>Démarrer, arrêter et supprimer une application dans Azure Spring Cloud

**Cet article s’applique à :** ✔️ Java ✔️ C#

Ce guide explique comment changer l’état d’une application dans Azure Spring Cloud en utilisant le Portail Azure ou Azure CLI.

## <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Après avoir déployé une application, vous pouvez la démarrer, l’arrêter et la supprimer avec le Portail Azure.

1. Accédez à votre instance du service Azure Spring Cloud dans le portail Azure.
1. Sélectionnez l’onglet **Tableau de bord de l’application**.
1. Sélectionnez l’application dont vous voulez changer l’état.
1. Sur la page de **Présentation** de cette application, sélectionnez **Démarrer/Arrêter**, **Redémarrer** ou **Supprimer**.

## <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

> [!NOTE]
> Vous pouvez utiliser des paramètres facultatifs et configurer des valeurs par défaut avec Azure CLI. Découvrez plus d’informations au sujet d’Azure CLI en lisant notre [documentation de référence](/cli/azure/spring-cloud).

Tout d’abord, installez l’extension Azure Spring Cloud pour Azure CLI comme suit :

```azurecli
az extension add --name spring-cloud
```

Sélectionnez ensuite l’une des opérations Azure CLI suivantes :

* Pour démarrer votre application :

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour arrêter votre application :

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour redémarrer votre application :

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Pour supprimer votre application :

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
