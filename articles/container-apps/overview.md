---
title: Vue d’ensemble d’Azure Container Apps (préversion)
description: Découvrir les scénarios et usages courants d’Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: overview
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 720d243796781ea2be2f01796c1892e64132f58a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028607"
---
# <a name="azure-container-apps-preview-overview"></a>Vue d’ensemble d’Azure Container Apps (préversion)

Azure Container Apps vous permet d’exécuter des microservices et des applications conteneurisées sur une plateforme serverless. Les usages courants d’Azure Container Apps comprennent :

- Déploiement de points de terminaison d’API
- Hébergement d’applications de traitement en arrière-plan
- Gestion du traitement piloté par les événements
- Exécution de microservices

Les applications basées sur Azure Container Apps peuvent être mises à l’échelle dynamiquement en fonction des caractéristiques suivantes :

- Trafic HTTP
- Traitement piloté par les événements
- Charge du processeur ou de la mémoire
- N’importe quel [scaler pris en charge par KEDA](https://keda.sh/docs/scalers/)

:::image type="content" source="media/overview/azure-container-apps-example-scenarios.png" alt-text="Exemples de scénario pour Azure Container Apps.":::

Azure Container Apps permet d’exécuter du code d’application empaqueté dans n’importe quel conteneur et n’a pas de préférence de runtime ou de modèle de programmation. Avec Container Apps, vous bénéficiez des avantages de l’exécution de conteneurs tout en évitant les soucis de gestion de l’infrastructure cloud et des orchestrateurs de conteneurs complexes.

Avec Azure Container Apps, vous pouvez :

- [**Exécuter plusieurs révisions de conteneur**](application-lifecycle-management.md) et gérer le cycle de vie des applications conteneur.

- [**Mettre à l’échelle automatiquement**](scale-app.md) vos applications avec un déclencheur de mise à l’échelle pris en charge par KEDA. La plupart des applications peuvent être mises à l’échelle à zéro<sup>1</sup>.

- [**Activer l’entrée HTTPS**](ingress.md) sans avoir à gérer une autre infrastructure Azure.

- [**Répartir le trafic**](revisions.md) entre plusieurs versions d’une application pour les déploiements bleus/verts et les scénarios de test A/B.

- [**Utiliser l’entrée interne et la découverte de service**](connect-apps.md) pour sécuriser les points de terminaison internes uniquement avec la détection de service intégrée basée sur le DNS.

- [**Créer des microservices avec Dapr**](microservices.md) et accéder à son ensemble fourni d’API.

- [**Exécuter des conteneurs de n’importe quel registre**](containers.md), public ou privé, notamment Docker Hub et Azure Container Registry (ACR).

- [**Utiliser l’extension Azure CLI ou les modèles ARM**](get-started.md) pour gérer vos applications.

- [**Gérer de façon sécurisée les secrets**](secure-app.md) directement dans votre application.

- [**Consulter les journaux des applications**](monitor.md) avec Azure Log Analytics.

<sup>1</sup> Les applications qui [sont mises à l’échelle lors d’une charge de processeur ou de mémoire](scale-app.md) ne peuvent pas être mises à l’échelle à zéro.

### <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer votre première application conteneur](get-started.md)
