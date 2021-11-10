---
title: Révisions dans la préversion d’Azure Container Apps
description: Découvrir comment les révisions sont créées dans Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 543d0465e4fe16aafa911d486d2099259ac8a769
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026650"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Révisions dans la préversion d’Azure Container Apps

Une révision est un instantané immuable d’une application conteneur.

- La première révision est créée automatiquement quand vous déployez votre application conteneur.
- De nouvelles révisions sont créées automatiquement quand la configuration `template` d’une application conteneur change.
- Les révisions sont immuables, mais elles sont affectées par les changements des valeurs de configuration globales, qui s’appliquent à toutes les révisions.

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure Container Apps : conteneurs":::

Les révisions sont particulièrement utiles quand vous activez l’[entrée](ingress.md) pour permettre à votre application conteneur d’être accessible via HTTP.  Les révisions sont souvent utilisées pour diriger le trafic d’un instantané de votre application conteneur vers le suivant. Les stratégies de direction de trafic classiques sont notamment le [Test A/B](https://wikipedia.org/wiki/A/B_testing) et le [déploiement Bluegreen](https://martinfowler.com/bliki/BlueGreenDeployment.html).

Le diagramme suivant montre une application conteneur avec deux révisions.

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure Container Apps : répartition du trafic entre les révisions":::

Le scénario ci-dessus suppose que l’application conteneur est dans l’état suivant :

- L’[entrée](ingress.md) est activée, ce qui rend l’application conteneur disponible via HTTP.
- La première révision est déployée comme _Revision 1_.
- Une fois le conteneur mis à jour, une nouvelle révision a été activée comme _Revision 2_.
- Les règles de [répartition du trafic](revisions-manage.md#traffic-splitting) sont configurées pour que _Revision 1_ reçoive 80 % des demandes et que _Revision 2_ reçoive les 20 % restants.

## <a name="change-types"></a>Types de modification

Les changements effectués sur une application conteneur sont classés dans l’une des deux catégories suivantes : changements *de niveau révision* et *de niveau application*. Les changements de niveau révision sont ceux qui déclenchent une nouvelle révision et les changements de niveau application ne créent pas de révisions.

### <a name="revision-scope-changes"></a>Changements de niveau révision

Les types de changements suivants créent une révision :

- Changements effectués sur les conteneurs
- Ajouter ou mettre à jour des règles de mise à l’échelle
- Changements effectués sur les paramètres Dapr
- Les changements qui affectent la section `template` de la configuration

### <a name="application-scope-changes"></a>Changements de niveau application

Les types de changements suivants ne créent pas de révision :

- Changements effectués sur les [règles de répartition du trafic](revisions-manage.md#traffic-splitting)
- Activation ou désactivation de l’[entrée](ingress.md)
- Changements effectués sur les [valeurs de secret](secure-app.md)
- Les changements qui n’affectent pas la section `template` de la configuration

Même si les changements effectués sur les secrets sont de niveau application, les révisions doivent être [redémarrées](revisions.md) pour que le conteneur reconnaisse les nouvelles valeurs de secret.

## <a name="activation-state"></a>État d’activation

Les nouvelles révisions restent actives jusqu’à ce que vous les désactiviez, mais vous pouvez définir votre application conteneur pour désactiver automatiquement les anciennes révisions.

- Les révisions inactives sont conservées sous la forme d’un enregistrement d’instantané de votre application conteneur dans un état donné.
- Vous n’êtes pas facturé pour les révisions inactives.
- Les révisions sont supprimées définitivement au-delà du seuil de 100 révisions disponibles.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sécuriser une application](get-started.md)
