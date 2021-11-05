---
title: Prise en charge des régions avec Service Connector
description: Liste de prise en charge des régions et de la disponibilité des régions avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33eefc9a36fcd6e230d95e962da344d7ad738d7d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096301"
---
# <a name="service-connector-region-support"></a>Prise en charge des régions avec Service Connector

Quand vous créez une connexion de service avec Service Connector, la ressource de connexion conceptuelle est provisionnée dans la même région que votre instance de service de calcul par défaut. Cette page affiche les informations de prise en charge des régions et le comportement correspondant de Service Connector (préversion publique).

## <a name="supported-regions-with-regional-endpoint"></a>Régions prises en charge avec point de terminaison régional

Vous trouverez la dernière liste de prise en charge des régions dans [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=service-connector). Si votre instance de service de calcul se trouve dans l’une des régions prises en charge par Service Connector, vous pouvez utiliser Service Connector pour créer et gérer les connexions de service.

## <a name="supported-regions-with-geographical-endpoint"></a>Régions prises en charge avec point de terminaison géographique

Votre instance de service de calcul peut être créée dans la région dans laquelle Service Connector a une prise en charge de région géographique. Cela signifie que votre connexion de service est créée dans une région différente de celle de votre instance de calcul. Une bannière à ce sujet apparaît quand vous créez une connexion de service. La différence de régions peut impacter la conformité, la résidence des données et la latence des données.

## <a name="not-supported-regions-in-public-preview"></a>Régions non prises en charge dans la préversion publique

Vous pouvez toujours voir le nœud du portail ou la commande CLI de Service Connector dans la région prise en charge par Service Connector. Toutefois, vous ne pouvez pas créer ou gérer des connexions de service dans ces régions. L’équipe produit travaille activement à l’activation de davantage de régions.
