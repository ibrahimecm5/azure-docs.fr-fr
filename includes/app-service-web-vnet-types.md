---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2064586fafd82cfc67e14b289797a562e3512afd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245149"
---
* Les niveaux tarifaires de calcul dédiés, y compris les niveaux Basic, Standard, Premium, PremiumV2 et PremiumV3.
* App Service Environment qui se déploie directement dans votre réseau virtuel avec une infrastructure de prise en charge dédiée et utilise les niveaux tarifaires Isolated et IsolatedV2.

La fonctionnalité d’intégration au réseau virtuel est utilisée dans les niveaux tarifaires de calcul dédiés App Service. Si votre application se trouve dans [App Service Environment](../articles/app-service/environment/overview.md), elle est déjà dans un réseau virtuel et ne nécessite pas l’utilisation de la fonctionnalité d’intégration au réseau virtuel pour accéder aux ressources du même réseau virtuel. Pour plus d’informations sur toutes les fonctionnalités de mise en réseau, consultez [Fonctionnalités de mise en réseau App Service](../articles/app-service/networking-features.md).

L’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, sans pour autant accorder d’accès privé entrant à votre application à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre une application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L’intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel. La fonctionnalité d’intégration au réseau virtuel se comporte différemment lorsqu’elle est utilisée avec un réseau virtuel situé dans la même région et dans d’autres régions. La fonctionnalité d’intégration au réseau virtuel présente deux variantes :

* **Regional VNet integration** : Quand vous vous connectez à des réseaux virtuels dans la même région, vous devez disposer d’un sous-réseau dédié dans le réseau virtuel avec lequel vous vous intégrez.
* **Intégration au réseau virtuel avec passerelle obligatoire** : Lors de la connexion directe à des réseaux virtuels dans d’autres régions ou à un réseau virtuel classique dans la même région, vous avez besoin d’une passerelle de réseau virtuel Azure créée dans le réseau virtuel cible.

La fonctionnalité d’intégration au réseau virtuel :

* Nécessite un plan tarifaire App Service **Standard**, **Premium**, **PremiumV2**, **PremiumV3** ou **Elastic Premium**.
* Prend en charge les protocoles TCP et UDP.
* Fonctionne avec les applications Azure App Service et les applications de fonction.

L’intégration au réseau virtuel ne prend pas en charge certaines choses, notamment :

* Montage d’un lecteur.
* Intégration de Windows Server Active Directory.
* NetBIOS.

L’intégration au réseau virtuel avec passerelle obligatoire fournit un accès aux ressources uniquement du réseau virtuel cible, ou des réseaux connectés au réseau virtuel cible avec un peering ou des réseaux privés virtuels. L’intégration au réseau virtuel avec passerelle obligatoire n’autorise pas l’accès aux ressources disponibles sur les connexions Azure ExpressRoute ni ne fonctionne avec des points de terminaison de service.

Quelle que soit la version utilisée, l’intégration au réseau virtuel permet à votre application d’accéder aux ressources de votre réseau virtuel, mais n’accorde pas d’accès privé entrant à votre application à partir du réseau virtuel. L’accès aux sites privés fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L'intégration au réseau virtuel sert uniquement à passer des appels sortants de votre application vers votre réseau virtuel.
