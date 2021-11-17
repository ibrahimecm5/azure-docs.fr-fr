---
title: 'Démarrage rapide : Accès aux relais TURN'
description: Découvrez comment récupérer un jeton STUN/TURN à l’aide d’Azure Communication Services
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 09/28/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 0e813fe3098b71478a88f1a8cf52c523efaaa82f
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893213"
---
# <a name="quickstart-access-turn-relays"></a>Démarrage rapide : Accès aux relais TURN

Ce guide de démarrage rapide montre comment récupérer un jeton de relais réseau pour accéder aux serveurs TURN d’Azure Communication Services.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free).
- Une ressource Azure Communication Services active ou consultez [Créer une ressource Communication Services](./create-communication-resource.md) si vous n’en avez pas.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get a network relay token with Python](./includes/relay-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get a network relay token with Java](./includes/relay-token-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer une ressource Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](./create-communication-resource.md#clean-up-resources).

