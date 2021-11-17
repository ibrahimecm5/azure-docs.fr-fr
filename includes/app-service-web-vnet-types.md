---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2a95e92ce5bb6705fa42b2885c73480a9d649050
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892847"
---
* Les niveaux tarifaires de calcul dédiés, y compris les niveaux Basic, Standard, Premium, Premium v2 et Premium v3.
* App Service Environment qui se déploie directement dans votre réseau virtuel avec une infrastructure de prise en charge dédiée et utilise les niveaux tarifaires Isolated et v2.

La fonctionnalité d’intégration au réseau virtuel est utilisée dans les niveaux tarifaires de calcul dédiés Azure App Service. Si votre application se trouve dans [Azure App Service Environment](../articles/app-service/environment/overview.md), elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau, consultez [Fonctionnalités de mise en réseau App Service](../articles/app-service/networking-features.md).

L’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais sans pour autant accorder d’accès privé entrant à votre application à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre une application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. La fonctionnalité Intégration du réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. La fonctionnalité d’intégration au réseau virtuel se comporte différemment selon qu’elle est utilisée avec des réseaux virtuels de la même région ou avec ceux d’autres régions. La fonctionnalité d’intégration au réseau virtuel présente deux variantes :

* **Intégration du réseau virtuel régional** : quand vous vous connectez à des réseaux virtuels dans la même région, vous devez disposer d’un sous-réseau dédié dans le réseau virtuel avec lequel vous vous intégrez.
* **Intégration au réseau virtuel avec passerelle obligatoire** :lors de la connexion directe à des réseaux virtuels dans d’autres régions ou à un réseau virtuel classique dans la même région, vous avez besoin d’une passerelle de réseau virtuel Azure créée dans le réseau virtuel cible.

La fonctionnalité d’intégration au réseau virtuel :

* Nécessite un plan tarifaire Standard, Premium, Premium v2, Premium v3 ou Elastic Premium App Service.
* Prend en charge les protocoles TCP et UDP.
* Fonctionne avec les applications App Service et les applications de fonction.

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* Montage d’un lecteur.
* Intégration de Windows Server Active Directory.
* NetBIOS.

L’intégration au réseau virtuel avec passerelle obligatoire fournit un accès aux ressources uniquement dans le réseau virtuel cible ou dans des réseaux connectés au réseau virtuel cible par Peering ou VPN. L’intégration au réseau virtuel avec passerelle obligatoire n’autorise pas l’accès aux ressources disponibles sur les connexions Azure ExpressRoute ni ne fonctionne avec des points de terminaison de service.

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais sans pour autant accorder d’accès privé entrant à votre application à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel.
