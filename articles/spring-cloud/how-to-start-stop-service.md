---
title: Guide pratique de démarrage ou d’arrêt d’une instance de service Azure Spring Cloud
description: Décrit le démarrage ou l’arrêt d’une instance de service Azure Spring Cloud
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 010c117218edccd31fb397785358a35c1774754a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027486"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Démarrer ou arrêter une instance de service Azure Spring Cloud

Cet article explique comment démarrer ou arrêter votre instance de service Azure Spring Cloud.

> [!NOTE]
> L’arrêt et le démarrage sont actuellement en préversion.

Il se peut que vos applications s’exécutant dans Azure Spring Cloud n’aient pas besoin de s’exécuter en continu, par exemple, si vous avez une instance de service qui est utilisée uniquement pendant les heures de bureau. À ce moment-là, Azure Spring Cloud peut être inactif et exécuter uniquement les composants système.

Vous pouvez réduire l’encombrement actif d’Azure Spring Cloud en réduisant les instances en cours d’exécution et en garantissant la réduction des coûts pour les ressources de calcul.

Pour réduire davantage vos coûts, vous pouvez arrêter complètement votre instance de service Azure Spring Cloud. Toutes les applications utilisateur et tous les composants système seront arrêtés. Toutefois, tous vos objets et paramètres réseau seront sauvegardés pour vous permettre de redémarrer votre instance de service et de reprendre là où vous vous étiez arrêté.

> [!NOTE]
> L’état d’une instance de service Azure Spring Cloud arrêtée est conservé pendant jusqu’à 90 jours pendant la préversion. Si votre cluster est arrêté pendant plus de 90 jours, l’état du cluster ne peut pas être récupéré.
> Le temps d’arrêt maximal sera susceptible de changer après la préversion.

Vous pouvez uniquement démarrer, afficher ou supprimer une instance de service Azure Spring Cloud arrêtée. Vous devez démarrer votre instance de service avant d’effectuer toute opération de mise à jour, telle que la création ou la mise à l’échelle d’une application.

## <a name="prerequisites"></a>Prérequis

- Une instance de service existante dans Azure Spring Cloud. Pour créer une instance de service, consultez [Démarrage rapide : Déploiement de votre première application dans Azure Spring Cloud](./quickstart.md).
- (Facultatif) [Azure CLI](/cli/azure/install-azure-cli) version 2.11.2 ou ultérieure.

# <a name="portal"></a>[Portail](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>Arrêter une instance en cours d’exécution

Dans le portail Azure, procédez comme suit pour arrêter une instance Azure Spring Cloud en cours d’exécution :

1. Accédez à la page Vue d’ensemble d’Azure Spring Cloud.
2. Sélectionnez **Arrêter** pour arrêter une instance en cours d’exécution.

   :::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="Capture d’écran du portail Azure montrant la page Vue d’ensemble Azure Spring Cloud avec le bouton Arrêter et la valeur État mis en surbrillance":::

3. Une fois l’instance arrêtée, l’état indique **Réussi (arrêté)** .

## <a name="start-a-stopped-instance"></a>Démarrer une instance arrêtée

Dans le portail Azure, procédez comme suit pour démarrer une instance Azure Spring Cloud arrêtée :

1. Accédez à la page Vue d’ensemble d’Azure Spring Cloud.
2. Sélectionnez **Démarrer** pour démarrer une instance arrêtée.

   :::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="Capture d’écran du portail Azure montrant la page Vue d’ensemble Azure Spring Cloud avec le bouton Démarrer et la valeur État mis en surbrillance":::

3. Une fois l’instance démarrée, l’état indique **Réussi (en cours d’exécution)** .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>Arrêter une instance en cours d’exécution

Dans le portail Azure, utilisez la commande suivante pour arrêter une instance Azure Spring Cloud en cours d’exécution :

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>Démarrer une instance arrêtée

Dans Azure CLI, utilisez la commande suivante pour démarrer une instance Azure Spring Cloud arrêtée :

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>Rechercher l’état d’alimentation

Après l’arrêt ou le démarrage de l’instance, utilisez la commande suivante pour vérifier l’état d’alimentation :

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller des événements de cycle de vie d’application à l’aide du Journal des activités Azure et d’Azure Service Health](./monitor-app-lifecycle-events.md)
* [Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor](../azure-monitor/usage-estimated-costs.md)
