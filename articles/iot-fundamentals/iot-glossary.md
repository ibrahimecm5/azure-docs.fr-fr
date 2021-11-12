---
title: Glossaire Azure IoT | Microsoft Docs
description: 'Guide du développeur : Glossaire expliquant certains des termes courants utilisés dans les articles sur Azure IoT.'
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 532bb1f9d9c2e612714a178d3c1445f24cd8c6ea
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552503"
---
# <a name="glossary-of-iot-terms"></a>Glossaire IoT

Cet article répertorie certains termes courants utilisés dans les articles relatifs à l’IoT.

## <a name="a"></a>Un

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queuing Protocol

L’un des protocoles de messagerie qu’[IoT Hub](#iot-hub) et IoT Central prennent en charge pour la communication avec les [appareils](#device).

[En savoir plus](../iot-hub/iot-hub-devguide-protocols.md)

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="allocation-policy"></a>Stratégie d’allocation

Dans le [service d’approvisionnement d’appareils](#device-provisioning-service), la stratégie d’allocation détermine la façon dont le service attribue des [appareils](#device) au [hub IoT lié](#linked-iot-hub).

S’applique à : Service d’approvisionnement d’appareils

### <a name="attestation-mechanism"></a>Mécanisme d’attestation

Dans le [service d’approvisionnement d’appareils](#device-provisioning-service), le mécanisme d’attestation est la méthode utilisée pour confirmer l’identité d’un [appareil](#device). Le mécanisme d’attestation est configuré sur une [inscription](#enrollment).

Les mécanismes d’attestation incluent les certificats X.509, les [modules](#module) de plateforme sécurisée et les clés symétriques.

S’applique à : Service d’approvisionnement d’appareils

### <a name="automatic-deployment"></a>Déploiement automatique

Un déploiement [IoT Edge](#iot-edge) configure un ensemble cible d’[appareils IoT Edge](#iot-edge-device) pour exécuter un ensemble de [modules](#module) IoT Edge. Chaque déploiement s’assure en permanence que tous les [appareils](#device) qui correspondent à la condition cible exécutent l’ensemble spécifié de modules, même lorsque de nouveaux appareils sont créés ou modifiées pour correspondre à la [condition cible](#target-condition). Chaque appareil IoT Edge ne reçoit que le déploiement de priorité la plus élevée pour lequel il répond à la condition cible.

[En savoir plus](../iot-edge/module-deployment-monitoring.md)

S’applique à : IoT Edge

### <a name="automatic-device-configuration"></a>Configuration automatique des appareils

Fonctionnalité d’[IoT Hub](#iot-hub) qui permet au back end de votre [solution](#solution) d’affecter des [propriétés souhaitées](#desired-properties) à un ensemble de [jumeaux d’appareil](#device-twin) et de signaler un état d’[appareil](#device) à l’aide de métriques système et personnalisées.

[En savoir plus](../iot-hub/iot-hub-automatic-device-management.md)

S’applique à : IoT Hub

### <a name="automatic-device-management"></a>Gestion automatique des appareils

Une fonctionnalité d’[IoT Hub](#iot-hub) qui automatise une grande partie des tâches répétitives et complexes liées à la gestion de grandes flottes d’[appareils](#device) pendant tout leur cycle de vie. La fonctionnalité vous permet de cibler un ensemble d’appareils en fonction de leurs [propriétés](#properties), définir la [configuration souhaitée](#desired-configuration) et permettre à IoT Hub de mettre à jour les appareils chaque fois qu’ils se trouvent dans l’étendue.

La gestion automatique des appareils comprend les [configurations automatiques des appareils](../iot-hub/iot-hub-automatic-device-management.md) et les [déploiements automatiques IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

S’applique à : IoT Hub

### <a name="azure-certified-device-program"></a>Programme Azure Certified Device

Azure Certified [Device](#device) est un programme gratuit qui vous permet de différencier, certifier et promouvoir vos appareils IoT conçus pour s’exécuter sur Azure.

[En savoir plus](../certification/overview.md)

S’applique à : IoT Hub, IoT Central

### <a name="azure-digital-twins"></a>Azure Digital Twins

Une offre de plateforme en tant que service (PaaS) pour la création de représentations numériques d’objets, de lieux, de processus métier et de personnes réels. Créez des graphes de jumeaux qui représentent des environnements entiers, et servez-vous-en pour obtenir des insights permettant d’améliorer les produits, d’optimiser les opérations et les coûts et de créer des expériences client exceptionnelles.

[En savoir plus](../digital-twins/overview.md)

S’applique à : Digital Twins

### <a name="azure-digital-twins-instance"></a>Instance Azure Digital Twins

Une seule instance du service [Azure Digital Twins](#azure-digital-twins) dans l’abonnement d’un client. Si [Azure Digital Twins](#digital-twin) fait référence au service Azure dans son ensemble, votre *instance* Azure Digital Twins est votre propre ressource Azure Digital Twins.

S’applique à : Digital Twins

### <a name="azure-iot-explorer"></a>Explorateur Azure IoT

Un outil que vous pouvez utiliser l’outil pour afficher les données de [télémétrie](#telemetry) que l’[appareil](#device) envoie, utiliser les [propriétés](#properties) de l’appareil et appeler des [commandes](#command). Vous pouvez également utiliser l'explorateur pour interagir avec vos appareils et les tester, et pour gérer les [appareils IoT Plug-and-Play](#iot-plug-and-play-device).

[En savoir plus](https://github.com/Azure/azure-iot-explorer)

S’applique à : IoT Hub, Développeur d’appareils

### <a name="azure-iot-tools"></a>Azure IoT Tools

Une extension multiplateforme open source de Visual Studio Code, qui vous permet de gérer Azure [IoT Hub](#iot-hub) et des [appareils](#device) dans Visual Studio Code. Avec Azure IoT Tools, les développeurs IoT peuvent facilement développer des projets IoT dans VS Code

S’applique à : IoT Hub, IoT Edge, IoT Central, Développeur d’appareils

### <a name="azure-iot-device-sdks"></a>Kits Azure IoT device SDK

Ces SDK sont disponibles pour plusieurs langages et permettent de créer des [applications pour appareils](#device-app) qui interagissent avec un [hub IoT](#iot-hub) ou une application IoT Central.

[En savoir plus](../iot-develop/about-iot-sdks.md)

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="azure-iot-service-sdks"></a>Kits Azure IoT service SDK

Ces SDK sont disponibles pour plusieurs langages et permettent de créer des [applications principales](#back-end-app) qui interagissent avec un [hub IoT](#iot-hub).

[En savoir plus](/iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

S’applique à : IoT Hub

## <a name="b"></a>B

### <a name="back-end-app"></a>Application principale

Dans le contexte de [IoT Hub](#iot-hub), une application principale qui se connecte à l’un des [points de terminaison](#endpoint) visible par le service sur un hub IoT. Par exemple, une application back-end peut récupérer des messages [appareil-à-cloud](#device-to-cloud) ou gérer le [registre des identités](#identity-registry). En règle générale, une application principale s’exécute dans le cloud mais, pour plus de simplicité, dans de nombreux didacticiels, les applications principales sont des applications console qui s’exécutent sur votre ordinateur de développement local.

S’applique à : IoT Hub

### <a name="built-in-endpoints"></a>Points de terminaison intégrés

[Points de terminaison](#endpoint) intégrés à [IoT Hub](#iot-hub). Par exemple, chaque IoT Hub inclut un point de terminaison intégré compatible Event Hubs.

S’applique à : IoT Hub

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Passerelle cloud

Application hébergée dans le cloud qui permet la connectivité pour les [appareils](#device) qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub) ou IoT Central. Une [passerelle](#gateway) cloud est hébergée dans le cloud, contrairement à une [passerelle locale](#field-gateway) qui s’exécute localement sur vos appareils. Un cas d’utilisation courant d’une passerelle cloud est l’implémentation d’une traduction de protocole pour vos appareils.

S’applique à : IoT Hub, IoT Central

### <a name="cloud-property"></a>Propriété cloud

Fonctionnalité d’IoT Central qui permet de stocker les métadonnées d’[appareil](#device) dans l’application IoT Central. Les [propriétés](#properties) cloud sont définies dans le [modèle d’appareil](#device-template), mais ne font pas partie du [modèle d’appareil](#device-model). Les propriétés cloud ne sont jamais synchronisées avec un appareil.

S’applique à : IoT Central

### <a name="cloud-to-device"></a>Cloud-à-appareil

Les messages envoyés à partir d’un [hub IoT](#iot-hub) à un [appareil](#device) connecté. Souvent, ces messages sont des [commandes](#command) qui donnent pour instruction à l’appareil d’effectuer une action.

S’applique à : IoT Hub

### <a name="command"></a>Commande

Une commande est définie dans une [interface](#interface) IoT Plug-and-Play pour représenter une méthode qui peut être appelée sur le [jumeau numérique](#digital-twin). Par exemple, une commande pour redémarrer un [appareil](#device). Dans IoT Central, les commandes sont définies dans le [modèle d’appareil](#device-template).

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="component"></a>Composant

Dans IoT Plug-and-Play et [Azure Digital Twins](#azure-digital-twins), les composants vous permettent de créer une [interface](#interface) [modèle](#model) en tant qu’assemblage d’autres interfaces. Un [modèle d’appareil](#device-model) peut combiner plusieurs interfaces sous forme de composants (par exemple, un modèle comportant un composant de commutateur et un composant de thermostat). Il peut également arriver que plusieurs composants d’un modèle utilisent le même type d’interface (par exemple, un modèle comportant deux composants de thermostat).

S’applique à : IoT Hub, Digital Twins, Développeur d’appareils

### <a name="configuration"></a>Configuration

Dans le contexte d’une [configuration automatique des appareils](#automatic-device-configuration) dans [IoT Hub](#iot-hub), une configuration dans IoT Hub définit la [configuration souhaitée](#desired-configuration) pour un ensemble de jumeaux d’[appareils](#device) et fournit un ensemble de métriques pour signaler l’état et la progression.

S’applique à : IoT Hub

### <a name="connection-string"></a>Chaîne de connexion

Utilisez des chaînes de connexion dans votre code d’application afin d’encapsuler les informations requises pour se connecter à un [point de terminaison](#endpoint). Une chaîne de connexion inclut généralement l’adresse du point de terminaison et des informations de sécurité, mais les formats de chaîne de connexion varient selon les services. Il existe deux types de chaîne de connexion associés au service [IoT Hub](#iot-hub) :

- Les *Chaînes de connexion d’[appareil](#device)* permettent aux appareils de se connecter aux points de terminaison visibles par l’appareil sur un hub IoT.
- Les *Chaînes de connexion IoT Hub* activent des [applications principales](#back-end-app) pour se connecter aux points de terminaison côté service sur un hub IoT.

S’applique à : IoT Hub, Développeur d’appareils

### <a name="custom-endpoints"></a>Points de terminaison personnalisés

Des [points de terminaison](#endpoint) définis par l’utilisateur sur un [hub IoT](#iot-hub) qui remettent les messages envoyés par une [règle d’acheminement](#routing-rule). Ces points de terminaison se connectent directement à un hub d’événements, à une file d’attente Service Bus ou à une rubrique Service Bus.

S’applique à : IoT Hub

### <a name="custom-gateway"></a>Passerelle personnalisée

Assure la connectivité des [appareils](#device) qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub) ou IoT Central. Azure [IoT Edge](#iot-edge) permet de créer des [passerelles](#gateway) personnalisées qui implémentent une logique personnalisée pour gérer les messages, les conversions de protocole personnalisées et d’autres opérations.

S’applique à : IoT Hub, IoT Central

## <a name="d"></a>D

### <a name="default-component"></a>Composant par défaut

Tous les [modèles](#model) d’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) disposent d’un [composant](#component) par défaut. Un [modèle d’appareil](#device-model) simple n’a qu’un composant par défaut ; ce type de modèle est également appelé « [appareil](#device) sans composant ». Un modèle plus complexe comporte plusieurs composants imbriqués sous le composant par défaut.

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="deployment-manifest"></a>Manifeste de déploiement

Document JSON dans [IoT Edge](#iot-edge) qui contient les données de [configuration](#configuration) pour un ou plusieurs [jumeaux de module](#module-twin) d’[appareil IoT Edge](#iot-edge-device).

S’applique à : IoT Edge, IoT Central

### <a name="desired-configuration"></a>Configuration souhaitée

Dans le contexte d’un [jumeau d’appareil](#device-twin), une [configuration](#configuration) souhaitée fait référence à l’ensemble complet des [propriétés](#properties) et métadonnées dans le jumeau d’[appareil](#device), qui doivent être synchronisées avec l’appareil.

S’applique à : IoT Hub

### <a name="desired-properties"></a>Propriétés souhaitées

Dans le contexte d’un [jumeau d’appareil](#device-twin), les [propriétés](#properties) souhaitées sont une sous-section du jumeau d’[appareil](#device) qui est utilisée avec des [propriétés signalées](#reported-properties) pour synchroniser une [configuration](#configuration) ou une condition d’appareil. Les propriétés souhaitées peuvent être définies uniquement par une [application principale](#back-end-app) et observées par l’[application pour appareil](#device-app). IoT Central utilise le terme propriétés accessibles en écriture.

S’applique à : IoT Hub

### <a name="device"></a>Appareil

Dans le contexte d’IoT, un appareil est généralement un petit dispositif informatique autonome qui peut collecter des données ou contrôler d’autres appareils. Par exemple, un appareil peut être un dispositif de surveillance de l’environnement ou un contrôleur pour les systèmes de ventilation et d’arrosage dans une serre. Le catalogue d’appareils fournit une liste d’appareils certifiés.

S’applique à : IoT Hub, IoT Central, IoT Edge, Service d’approvisionnement des appareils, Développeur d’appareils

### <a name="device-provisioning-service"></a>Device Provisioning Service

Un service d’assistance pour [IoT Hub](#iot-hub) et IoT Central que vous utilisez pour configurer l’[approvisionnement des appareils](#device-provisioning) sans intervention. Le service DPS vous permet d’approvisionner des millions d’[appareils](#device) de manière sécurisée et évolutive.

S’applique à : IoT Hub, Service d’approvisionnement des appareils, IoT Central

### <a name="device-rest-api"></a>API REST d’appareil

Une API REST que vous pouvez utiliser sur un [appareil](#device) pour envoyer des messages [appareil-à-cloud](#device-to-cloud) à un [hub IoT](#iot-hub) et recevoir des messages [cloud-à-appareil](#cloud-to-device) d’un hub IoT. En règle générale, vous devez utiliser l’un des [kits de développement logiciel (SDK) d’appareils Azure IoT](#azure-iot-device-sdks) de niveau supérieur.

[En savoir plus](/rest/api/iothub/device)

S’applique à : IoT Hub

### <a name="device-app"></a>Application d’appareil

Une application pour appareil s’exécute sur votre [appareil](#device) et gère la communication avec votre [hub IoT](#iot-hub) ou application IoT Central. En règle générale, vous utilisez l’un des kits [Azure IoT device SDK](#azure-iot-device-sdks) lorsque vous implémentez une application pour appareil.

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="device-builder"></a>Générateur d’appareils

Personne responsable de la création du code à exécuter sur vos [appareils](#device). Ils s'appuient généralement sur l'un des [kits de développement logiciel Azure IoT device SDK](#azure-iot-device-sdks) pour implémenter le client d'appareil. Les générateurs d’appareils utilisent un [modèle d’appareil](#device-model) et des [interfaces](#interface) lors de l’implémentation du code à exécuter sur un [appareil IoT Plug-and-Play](#iot-plug-and-play-device).

S’applique à : IoT Hub, IoT Central, IoT Edge, Développeur d’appareils

### <a name="device-identity"></a>Identité d’appareil

Identificateur unique affecté à chaque [appareil](#device) inscrit dans le [registre des identités](#identity-registry) [IoT Hub](#iot-hub) ou dans une application IoT Central.

S’applique à : IoT Hub, IoT Central

### <a name="device-management"></a>Gestion des appareils

La gestion des [appareils](#device) couvre le cycle de vie complet associé à la gestion des appareils dans votre [solution](#solution) IoT, comprenant la planification, l’approvisionnement, la configuration, la surveillance et la mise hors service.

S’applique à : IoT Hub, IoT Central

### <a name="device-model"></a>Modèle de l'appareil

Description, qui utilise le [langage de définition Digital Twins (DTDL)](#digital-twins-definition-language), des fonctionnalités d’un [appareil](#device). Les capacités comprennent la [télémétrie](#telemetry), les [propriétés](#properties) et les [commandes](#command).

[En savoir plus](../iot-develop/concepts-modeling-guide.md)

S’applique à : IoT Hub, IoT Central, Développeur d’appareils, Digital Twins

### <a name="device-provisioning"></a>Approvisionnement des appareils

Le processus d’ajout des données d’[appareil](#device) initial aux magasins dans votre [solution](#solution). Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au [registre des identités](#identity-registry) d’[IoT Hub](#iot-hub). Le [Service d’approvisionnement des appareils](#device-provisioning-service) peut approvisionner automatiquement des appareils dans une application IoT Hub ou IoT Central.

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils

### <a name="device-template"></a>Modèle d’appareil

Dans IoT Central, un modèle d’[appareil](#device) est un blueprint qui définit les caractéristiques et comportements d’un type d’appareil se connectant à votre application.

Par exemple, le modèle d’appareil définit la [télémétrie](#telemetry) qu’un appareil envoie afin qu’IoT Central puisse créer des visualisations qui utilisent les unités et types de données appropriés. Un [modèle de l’appareil](#device-model) fait partie du modèle d’appareil.

S’applique à : IoT Central

### <a name="device-twin"></a>Jumeau d’appareil

Un jumeau d’[appareil](#device) est un document JSON contenant des informations d’état d’appareil telles que des métadonnées, des [configurations](#configuration) et des conditions. [IoT Hub](#iot-hub) conserve une représentation d’appareil pour chaque appareil que vous configurez dans votre IoT Hub. Les jumeaux d’appareil vous permettent de synchroniser des conditions d’appareil et des configurations entre l’appareil et le backend de la [solution](#solution). Vous pouvez interroger des jumeaux d'appareil pour localiser des appareils spécifiques et déterminer l'état d'opérations longues.

S’applique à : IoT Hub

### <a name="device-to-cloud"></a>Appareil-à-cloud

Qualifie des messages envoyés à partir d’un [appareil](#device) connecté vers [IoT Hub](#iot-hub) ou IoT Central.

S’applique à : IoT Hub

### <a name="digital-twins-definition-language"></a>Langage DTDL (Digital Twins Definition Language)

Langage JSON-LD de description de [modèles](#model) et d’[interfaces](#interface) pour [appareils IoT Plug-and-Play](#iot-plug-and-play-device) et entités [Azure Digital Twins](#azure-digital-twins). Le langage permet à la plateforme IoT et aux [solutions](#solution) IoT d’utiliser la sémantique de l’entité.

[En savoir plus](https://github.com/Azure/opendigitaltwins-dtdl)

S’applique à : IoT Hub, IoT Central, Digital Twins

### <a name="digital-twin"></a>Jumeau numérique

Un jumeau numérique est une collection de données numériques qui représente un objet physique. Les modifications apportées à l'objet physique se reflètent dans le jumeau numérique. Dans certains scénarios, vous pouvez utiliser le jumeau numérique pour manipuler l'objet physique. Le [service Azure Digital Twins](../digital-twins/index.yml) utilise des [modèles](#model) exprimés en [DTDL (Digital Twins Definition Language)](#digital-twins-definition-language) pour représenter des jumeaux numériques d’[appareils physiques](#physical-device) ou des concepts métier abstraits de niveau supérieur, ce qui permet de disposer d’un vaste éventail de [solutions](#solution) de jumeau numérique basées sur le cloud. Un [appareil](#device) [IoT Plug-and-Play](../iot-develop/index.yml) a un jumeau numérique, décrit par un [modèle d’appareil](#device-model) de langage de définition Digital Twins.

S’applique à : IoT Hub, IoT Central, Digital Twins, Développeur d’appareils

### <a name="digital-twin-change-events"></a>Événements de changement de jumeau numérique

Lorsqu’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) est connecté à un [hub IoT](#iot-hub), le hub peut utiliser ses capacités de routage pour envoyer des notifications sur les changements de [jumeau numérique](#digital-twin). La fonctionnalité d’exportation de données d’IoT Central peut également transférer les événements de changement de jumeau numérique vers d’autres services. Par exemple, chaque fois que la valeur d’une propriété change sur un [appareil](#device), IoT Hub peut envoyer une notification à un [point de terminaison](#endpoint), comme un hub Event Hub.

S’applique à : IoT Hub, IoT Central

### <a name="digital-twin-graph"></a>Graphique de jumeaux numériques

Dans le service [Azure Digital Twins](#azure-digital-twins), vous pouvez connecter des [jumeaux numériques](#digital-twin) avec des [relations](#relationship) pour créer des graphiques de connaissances représentant numériquement l’ensemble de votre environnement physique. Une seule [instance Azure Digital Twins](#azure-digital-twins-instance) peut héberger de nopmbreux graphiques déconnectés ou un seul graphique interconnecté.

S’applique à : IoT Hub

### <a name="direct-method"></a>Méthode directe

Un moyen de déclencher une méthode à exécuter sur un [appareil](#device) en appelant une API sur votre [hub IoT](#iot-hub).

S’applique à : IoT Hub

### <a name="downstream-service"></a>Service en aval

Terme relatif décrivant les services qui reçoivent des données du contexte actuel. Par exemple, dans le contexte d’[Azure Digital Twins](#azure-digital-twins), Time Series Insights est un service en aval si vous configurez vos données pour qu’elles circulent d’Azure [Digital Twins](#digital-twin) dans Time Series Insights.

S’applique à : IoT Hub, IoT Central, Digital Twins

## <a name="e"></a>E

### <a name="endpoint"></a>Point de terminaison

Représentation nommée d’un service de routage de données pouvant recevoir des données d’autres services.

Un [hub IoT](#iot-hub) expose plusieurs points de terminaison qui permettent à vos applications de s’y connecter. Des points de terminaison visibles par l’[appareil](#device) permettent aux appareils d’effectuer des opérations telles que l’envoi de messages [appareil-à-cloud](#device-to-cloud). Des points de terminaison de gestion côté service permettent aux [applications principales](#back-end-app) d’effectuer des opérations telles que la gestion d’[identité d’appareil](#device-identity). Des [points de terminaison intégrés](#built-in-endpoints) visibles par le service permettent de lire des messages appareil-à-cloud. Vous pouvez créer des [points de terminaison personnalisés](#custom-endpoints) pour recevoir des messages appareil-à-cloud envoyés à l’aide d’une [règle de routage](#routing-rule).

S’applique à : IoT Hub

### <a name="enrollment"></a>Inscription

Dans le [service d’approvisionnement des appareils](#device-provisioning-service), une inscription est l’enregistrement d’[appareils](#device) individuels ou de groupes d’appareils qui peuvent s’inscrire auprès d’un [hub IoT lié](#linked-iot-hub) par le biais de l’approvisionnement automatisé.

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils

### <a name="enrollment-group"></a>Groupe d’inscription

Dans le [service d’approvisionnement des appareils](#device-provisioning-service) et IoT Central, un groupe d’[inscription](#enrollment) identifie un groupe d’[appareils](#device) qui partagent un [mécanisme d’attestation](#attestation-mechanism) par certificat X.509 ou clé symétrique.

S’applique à : IoT Hub, Service d’approvisionnement des appareils, IoT Central

### <a name="event-hubs-compatible-endpoint"></a>Point de terminaison compatible avec Event Hubs

Un [point de terminaison](#endpoint) [IoT Hub](#iot-hub) qui vous permet d’utiliser n’importe quelle méthode compatible Event Hubs pour lire les messages d’[appareil](#device) envoyés au hub. Les méthodes compatibles Event Hub comprennent l’utilisation des kits [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) et d’[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

S’applique à : IoT Hub

### <a name="event-handler"></a>Gestionnaire d'événements

Processus qui est déclenché par l’arrivée d’un événement. Par exemple, vous pouvez créer des gestionnaires d’événements en ajoutant un code de traitement des événements à une fonction Azure, et à envoyer des données via celle-ci en utilisant des [points de terminaison](#endpoint) et un [routage d’événement](#event-routing).

S’applique à : IoT Hub

### <a name="event-routing"></a>Routage d’événement

Processus d’envoi d’événements et de leurs données à partir d’un [appareil](#device) ou d’un service au [point de terminaison](#endpoint) d’un autre.

Dans [IoT Hub](#iot-hub), vous pouvez définir des [règles d’acheminement](#routing-rule) pour décrire la façon dont les messages doivent être envoyés. Dans [Azure Digital Twins](#azure-digital-twins), les itinéraires d’événements sont des entités créées à cette fin. Les itinéraires d’événements Azure [Digital Twins](#digital-twin) peuvent contenir des filtres pour limiter les types d’événements envoyés à chaque point de terminaison.

S’applique à : IoT Hub, Digital Twins

## <a name="f"></a>F

### <a name="field-gateway"></a>Passerelle de champ

Assure la connectivité des [appareils](#device) qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub), et est généralement déployée localement avec vos appareils.

S’applique à : IoT Hub, IoT Central

## <a name="g"></a>G

### <a name="gateway"></a>Passerelle

Une passerelle assure la connectivité d’[appareils](#device) qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Voir aussi [Passerelle de champ](#field-gateway), [Passerelle cloud](#cloud-gateway) et [Passerelle personnalisée](#custom-gateway).

S’applique à : IoT Hub, IoT Central

### <a name="gateway-device"></a>Appareil de passerelle

Un exemple de [passerelle locale](#field-gateway). Un [appareil](#device) [passerelle](#gateway) peut être un appareil IoT standard ou un [appareil IoT Edge](#iot-edge-device).

Un appareil passerelle assure la connectivité d’appareils en aval qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub).

S’applique à : IoT Hub, IoT Central, IoT Edge

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Module de sécurité matériel

Utilisé pour le stockage matériel sécurisé des secrets de l’[appareil](#device). Il s’agit de la forme de stockage des secrets la plus sécurisée pour un appareil. Un [module](#module) de sécurité matériel peut stocker des clés symétriques et des certificats X.509. Dans le [service d’approvisionnement des appareils](#device-provisioning-service), un [mécanisme d’attestation](#attestation-mechanism) peut utiliser un module de sécurité du matériel.

S’applique à : IoT Hub, Développeur d’appareils, Service d’approvisionnement des appareils

## <a name="i"></a>I

### <a name="id-scope"></a>Étendue de l’ID

Une valeur unique attribuée à une instance du [service d’approvisionnement des appareils](#device-provisioning-service) lors de sa création.

Les applications IoT Central utilisent des instances DPS et rendent l’étendue de l’ID disponible par le biais de l’interface utilisateur d’IoT Central.

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils

### <a name="identity-registry"></a>Registre des identités

Un [composant](#component) intégré d’un [IoT Hub](#iot-hub), qui stocke des informations sur les [appareils](#device) autorisés à se connecter à un IoT Hub.

S’applique à : IoT Hub

### <a name="individual-enrollment"></a>Inscription individuelle

Identifie un [appareil](#device) unique que le [service d’approvisionnement des appareils](#device-provisioning-service) peut approvisionner sur un [hub IoT](#iot-hub).

S’applique à : IoT Hub, Service d’approvisionnement des appareils

### <a name="interface"></a>Interface

Dans IoT Plug-and-Play, une interface décrit les capacités associées qui sont implémentées par un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) ou par un [jumeau numérique](#digital-twin). Il est possible de réutiliser des interfaces sur différents [modèles d’appareil](#device-model). Quand une interface est employée dans un [modèle](#model) d’[appareil](#device), elle définit un [composant](#component) de l’appareil. Un appareil simple contient seulement une interface par défaut.

Dans [Azure Digital Twins](#azure-digital-twins), vous pouvez utiliser une *interface* pour faire référence à l’élément de code de niveau supérieur dans une définition de modèle [DTDL (Digital Twins Definition Language)](#digital-twins-definition-language).

S’applique à : Développeur d’appareils, Digital Twins

### <a name="iot-edge"></a>IoT Edge

Un service et les bibliothèques clientes et le runtime connexes qui permettent le déploiement cloud de services Azure, et le code spécifique à la [solution](#solution) sur les [appareils](#device) locaux. Les [appareils IoT Edge](#iot-edge-device) peuvent agréger des données provenant d'autres appareils pour effectuer des calculs et des analyses avant d'envoyer les données vers le cloud.

[En savoir plus](../iot-edge/index.yml)

S’applique à : IoT Edge

### <a name="iot-edge-agent"></a>Agent IoT Edge

Partie du [runtime IoT Edge](#iot-edge-runtime) responsable des [modules](#module) de déploiement et de surveillance.

S’applique à : IoT Edge

### <a name="iot-edge-device"></a>Appareil IoT Edge

Un [appareil](#device) qui utilise des [modules](#module) [IoT Edge](#iot-edge) conteneurisés pour exécuter des services Azure, des services tiers ou votre propre code. Sur l’appareil, le [runtime IoT Edge](#iot-edge-runtime) gère les modules. Vous pouvez surveiller et gérer un appareil IoT Edge à distance à partir du cloud.

S’applique à : IoT Edge

### <a name="iot-edge-hub"></a>Hub IoT Edge

La partie du [runtime IoT Edge](#iot-edge-runtime) responsable des communications de [module](#module) à module, en amont et en aval.

S’applique à : IoT Edge

### <a name="iot-edge-runtime"></a>Runtime IoT Edge

Inclut tout ce que Microsoft distribue pour l’installation sur un [appareil IoT Edge](#iot-edge-device). Il comprend l’agent Edge, le hub Edge et le démon de sécurité [IoT Edge](#iot-edge).

S’applique à : IoT Edge

### <a name="iot-hub"></a>IoT Hub

Un service Azure entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’[appareils](#device) et un backend de [solution](#solution). Pour plus d’informations, consultez [Qu’est-ce qu’Azure IoT Hub ?](../iot-hub/about-iot-hub.md). Votre abonnement Azure vous permet de créer des hubs IoT pour gérer vos charges de travail de messagerie IoT.

[En savoir plus](../iot-hub/about-iot-hub.md)

S’applique à : IoT Hub

### <a name="iot-hub-resource-rest-api"></a>API REST de ressource IoT Hub

API que vous pouvez utiliser pour gérer les [hubs IoT](#iot-hub) dans votre abonnement Azure avec des opérations telles que la création, la mise à jour et la suppression de hubs.

S’applique à : IoT Hub

### <a name="iot-hub-metrics"></a>Métriques IoT Hub

Fonctionnalité du portail Azure qui vous permet de surveiller l’état de vos [hubs IoT](#iot-hub). Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale d’un hub IoT et des [appareils](#device) connectés à ce dernier.

S’applique à : IoT Hub

### <a name="iot-hub-query-language"></a>Langage de requête IoT Hub

Langage de type SQL pour [IoT Hub](#iot-hub) qui vous permet d’interroger vos [travaux](#job), [jumeaux numériques](#digital-twin) et [jumeaux d’appareil](#device-twin).

S’applique à : IoT Hub

### <a name="iot-plug-and-play-bridge"></a>Pont IoT Plug-and-Play

Une application open source permettant à des capteurs ou périphériques attachés à des [passerelles](#gateway) Windows ou Linux de se connecter en tant qu’[appareils IoT Plug-and-Play](#iot-plug-and-play-device).

S’applique à : IoT Hub, Développeur d’appareils, IoT Central

### <a name="iot-plug-and-play-conventions"></a>Conventions IoT Plug-and-Play

Ensemble de conventions que les [appareils](#device) IoT doivent suivre lorsqu’ils échangent des données avec une [solution](#solution).

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="iot-plug-and-play-device"></a>Appareil IoT Plug-and-Play

Généralement un petit [appareil](#device) informatique autonome qui collecte des données ou contrôle d’autres appareils, et exécute un logiciel ou un microprogramme implémentant un [modèle d’appareil](#device-model). Par exemple, un appareil IoT Plug-and-Play peut être un appareil de supervision de l’environnement ou un contrôleur pour un système d’irrigation agricole intelligent. Un appareil IoT Plug-and-Play peut être implémenté directement ou en tant que [module](#module) [IoT Edge](#iot-edge).

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="iot-extension-for-azure-cli"></a>Extension IoT pour Azure CLI

Une extension pour Azure CLI. L’extension vous permet d’effectuer des tâches telles que la gestion de vos [appareils](#device) dans le [registre des identités](#identity-registry), l’envoi et la réception de messages d’appareil et la surveillance de vos opérations de [hub IoT](#iot-hub).

[En savoir plus](/cli/azure/azure-cli-reference-for-IoT)

S’applique à : IoT Hub, IoT Central, IoT Edge, Service d’approvisionnement des appareils, Développeur d’appareils

## <a name="j"></a>J

### <a name="job"></a>Travail

Dans le contexte d’[IoT Hub](#iot-hub), les [tâches](#device) vous permettent de planifier et de suivre les activités sur un ensemble d’appareils inscrits auprès de votre IoT Hub. Ces activités comprennent la mise à jour des [propriétés souhaitées](#desired-properties) de l’appareil, la mise à jour des [balises](#tag) de [jumeau d’appareil](#device-twin) et l’appel de [méthodes directes](#direct-method). IoT Hub utilise également des travaux pour importer dans et exporter à partir du [registre des identités](#identity-registry).

Dans le contexte d’IoT Central, les travaux vous permettent de gérer vos appareils connectés en bloc en définissant les [propriétés](#properties) et en appelant les [commandes](#command). Les tâches de IoT Central vous permettent également de mettre à jour les Propriétés de Cloud en bloc.

S’applique à : IoT Hub, IoT Central

## <a name="l"></a>L

### <a name="leaf-device"></a>Appareil de nœud terminal

Un [appareil](#device) sans aucun appareil en aval connecté. En règle générale, les appareils de nœud terminal sont connectés à un [appareil de passerelle](#gateway-device).

S’applique à : IoT Edge, IoT Central, Développeur d’appareils

### <a name="lifecycle-event"></a>Événement de cycle de vie

Dans [Azure Digital Twins](#azure-digital-twins), ce type d’événement est déclenché quand un élément de données (par exemple, un [jumeau numérique](#digital-twin), une [relation](#relationship) ou un [gestionnaire d’événements](#event-handler)) est créé ou supprimé à partir de votre [instance Azure Digital Twins](#azure-digital-twins-instance).

S’applique à : Digital Twins, IoT Hub, IoT Central

### <a name="linked-iot-hub"></a>Hub IoT lié

Un [hub IoT](#iot-hub) lié à une instance du [Service d’approvisionnement des appareils](#device-provisioning-service). Une instance DPS peut inscrire un ID d’[appareil](#device) et définir la [configuration](#configuration) initiale dans les [jumeaux d’appareil](#device-twin) dans les hubs IoT liés.

S’applique à : IoT Hub, Service d’approvisionnement des appareils

## <a name="m"></a>M

### <a name="mqtt"></a>MQTT

L’un des protocoles de messagerie qu’[IoT Hub](#iot-hub) et IoT Central prennent en charge pour la communication avec les [appareils](#device). MQTT n’a pas de signification particulière.

[En savoir plus](../iot-hub/iot-hub-devguide-protocols.md)

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="model"></a>Modéliser

Définition d’un type d’entité dans votre environnement physique, y compris ses [propriétés](#properties), sa télémétrie et ses [composants](#component). Des modèles sont utilisés pour créer des [jumeaux numériques](#digital-twin) qui représentent des objets physiques spécifiques de ce type. Les modèles sont écrits en [Digital Twins Definition Language](#digital-twins-definition-language).

Dans le service [Azure Digital Twins](#azure-digital-twins), les [modèles](#device) définissent des appareils ou des concepts métier abstraits de niveau supérieur. Dans IoT Plug-and-Play, les [modèles d’appareil](#device-model) décrivent des appareils de manière spécifique.

S’applique à : IoT Hub, IoT Central, Digital Twins, Développeur d’appareils

### <a name="model-id"></a>ID de modèle

Lorsqu’un [appareil IoT Plug-and-Play](#iot-plug-and-play-device) se connecte à une application [IoT Hub](#iot-hub) ou IoT Central, il envoie l’ID de [modèle](#model) du modèle de [langage de définition Digital Twins (DTDL)](#digital-twins-definition-language) qu’il implémente. Chaque modèle en tant qu’ID de modèle unique. Cet ID de modèle permet à la [solution](#solution) de trouver le [modèle d’appareil](#device-model).

S’applique à : IoT Hub, IoT Central, Développeur d’appareils, Digital Twins

### <a name="model-repository"></a>Référentiel de modèles

Stocke les [interfaces](#interface) et les [modèles](#model) du [langage de définition Digital Twins](#digital-twins-definition-language). Une [solution](#solution) utilise un [ID de modèle](#model-id) pour récupérer un modèle à partir d’un référentiel.

S’applique à : IoT Hub, IoT Central, Digital Twins

### <a name="model-repository-rest-api"></a>API REST de référentiel de modèles

Il s’agit d’une API permettant de gérer un [référentiel de modèle](#model-repository) et d’interagir avec eux. Par exemple, vous pouvez l’utiliser pour ajouter et rechercher des [modèles d’appareil](#device-model).

S’applique à : IoT Hub, IoT Central, Digital Twins

### <a name="module"></a>Module

Les kits de développement logiciel (SDK) d’[appareil](#device) [IoT Hub](#iot-hub) vous permettent d’instancier des modules où chacun d’entre eux ouvre une connexion indépendante à votre IoT Hub. Vous pouvez utiliser des espaces de noms distincts pour les différents [composants](#component) de votre appareil.

L’[identité de module](#module-identity) et le [jumeau de module](#module-twin) fournissent les mêmes fonctionnalités que l’[identité d’appareil](#device-identity) et le [jumeau d’appareil](#device-twin), mais avec une plus grande précision.

Dans [IoT Edge](#iot-edge), un module est un conteneur Docker que vous pouvez déployer sur des [appareils IoT Edge](#iot-edge-device). Il effectue une tâche spécifique, comme l’ingestion de messages provenant d’appareils, la transformation de messages ou l’envoi de messages à un IoT Hub. Il communique avec d’autres modules et envoie des données au [runtime IoT Edge](#iot-edge-runtime).

S’applique à : IoT Central, IoT Edge, Développeur d’appareils

### <a name="module-identity"></a>Identité de module

Un identificateur unique attribué à chaque [module](#module) qui appartient à un [appareil](#device). Les identités de module sont également enregistrées dans le [registre des identités](#identity-registry).

L’identité de module détaille les informations d’identification de sécurité que le module utilise pour s’authentifier auprès de l’[IoT Hub](#iot-hub) ou, dans le cas d’un module [IoT Edge](#iot-edge) auprès du [hub IoT Edge](#iot-edge-hub).

S’applique à : IoT Central, IoT Edge, Développeur d’appareils

### <a name="module-image"></a>Image de module

Image Docker utilisée que le [runtime IoT Edge](#iot-edge-runtime) utilise pour instancier des instances du [module](#module).

S’applique à : IoT Edge

### <a name="module-twin"></a>Jumeau de module

À l’image d’un [jumeau d’appareil](#device-twin), un jumeau de [module](#module) est un document JSON contenant des informations d’état de module telles que des métadonnées, des [configurations](#configuration) et des conditions. [IoT Hub](#iot-hub) conserve un jumeau de module pour chaque [identité de module](#module-identity) que vous provisionnez sous une [identité d’appareil](#device-identity) dans votre hub IoT. Les jumeaux de module vous permettent de synchroniser des conditions de module et des configurations entre le module et le backend de la [solution](#solution). Vous pouvez interroger des jumeaux de module pour localiser des modules spécifiques et déterminer l’état d’opérations longues.

S’applique à : IoT Hub

## <a name="o"></a>O

### <a name="ontology"></a>Ontologie

Dans le contexte de [Digital Twins](#digital-twin), un ensemble de [modèles](#model) pour un domaine particulier, comme le patrimoine immobilier, les villes intelligentes, les systèmes IoT, les réseaux d’énergie et bien plus encore. Les ontologies sont souvent utilisées comme schémas pour les graphiques de connaissances tels que ceux d’[Azure Digital Twins](#azure-digital-twins), car elles fournissent un point de départ basé sur les normes du secteur et les meilleures pratiques.

[En savoir plus](../digital-twins/concepts-ontologies.md)

S’applique à : Digital Twins

### <a name="operations-monitoring"></a>Surveillance des opérations

Fonctionnalité d’[IoT Hub](#iot-hub) qui vous permet de surveiller l’état des opérations sur votre hub IoT en temps réel. IoT Hub effectue le suivi des événements entre différentes catégories d’opérations. Vous pouvez opter pour l’envoi des événements d’une ou plusieurs catégories à un [point de terminaison](#endpoint) d’IoT Hub en vue de leur traitement. Vous pouvez surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

S’applique à : IoT Hub

## <a name="p"></a>P

### <a name="physical-device"></a>Appareil physique

[Appareil](#device) IoT réel qui se connecte à un [hub IoT](#iot-hub). Pour plus de commodité, de nombreux didacticiels et guides de démarrage rapide exécutent du code d’appareil IoT sur un ordinateur de bureau plutôt que sur un appareil physique.

S’applique à : IoT Hub, IoT Central, Développeur d’appareils, IoT Edge

### <a name="primary-and-secondary-keys"></a>Clés primaires et secondaires

Lorsque vous vous connectez à un [point de terminaison](#endpoint) visible par l’[appareil](#device) ou par le service sur un [hub IoT](#iot-hub) ou une application IoT Central, votre [chaîne de connexion](#connection-string) inclut la clé vous permettant d’accéder. Lorsque vous ajoutez un appareil au [registre des identités](#identity-registry) ou une [stratégie d’accès partagé](#shared-access-policy) à votre hub, le service génère une clé primaire et une clé secondaire. Le fait de disposer de deux clés vous permet de passer d’une clé à un autre lorsque vous mettez à jour une clé sans perdre l’accès au hub IoT ou à l’application IoT Central.

S’applique à : IoT Hub, IoT Central

### <a name="properties"></a>Propriétés

Dans le contexte d’un [jumeau numérique](#digital-twin), les propriétés sont des champs de données définis dans une [interface](#interface), qui représentent un état persistant du jumeau numérique. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture. Les propriétés en lecture seule, comme le numéro de série, sont définies par du code s’exécutant sur l’[appareil IoT Plug-and-Play](#iot-plug-and-play-device) lui-même. Les propriétés accessibles en écriture, comme un seuil d’alarme, sont généralement définies à partir de la [solution](#solution) IoT cloud.

S’applique à : IoT Hub, IoT Central, Digital Twins, Développeur d’appareils

### <a name="property-change-event"></a>Événement de changement de propriété

Événement résultant d’un changement de propriété dans un [jumeau numérique](#digital-twin).

S’applique à : IoT Hub, IoT Central, Digital Twins

### <a name="protocol-gateway"></a>Passerelle de protocole

Une [passerelle](#gateway) généralement déployée dans le cloud pour fournir des services de traduction de protocole pour les [appareils](#device) qui se connectent à un [hub IoT](#iot-hub) ou à une application IoT Central.

S’applique à : IoT Hub, IoT Central

## <a name="r"></a>R

### <a name="registration"></a>Inscription

Enregistrement d’un [appareil](#device) dans le [registre des identités](#identity-registry) d’[IoT Hub](#iot-hub). Vous pouvez vous inscrire l’appareil directement ou utiliser le [Service d’approvisionnement des appareils](#device-provisioning-service) pour automatiser l’inscription de l’appareil.

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils

### <a name="registration-id"></a>ID d’enregistrement

Identité d’[appareil](#device-identity) unique dans le [Service d’approvisionnement des appareils](#device-provisioning-service). L’ID d’[inscription](#registration) peut avoir la même valeur que l’identité d’[appareil](#device).

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils

### <a name="relationship"></a>Relation

Utilisées dans le service [Azure Digital Twins](#azure-digital-twins) pour connecter des [jumeaux numériques](#digital-twin) à des graphiques de connaissances qui représentent numériquement l’ensemble de votre environnement physique. Les types de relations que vos jumeaux peuvent avoir sont définis dans le [modèle](#model) de [langage de définition Digital Twins](#digital-twins-definition-language).

S’applique à : Digital Twins

### <a name="reported-configuration"></a>Configuration signalée

Dans le contexte d’un [jumeau d’appareil](#device-twin), cela fait référence à l’ensemble complet des [propriétés](#properties) et métadonnées dans le jumeau d’[appareil](#device), qui doivent être signalées au serveur principal de [solution](#solution).

S’applique à : IoT Hub, Développeur d’appareils

### <a name="reported-properties"></a>Propriétés signalées

Dans le contexte d’un [jumeau d’appareil](#device-twin), les [propriétés](#properties) signalées sont une sous-section du [jumeau d’appareil](#device). Les propriétés signalées peuvent être définies uniquement par l’appareil, mais peuvent être lues et interrogées par une [application principale](#back-end-app).

S’applique à : IoT Hub, Développeur d’appareils

### <a name="retry-policy"></a>Stratégie de nouvelle tentative

Un moyen de gérer des erreurs temporaires lorsque vous vous connectez à un service cloud.

S’applique à : IoT Central, IoT Central, Développeur d’appareils

### <a name="routing-rule"></a>Règle de routage

Une fonctionnalité d’[IoT Hub](#iot-hub) pour acheminer les messages [appareil-à-cloud](#device-to-cloud) vers un [point de terminaison](#endpoint) intégré ou vers des [points de terminaison personnalisés](#custom-endpoints) afin qu’ils soient traités par votre backend de [solution](#solution).

S’applique à : IoT Hub

## <a name="s"></a>S

### <a name="saslplain"></a>SASL/PLAIN

Protocole utilisé par le [protocole de mise en file d’attente de messages avancé (AMPQ)](#advanced-message-queueing-protocol) pour transférer des jetons de sécurité.

[En savoir plus](https://wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

S’applique à : IoT Hub

### <a name="service-rest-api"></a>API REST du service

Une API REST que vous pouvez utiliser à partir du backend de la [solution](#solution) pour gérer vos [appareils](#device). Par exemple, vous pouvez utiliser l’API de service [IoT Hub](#iot-hub) pour récupérer et mettre à jour les [propriétés](#properties) de [jumeau d’appareil](#device-twin), appeler des [méthodes directes](#direct-method) et planifier des [travaux](#job). En général, vous devez utiliser l’un des kits de développement logiciel (SDK) de service de niveau supérieur.

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils, IoT Edge

### <a name="service-operations-endpoint"></a>Point de terminaison des opérations de service

Un [point de terminaison](#endpoint) utilisé par un administrateur pour gérer les paramètres de service. Par exemple, dans le [Service d’approvisionnement des appareils](#device-provisioning-service), vous utilisez le point de terminaison de service pour gérer les [inscriptions](#enrollment).

S’applique à : IoT Hub, Service d’approvisionnement des appareils, IoT Edge, Digital Twins

### <a name="shared-access-policy"></a>Stratégie d’accès partagé

Un moyen de définir les autorisations accordées à toute personne disposant d’une clé primaire ou secondaire valide associée à cette stratégie. Vous pouvez gérer les stratégies d’accès partagé et les clés de votre hub via le portail.

S’applique à : IoT Hub, IoT Edge, Service d’approvisionnement des appareils

### <a name="shared-access-signature"></a>Signature d’accès partagé

Une signature d’accès partagé est un URI signé qui pointe vers une ou plusieurs ressources telles qu’un [point de terminaison](#endpoint) [IoT Hub](#iot-hub). L’URI comprend un jeton qui indique comment le client peut accéder aux ressources. L’un des paramètres de requête, la signature, est construit à partir des paramètres de signature d’accès partagé et signé avec la clé utilisée pour créer la SAP. Cette signature est utilisée par le stockage Azure pour autoriser l’accès à la ressource de stockage.

S’applique à : IoT Hub, Digital Twins, IoT Central, IoT Edge

### <a name="simulated-device"></a>Appareil simulé

Pour plus de commodité, un grand nombre des tutoriels et guides de démarrage rapide exécutent du code d’[appareil](#device) avec des capteurs simulés sur votre ordinateur de développement local. En revanche, un [appareil physique](#physical-device), comme un MXCHIP, a des capteurs réels et se connecte à un [hub IoT](#iot-hub).

S’applique à : IoT Hub, IoT Central, Développeur d’appareils, IoT Edge, Digital Twins, Service d’approvisionnement des appareils

### <a name="solution"></a>Solution

Dans le contexte de l’IoT, une *solution* fait généralement référence à une solution IoT incluant des éléments tels que des [appareils](#device), des [applications pour appareil](#device-app), un [hub IoT](#iot-hub), d’autres services Azure et des [applications principales](#back-end-app).

S’applique à : IoT Hub, IoT Central, Service d’approvisionnement des appareils, IoT Edge, Digital Twins

### <a name="system-properties"></a>Propriétés système

Dans le contexte d’un [jumeau d’appareil](#device-twin), les [propriétés](#properties) en lecture seule qui incluent des informations sur l’utilisation de l’[appareil](#device), telles que l’heure et l’état de connexion de la dernière activité.

S’applique à : IoT Hub

## <a name="t"></a>T

### <a name="tag"></a>Tag

Dans le contexte d’un [jumeau d’appareil](#device-twin), les balises sont des métadonnées d’[appareil](#device) stockées et récupérées par le backend de [solution](#solution) sous la forme d’un document JSON. Les balises ne sont pas visibles pour les applications sur un appareil.

S’applique à : IoT Hub

### <a name="target-condition"></a>Condition cible

Dans un déploiement [IoT Edge](#iot-edge), la condition cible sélectionne les [appareils](#device) cibles du déploiement. La condition cible est évaluée en permanence pour inclure les nouveaux appareils qui répondent aux exigences ou pour supprimer les appareils qui n’y répondent plus.

S’applique à : IoT Edge

### <a name="telemetry"></a>Télémétrie

Les données, comme la vitesse du vent ou la température, sont envoyées à un [hub IoT](#iot-hub) qui a été collecté par un [appareil](#device) à partir de ses capteurs.

Contrairement aux [propriétés](#properties), la télémétrie n’est pas stockée sur un [jumeau numérique](#digital-twin). Il s’agit d’un flux d’événements de données limités dans le temps qui doivent être traités au fur et à mesure qu’ils se produisent.

Dans IoT Plug-and-Play et [Azure Digital Twins](#azure-digital-twins), les champs de télémétrie définis dans une [interface](#interface) représentent des mesures. Ces mesures sont généralement des valeurs comme des lectures de capteur qu’envoient des appareils tels que des [appareils IoT Plug-and-Play](#iot-plug-and-play-device) sous la forme d’un flux de données.

S’applique à : IoT Hub, IoT Central, Digital Twins, IoT Edge, Développeur d’appareils

### <a name="telemetry-event"></a>Événement de télémétrie

Un événement dans un [hub d’événements](#iot-hub) indiquant l’arrivée de données de [télémétrie](#telemetry).

S’applique à : IoT Hub

### <a name="twin-queries"></a>Requêtes de jumeaux

Fonctionnalité d’[IoT Hub](#iot-hub) qui vous permet d’utiliser un langage de requête de type SQL pour récupérer des informations à partir de vos [jumeaux d’appareil](#device-twin) ou [jumeaux de module](#module-twin).

S’applique à : IoT Hub

### <a name="twin-synchronization"></a>Synchronisation des jumeaux

Le processus dans [IoT Hub](#iot-hub) qui utilise les [propriétés souhaitées](#desired-properties) de vos [jumeaux d’appareil](#device-twin) ou [jumeaux de module](#module-twin) pour configurer vos [appareils](#device) ou [modules](#module) et extraire de ceux-ci des [propriétés signalées](#reported-properties) à stocker dans le jumeau.

S’applique à : IoT Hub

## <a name="u"></a>U

### <a name="upstream-service"></a>Services en amont

Terme relatif décrivant des services sources de données dans le contexte actuel. Par exemple, dans le contexte d’[Azure Digital Twins](#azure-digital-twins), [IoT Hub](#iot-hub) est considéré comme un service en amont, car les données circulent d’IoT Hub vers Azure [Digital Twins](#digital-twin).

S’applique à : IoT Hub, IoT Central, Digital Twins

