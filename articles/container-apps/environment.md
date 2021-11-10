---
title: Version préliminaire des environnements d’applications Azure Container
description: Découvrez comment les environnements sont gérés dans les applications Azure Container.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 122b172fc9fe2a66bae742f907d910cbccbf8fbc
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025795"
---
# <a name="azure-container-apps-preview-environments"></a>Version préliminaire des environnements d’applications Azure Container

Les applications de conteneur individuelles sont déployées dans un environnement d’applications à conteneur unique, qui joue le rôle de limite sécurisée autour des groupes d’applications de conteneur. Les applications de conteneur d’un même environnement sont déployées dans le même réseau virtuel et écrivent les journaux dans le même espace de travail Log Analytics.

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Environnements d’applications Azure Container.":::

Les raisons de déployer des applications de conteneur dans le même environnement sont les suivantes :

- Gérer les services connexes
- Déployer différentes applications sur le même réseau virtuel
- Faire en sorte que les applications communiquent entre elles à l’aide de Dapr
- Faire en sorte que les applications partagent la même configuration Dapr
- Faire en sorte que les applications partagent le même espace de travail analytique des journaux d'activité

Les raisons pour lesquelles déployer des applications de conteneur dans différents environnements incluent des situations dans lesquelles vous souhaitez garantir :

- Deux applications ne partagent jamais les mêmes ressources de calcul
- Deux applications ne peuvent pas communiquer entre elles via Dapr

## <a name="logs"></a>Journaux d’activité

Paramètres pertinents pour la ressource API d’environnement Azure Container Apps.

| Propriété | Description |
|---|---|
| `properties.appLogsConfiguration` | Utilisé pour configurer l’espace de travail Log Analytics dans lequel les journaux de toutes les applications de l’environnement seront publiés |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | Clé d’instrumentation des informations d’application fournie à Dapr pour le suivi |

## <a name="billing"></a>Facturation

La facturation s’applique uniquement aux applications de conteneur individuelles et à l’utilisation des ressources. Aucuns frais de base n’est associé à l’environnement des applications de conteneur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gestion du comportement de mise à l’échelle automatique](scale-app.md)