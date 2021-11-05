---
title: Concepts d’architecture dans Azure IoT Central | Microsoft Docs
description: Cet article présente les concepts clés relatifs à l’architecture d’Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 58c9f69b898a41a1d07ae41755f67d926b2ac74f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082452"
---
# <a name="azure-iot-central-architecture"></a>Architecture d’Azure IoT Central

Cet article donne une vue d’ensemble des concepts clés de l’architecture d’une solution Azure IoT Central.

:::image type="content" source="media/concepts-architecture/architecture.png" alt-text="Architecture générale d’une solution IoT Central" border="false":::

Une application IoT Central :

- Vous permet de gérer les appareils IoT dans votre solution.
- Vous permet de visualiser et d’analyser les données de vos appareils.
- Peut exporter vers et s’intégrer à d’autres services qui font partie de la solution.

## <a name="iot-central"></a>IoT Central

IoT Central est un environnement prêt à l’emploi pour le développement de solutions IoT. C’est une solution IoT PaaS (plateforme en tant que service) et son interface principale est une interface utilisateur web. Il y a également une [API REST](#rest-api) qui vous permet d’interagir par programmation avec votre application.

Cette section décrit les principales fonctionnalités d’une application IoT Central.

### <a name="manage-devices"></a>Gérer des unités

IoT Central vous permet de gérer la flotte des [appareils IoT](#devices) qui envoient des données à votre solution. Par exemple, vous pouvez :

- Contrôler les appareils qui peuvent [se connecter](concepts-get-connected.md) à votre application et comment ils s’authentifient.
- Utiliser des [modèles d’appareils](concepts-device-templates.md) pour définir les types d’appareils qui peut se connecter à votre application.
- Gérer des appareils en définissant des propriétés ou en appelant des commandes sur des appareils connectés. Par exemple, définir une propriété de température cible pour un appareil à thermostat ou appeler une commande pour déclencher la mise à jour de son microprogramme par l’appareil. Vous pouvez définir des propriétés et appeler des commandes sur :
  - Des appareils individuels via une interface utilisateur web [personnalisable](concepts-device-templates.md#views).
  - Plusieurs appareils avec des [travaux](howto-manage-devices-in-bulk.md) planifiés ou à la demande.
- Gérer les [métadonnées des appareils](concepts-device-templates.md#cloud-properties), comme l’adresse du client ou la date de la dernière opération de maintenance.

### <a name="view-and-analyze-data"></a>Visualiser et analyser les données

Dans une application IoT Central, vous pouvez visualiser et analyser les données pour des appareils individuels ou pour les données agrégées provenant de plusieurs appareils :

- Utilisez des modèles d’appareils pour définir des [vues personnalisées](howto-set-up-template.md#views) pour des appareils individuels de types spécifiques. Par exemple, vous pouvez tracer un graphique de la température au fil du temps pour un thermostat individuel ou montrer l’emplacement actuel d’un camion de livraison.
- Utilisez l’[analytique](tutorial-use-device-groups.md) intégrée pour visualiser les données agrégées de plusieurs appareils. Par exemple, vous pouvez voir l’affluence totale dans plusieurs magasins, ou identifier les magasins avec les affluences les plus élevées ou les plus faibles.
- Créez des [tableaux de bord](howto-manage-dashboards.md) personnalisés pour vous aider à gérer vos appareils. Par exemple, vous pouvez ajouter des cartes, des vignettes et des graphiques pour afficher la télémétrie des appareils.  

### <a name="secure-your-solution"></a>Sécuriser votre solution

Dans une application IoT Central, vous pouvez gérer les aspects suivants de la sécurité de votre solution :

- [Connectivité des appareils](concepts-get-connected.md) : créez, révoquez et mettez à jour les clés de sécurité que vos appareils utilisent pour établir une connexion à votre application.
- [Intégrations des applications](howto-authorize-rest-api.md#get-an-api-token) : créez, révoquez et mettez à jour les clés de sécurité que d’autres applications utilisent pour établir une connexion sécurisée à votre application.
- [Exportation de données](howto-export-data.md#connection-options) : utilisez des identités managées pour sécuriser la connexion à vos destinations d’exportation de données.
- [Gestion des utilisateurs](howto-manage-users-roles.md) : gérez les utilisateurs qui peuvent se connecter à l’application et les rôles qui déterminent les autorisations dont disposent ces utilisateurs.
- [Organisations](howto-create-organizations.md) : définissez une hiérarchie pour gérer quels utilisateurs peuvent voir quels appareils dans votre application IoT Central.

### <a name="rest-api"></a>API REST

Créez des intégrations permettant à d’autres applications et services de gérer votre application. Par exemple, [gérez les appareils](howto-control-devices-with-rest-api.md) par programmation dans votre application ou synchronisez les [informations des utilisateurs](howto-manage-users-roles-with-rest-api.md) avec un système externe.

## <a name="devices"></a>Périphériques

Les appareils collectent les données provenant de capteurs et les envoient sous la forme d’un flux de télémétrie à une application IoT Central. Par exemple, une unité de réfrigération envoie un flux de valeurs de température ou un camion de livraison envoie en continu son emplacement.

Un appareil peut utiliser des propriétés pour indiquer son état, par exemple si une vanne est ouverte ou fermée. Une application IoT Central peut également utiliser des propriétés pour définir l’état de l’appareil, par exemple en définissant une température cible pour un thermostat.

IoT Central peut aussi contrôler les appareils en appelant des commandes sur ceux-ci. Par exemple, indiquer à un appareil de télécharger et d’installer une mise à jour du microprogramme.

[La télémétrie, les propriétés et les commandes](concepts-telemetry-properties-commands.md) implémentées par un appareil sont collectivement appelée « fonctionnalités de l’appareil ». Vous définissez ces fonctionnalités dans un modèle qui est partagé entre l’appareil et l’application IoT Central. Dans IoT Central, ce modèle fait partie du modèle d’appareil qui définit un type spécifique d’appareil.

L’[implémentation de l’appareil](tutorial-connect-device.md) doit suivre les [conventions d’IoT Plug-and-Play](../../iot-develop/concepts-convention.md) pour garantir qu’il peut communiquer avec IoT Central. Pour plus d’informations, reportez-vous aux [SDK et exemples](../../iot-develop/libraries-sdks.md) pour les différents langages.

Les appareils se connectent à IoT Central en utilisant un des protocoles pris en charge : [MQTT, AMQP ou HTTP](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="gateways"></a>Passerelles

Des passerelles d’appareils locales sont utiles dans plusieurs scénarios, par exemple :

- Les appareils peuvent ne pas être en mesure de se connecter directement à IoT Central, car ils ne peuvent pas se connecter à Internet. Par exemple, vous pouvez avoir une collection de capteurs d’occupation Bluetooth qui doivent se connecter via une passerelle.
- La quantité de données générées par vos appareils peut être élevée. Pour réduire les coûts, vous pouvez combiner ou agréger les données dans une passerelle locale avant qu’elles ne soient envoyées à votre application IoT Central.
- Votre solution peut nécessiter des réponses rapides aux anomalies dans les données. Vous pouvez exécuter des règles sur une passerelle qui identifient les anomalies et appliquer une action localement sans devoir envoyer des données à votre application IoT Central.

Pour en savoir plus, reportez-vous à [Connecter des appareils Azure IoT Edge à une application Azure IoT Central](concepts-iot-edge.md).

## <a name="data-export"></a>Exportation de données

Bien qu’IoT Central dispose de fonctionnalités d’analytique intégrées, vous pouvez exporter des données vers d’autres services et applications. Les raisons d’exporter des données sont notamment :

### <a name="storage-and-analysis"></a>Stockage et analyse

Pour un stockage à long terme et le contrôle des stratégies d’archivage et de conservation, vous pouvez [exporter vos données en continu](howto-export-data.md) vers d’autres destinations de stockage. L’utilisation d’un stockage distinct vous permet également d’utiliser d’autres outils d’analytique pour dériver des insights et visualiser les données de votre solution.

### <a name="business-automation"></a>Automatisation métier

Les [règles](howto-configure-rules-advanced.md) dans IoT Central vous permettent de déclencher des actions externes, comme envoyer un e-mail ou déclencher un événement, en réponse à des conditions dans IoT Central. Par exemple, vous pouvez informer un ingénieur si la température ambiante d’un appareil atteint un certain seuil.

### <a name="additional-computation"></a>Calculs supplémentaires

Il peut être nécessaire de [transformer ou d’effectuer des calculs](howto-transform-data.md) sur vos données avant de pouvoir les utiliser dans IoT Central ou un autre service. Par exemple, vous pouvez ajouter des informations météorologiques locales aux données d’emplacement signalées par un camion de livraison.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert l’architecture d’Azure IoT Central, l’étape suivante suggérée est de découvrir la [scalabilité et la haute disponibilité](concepts-scalability-availability.md) dans Azure IoT Central.
