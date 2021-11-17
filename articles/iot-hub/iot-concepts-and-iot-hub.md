---
title: Concepts IoT et Azure IoT Hub | Microsoft Docs
description: Cet article présente les concepts de base pour les nouveaux utilisateurs d’Azure IoT Hub.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: d04f5b8ace6bfb331e9460745271d15b82a3fb98
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553461"
---
# <a name="iot-concepts-and-azure-iot-hub"></a>Concepts IoT et Azure IoT Hub

Cet article décrit l’Internet des objets (IoT, Internet of Things), Azure IoT Hub et les appareils IoT.

## <a name="iot-concepts"></a>Concepts de l’IoT

L’Internet des objets (IoT, Internet of Things) est généralement défini comme un réseau d’appareils physiques qui se connectent à d’autres appareils et services sur Internet ou un autre réseau de communication, et échangent des données avec ces appareils et services. Il existe actuellement plus de 10 milliards d’appareils connectés dans le monde, et d’autres sont ajoutés chaque année. Tout ce qui peut être incorporé avec les capteurs et les logiciels nécessaires peut être connecté à Internet. Les technologies qui ont rendu l’IoT possible sont les suivantes :

- Accès à des capteurs à faible coût et à faible consommation d’énergie
- Divers protocoles permettant la connectivité à Internet
- Plateformes de cloud computing comme Azure
- Big Data
- Apprentissage automatique.
- Intelligence artificielle.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub est un service géré, hébergé dans le cloud, qui joue le rôle de hub de messages central pour la communication entre une application IoT et les appareils attachés. Vous pouvez connecter des millions d’appareils et leurs solutions back-end de manière fiable et sécurisée. Presque tous les appareils peuvent être connectés à un hub IoT. 

Plusieurs modèles de messagerie sont pris en charge, notamment la télémétrie appareil-à-cloud, le chargement de fichiers à partir d’appareils et les méthodes demande-réponse pour contrôler vos appareils à partir du cloud. IoT Hub prend également en charge le monitoring pour vous aider à effectuer le suivi de la création des appareils, de leurs connexions et de leurs défaillances.

IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde pour prendre en charge vos charges de travail IoT. Pour plus d’informations sur la mise à l’échelle de votre IoT Hub, consultez [Mise à l’échelle IoT Hub](iot-hub-scaling.md). Pour en savoir plus sur les différents niveaux de service offerts par IoT Hub et sur la meilleure manière de s’adapter à vos besoins d’extensibilité, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/iot-hub/).

Vous pouvez intégrer IoT Hub avec d’autres services Azure pour créer des solutions complètes, de bout en bout. Par exemple, utilisez :

* [Azure Event Grid](../event-grid/index.yml) pour permettre à votre entreprise de réagir rapidement à des événements critiques de façon sûre, évolutive et sécurisée.

* [Azure Logic Apps](../logic-apps/index.yml) pour automatiser les processus métier.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) pour ajouter l’apprentissage automatique et les modèles AI à votre solution.

* [Azure Stream Analytics](../stream-analytics/index.yml) pour exécuter des calculs analytiques en temps réel sur le streaming de données à partir de vos appareils.

Il existe un [contrat de niveau de service pour IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) de 99,9 %. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/) explique la disponibilité garantie d’Azure dans son ensemble.

Chaque abonnement Azure a des limites de quota par défaut pour empêcher tout abus du service. Ces limites peuvent impacter l’étendue de votre solution IoT. La limite actuelle est de 50 IoT Hubs par abonnement. Vous pouvez demander une augmentation de ce quota en contactant le support. Pour plus d’informations, consultez [Quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md). Pour plus d’informations sur les limites de quota, consultez l’un des articles suivants :

* [Limites du service d’abonnement Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [Limitation d’IoT Hub et vous](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-devices"></a>Appareils IoT

Les appareils IoT diffèrent d’autres clients comme les navigateurs et les applications mobiles. Plus précisément, les appareils IoT :

- sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
- peuvent être déployés sur des sites distants avec un accès physique coûteux ;
- peuvent n’être accessibles que par le biais du serveur principal de la solution ;
- peuvent présenter des performances et des ressources de traitement limitées ;
- peuvent afficher une connectivité réseau intermittente, lente ou coûteuse ;
- peuvent exiger l’utilisation de protocoles d’application privés, personnalisés ou propres au secteur.

### <a name="device-identity"></a>Identité d’appareil

Chaque hub IoT possède un registre des identités qui stocke des informations sur les appareils et modules autorisés à s’y connecter. Pour qu’un appareil ou module puisse se connecter, une entrée correspondant à cet appareil ou module doit figurer dans le registre des identités du hub IoT. Un appareil ou module doit également s’authentifier auprès du hub IoT à l’aide des informations d’identification stockées dans le registre des identités.

Nous prenons en charge deux méthodes d’authentification entre l’appareil et le hub IoT. Vous pouvez utiliser une authentification par jeton SAS ou par certificat X.509.

La méthode du jeton SAS fournit une authentification pour chaque appel effectué par l’appareil à IoT Hub en associant la clé symétrique à chaque appel. L’authentification basée sur le certificat X.509 permet l’authentification d’un appareil IoT au niveau physique dans le cadre de l’établissement d’une connexion TLS (Transport Layer Security) standard. La méthode basée sur le jeton de sécurité peut être utilisée sans l’authentification X.509 qui constitue un modèle moins sécurisé. Le choix entre les deux méthodes dépend principalement du niveau requis pour l’authentification de l’appareil et de la disponibilité d’un stockage sécurisé sur l’appareil (pour stocker la clé privée en toute sécurité).

Vous pouvez configurer et approvisionner de nombreux appareils à la fois à l’aide du [Service IoT Hub Device Provisioning](../iot-dps/index.yml).

### <a name="device-communication"></a>Communication de l’appareil

Une fois la méthode d’authentification sélectionnée, la connexion Internet entre l’appareil IoT et IoT Hub est sécurisée suivant la norme TLS (Transport Layer Security). Azure IoT prend en charge TLS 1.2, TLS 1.1 et TLS 1.0, dans cet ordre. La prise en charge de TLS 1.0 est fournie uniquement à des fins de compatibilité descendante. Consultez Prise en charge de TLS dans IoT Hub pour savoir comment configurer votre hub de manière à utiliser TLS 1.2, car il offre le plus de sécurité.

### <a name="device-communication-patterns"></a>Modèles de communication entre les appareils

En règle générale, les appareils IoT envoient la télémétrie des capteurs aux services back-end dans le cloud. Toutefois, d’autres types de communication sont possibles, par exemple un service back-end qui envoie des commandes à vos appareils. Voici des exemples de différents types de communication : 

*  Un camion frigorifique envoie la température à un hub IoT toutes les 5 minutes
*  Un service back-end envoie une commande à un appareil pour changer sa fréquence d’envoi de la télémétrie en vue de diagnostiquer un problème
*  Un appareil qui supervise un réacteur discontinu dans une usine chimique envoie une alerte quand la température dépasse une certaine valeur

### <a name="device-telemetry"></a>Télémétrie d’appareil

Exemples de données de télémétrie reçues d’un appareil : données de capteur telles que la vitesse ou la température, message d’erreur tel qu’un événement manqué ou encore message d’information indiquant que l’appareil est en bonne santé. Les appareils IoT envoient des événements à une application pour obtenir des insights. Les applications peuvent nécessiter des sous-ensembles spécifiques d’événements à des fins de traitement ou de stockage à des points de terminaison différents.

### <a name="device-properties"></a>Propriétés d’appareil

Les propriétés peuvent être lues ou définies dans le hub IoT et permettre d’envoyer des notifications lorsqu’une action a été effectuée. La température est un exemple de propriété spécifique sur un appareil. Il peut s’agir d’une propriété accessible en écriture pouvant être mise à jour sur l’appareil ou lue à partir d’un capteur de température fixé à l’appareil. 

Vous pouvez activer des propriétés dans IoT Hub à l’aide de [jumeaux d’appareils ](iot-hub-devguide-device-twins.md) ou de la fonctionnalité [Plug and Play](../iot-develop/overview-iot-plug-and-play.md).

Pour en savoir plus sur les différences entre les jumeaux d’appareil et la fonctionnalité Plug and Play, consultez[Plug and Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="device-commands"></a>Commandes d’appareil

Le redémarrage d’un appareil constitue un exemple de commande. IoT Hub implémente des commandes en offrant la possibilité d’appeler des méthodes directes sur les appareils. Les [méthodes directes](iot-hub-devguide-direct-methods.md) représentent une interaction de requête-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur). Cette approche est utile pour les scénarios où le plan d’action immédiate est différent selon que l’appareil a été en mesure ou non de répondre.

### <a name="act-on-device-data"></a>Actions basées sur les données des appareils

IoT Hub vous donne la possibilité d’exploiter tout le potentiel des données de vos appareils avec d’autres services Azure, afin que vous puissiez passer à la résolution prédictive des problèmes plutôt qu’à une gestion réactive. Connectez votre hub IoT à d’autres services Azure pour accéder au Machine Learning, aux analyses et à l’IA afin d’agir sur des données en temps réel, d’optimiser le traitement et d’obtenir des insights plus détaillés.

#### <a name="built-in-endpoint-collects-device-data-by-default"></a>Le point de terminaison intégré collecte les données des appareils par défaut

Un point de terminaison intégré collecte les données de votre appareil par défaut. Les données sont recueillies suivant un modèle requête-réponse sur des points de terminaison d’appareil IoT dédiés. Elles sont disponibles pendant une durée maximale de sept jours et peuvent être utilisées pour effectuer des actions sur un appareil. Voici les données acceptées par le point de terminaison de l’appareil :

*    Envoyer des messages appareil-à-cloud.
*    Recevoir des messages cloud-à-appareil.
*    Initier des téléchargements de fichiers.
*    Récupérer et mettre à jour les propriétés d’une représentation d’appareil.
*    Recevoir des requêtes de méthodes directes.

Pour plus d’informations sur les points de terminaison IoT Hub, consultez [Points de terminaison IoT Hub - Guide pour développeurs](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)

#### <a name="message-routing-sends-data-to-other-endpoints"></a>Le routage des messages envoie des données à d’autres points de terminaison

Ces données peuvent également être acheminées vers différents services pour poursuivre leur traitement. À mesure que la solution IoT est mise à l’échelle, le nombre d’appareils, le volume d’événements, la diversité des événements et les différents services varient également. Une méthode flexible, évolutive, cohérente et fiable pour le routage des événements est nécessaire au traitement de ce modèle. Une fois qu’un itinéraire de message a été créé, les données cessent de circuler vers le point de terminaison intégré, sauf si un itinéraire de secours a été configuré. Pour obtenir un didacticiel présentant plusieurs utilisations du routage des messages, consultez le [Didacticiel de routage](tutorial-routing.md).

IoT Hub s’intègre également à Event Grid qui vous permet de répartir les données sur plusieurs abonnés. Event Grid est un service d’événement entièrement géré qui vous permet de facilement gérer des événements dans de nombreux services et applications Azure différents. Conçu pour les performances et la mise à l’échelle, il simplifie la création d’applications pilotées par les événements et d’architectures serverless. Les différences entre le routage des messages et l’utilisation d’Event Grid sont expliquées dans [Comparaison entre le routage des messages et Event Grid](iot-hub-event-grid-routing-comparison.md)

## <a name="next-steps"></a>Étapes suivantes

Pour tester une solution IoT de bout en bout, consultez les démarrages rapides relatifs à IoT Hub :

* [Démarrage rapide : Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)

Pour en savoir plus sur la façon dont vous pouvez générer et déployer des solutions IoT avec Azure IoT, consultez la page :

* [Fondamentaux : Technologies et solutions Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).