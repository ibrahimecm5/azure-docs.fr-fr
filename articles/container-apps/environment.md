---
title: Version préliminaire des environnements d’applications Azure Container
description: Découvrez comment les environnements sont gérés dans les applications Azure Container.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3de8b98a992a97bb96d5e6164321e89da01f6342
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097462"
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

## <a name="virtual-network-integration"></a>Intégration du réseau virtuel

Une adresse IP externe est automatiquement affectée à chaque environnement. Toutefois, vous pouvez configurer des applications de conteneur individuelles afin qu’elles ne soient pas accessibles à partir de l’extérieur du réseau virtuel.

| Propriété | Description |
|---|---|
| `properties.workerAppsConfiguration.subnetResourceId` | L’ID de la ressource Azure Resource Manager pour le sous-réseau utilisé pour l’infrastructure de l’environnement. |
| `properties.workerAppsConfiguration.aciSubnetResourceName` | Nom d’un sous-réseau dans le même réseau virtuel où s’exécutent les applications de conteneur. |

## <a name="logs"></a>Journaux d’activité

Paramètres pertinentes pour la ressource de l’API d’environnement Kubernetes.

| Propriété | Description |
|---|---|
| `properties.appLogsConfiguration` | Utilisé pour configurer l’espace de travail Log Analytics dans lequel les journaux de toutes les applications de l’environnement seront publiés |
| `properties.workerAppsConfiguration.daprAIInstrumentationKey` | Clé d’instrumentation des informations d’application fournie à Dapr pour le suivi |

## <a name="billing"></a>Facturation

La facturation s’applique uniquement aux applications de conteneur individuelles et à l’utilisation des ressources. Aucuns frais de base n’est associé à l’environnement des applications de conteneur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gestion du comportement de mise à l’échelle automatique](scale-app.md)
