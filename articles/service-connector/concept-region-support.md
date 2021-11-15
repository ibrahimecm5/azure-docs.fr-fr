---
title: Prise en charge des régions avec Service Connector
description: Liste de prise en charge des régions et de la disponibilité des régions avec Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021, references_regions
ms.openlocfilehash: 75678da43f400eb3ee56f956dcd307207b501e91
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853051"
---
# <a name="service-connector-region-support"></a>Prise en charge des régions avec Service Connector

Quand vous créez une connexion de service avec Service Connector, la ressource de connexion conceptuelle est provisionnée dans la même région que votre instance de service de calcul par défaut. Cette page affiche les informations de prise en charge des régions et le comportement correspondant de Service Connector (préversion publique).

## <a name="supported-regions-with-regional-endpoint"></a>Régions prises en charge avec point de terminaison régional

Si votre instance de service de calcul se trouve dans l’une des régions prises en charge par Service Connector ci-dessous, vous pouvez utiliser Service Connector pour créer et gérer les connexions de service.

- Centre-USA Ouest
- Europe Ouest
- Europe Nord
- USA Est
- USA Ouest 2

## <a name="supported-regions-with-geographical-endpoint"></a>Régions prises en charge avec point de terminaison géographique

Votre instance de service de calcul peut être créée dans la région dans laquelle Service Connector a une prise en charge de région géographique. Cela signifie que votre connexion de service est créée dans une région différente de celle de votre instance de calcul. Une bannière à ce sujet apparaît quand vous créez une connexion de service. La différence de régions peut impacter la conformité, la résidence des données et la latence des données.

- USA Est 2
- USA Ouest 3
- États-Unis - partie centrale méridionale

## <a name="not-supported-regions-in-public-preview"></a>Régions non prises en charge dans la préversion publique

Vous pouvez toujours voir le nœud du portail ou la commande CLI de Service Connector dans la région prise en charge par Service Connector. Toutefois, vous ne pouvez pas créer ou gérer des connexions de service dans ces régions. L’équipe produit travaille activement à l’activation de davantage de régions.
