---
title: Connecter des applications dans la version préliminaire d’Azure Container
description: Apprenez à déployer plusieurs applications qui communiquent ensemble dans Azure Container.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10fe890ecd4bb2bc89cea71d1a70f7df9eb38e1e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096385"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Connecter des applications dans la version préliminaire d’Azure Container

Les applications Azure Container exposent chaque application conteneur par le biais d’un nom de domaine [ si l'entrée ](ingress.md) est activée. Les points de terminaison d’entrée peuvent être exposés publiquement au monde ou en interne et uniquement disponibles pour d’autres applications de conteneur dans le même [environnement](environment.md).

Une fois que vous connaissez le nom de domaine d’une application conteneur, vous pouvez appeler l’emplacement dans le code de votre application pour connecter plusieurs applications de conteneur.

## <a name="location"></a>Location

L’emplacement d’une application conteneur est composé de valeurs associées à son environnement, son nom et sa région. Disponible via le domaine de niveau supérieur `azurecontainerapps.io`, le nom de domaine complet utilise :

- nom de l’application de conteneur
- identificateur unique de l’environnement
- Nom de la région

Le diagramme suivant montre comment ces valeurs sont utilisées pour composer le nom de domaine complet d’une application conteneur.

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Nom de domaine complet de l’application conteneur Azure Container.":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>Emplacement Dapr

Le développement de microservices vous oblige souvent à implémenter des modèles communs à l’architecture distribuée. Dapr vous permet de sécuriser les microservices avec TLS mutuel, de déclencher de nouvelles tentatives en cas d’erreur et de tirer parti du traçage distribué quand Azure Application Informations est activée.

Un microservice qui utilise DAPR est disponible via le modèle d’URL suivant :

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Emplacement de l’application conteneur Azure Container avec Dapr.":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main](get-started.md)
