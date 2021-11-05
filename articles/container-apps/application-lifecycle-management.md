---
title: Gestion du cycle de vie des applications dans Azure Container (version préliminaire)
description: En savoir plus sur le cycle de vie complet des applications dans Azure Container (version préliminaire)
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: be6070817a8d0b419ba73788b2cde48b02d56af3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096539"
---
# <a name="application-lifecycle-management-in-azure-container-apps-preview"></a>Gestion du cycle de vie des applications dans Azure Container (version préliminaire)

Le cycle de vie des applications Azure Container repose sur les [révisions](revisions.md).

Lorsque vous déployez une application de conteneur, la première révision est créée automatiquement. Des [révisions supplémentaires sont créées](revisions.md) en cas de modification des [conteneurs](containers.md) ou des ajustements sont apportés à la `template` section de la configuration.

Une application de conteneur passe par trois phases : le déploiement, la mise à jour et la désactivation.

## <a name="deployment"></a>Déploiement

Quand une application de conteneur est déployée, la première révision est créée automatiquement.

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deployment.png" alt-text="Applications Azure Container : phase de déploiement":::

## <a name="update"></a>Update

Quand une application de conteneur est mise à jour avec une [étendue de révision-modification](revisions.md#revision-scope-changes), une nouvelle révision est créée. Vous pouvez choisir de [désactiver automatiquement les nouvelles révisions anciennes ou de les autoriser à rester disponibles](revisions.md).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-update.png" alt-text="Applications Azure Container : phase de mise à jour":::

## <a name="deactivate"></a>Désactivation

Une fois qu’une révision n’est plus nécessaire, vous pouvez désactiver une révision à l’aide de l’option pour la réactiver ultérieurement. Lors de la désactivation, le conteneur est [arrêté](#shutdown).

:::image type="content" source="media/application-lifecycle-management/azure-container-apps-lifecycle-deactivate.png" alt-text=" Applications Azure Container : phase de désactivation":::

## <a name="shutdown"></a>Shutdown

Les conteneurs sont arrêtés dans les cas suivants :

- En cas de mise à l’échelle d’une ’application de conteneur
- En de cas de suppression d’une application de conteneur
- En cas de révision en cours de désactivation

Lorsqu’un arrêt est initié, l’hôte de conteneur envoie un [message SIGTERM](https://wikipedia.org/wiki/Signal_(IPC)) à votre conteneur. Le code implémenté dans le conteneur peut répondre à ce message au niveau du système d’exploitation pour gérer l’arrêt.

Si votre application ne répond pas au `SIGTERM` message, [SIGKILL](https://wikipedia.org/wiki/Signal_(IPC)) met fin à votre conteneur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser des révisions](revisions.md)
