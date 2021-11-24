---
title: Vue d’ensemble de la gestion des appareils avec Microsoft Azure IoT Hub
description: 'Vue d’ensemble de la gestion des appareils dans Azure IoT Hub : cycle de vie des appareils d’entreprise et modèles de gestion des appareils, tels que redémarrage, réinitialisation des paramètres d’usine, mise à jour du microprogramme, configuration, jumeaux d’appareil, requêtes, travaux détection de la menace.'
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 7e21115bd1f55d79b1ba09b91cea05576a74dd75
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312531"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Vue d’ensemble de la gestion des appareils avec IoT Hub

Azure IoT Hub fournit les fonctionnalités et un modèle d’extensibilité qui permettent aux développeurs d’appareils et de serveur principal de créer des solutions robustes de gestion des appareils. Les appareils englobent les capteurs de contraintes et les microcontrôleurs à un seul objectif, les passerelles puissantes qui acheminent les communications pour les groupes d’appareils.  Par ailleurs, les cas d’usage et les besoins des opérateurs IoT varient considérablement entre les différents secteurs d’activité.  Malgré cette variation, la gestion des appareils avec IoT Hub fournit les fonctionnalités, les modèles et les bibliothèques de code pour répondre aux besoins d’un ensemble varié d’appareils et d’utilisateurs finaux.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Une composante essentielle de la création d’une solution IoT d’entreprise efficace consiste à fournir une stratégie portant sur la manière dont les opérateurs assurent la gestion continue de leur collection d’appareils. Les opérateurs IoT ont besoin d’outils et d’applications à la fois simples et fiables qui leur permettent de se concentrer sur les aspects plus stratégiques de leur travail. Cet article fournit :

* Une brève vue d’ensemble de l’approche de gestion des appareils Azure IoT Hub.
* Une description des principes courants de gestion des appareils.
* Une description du cycle de vie de l’appareil.
* Une vue d’ensemble des modèles courants de gestion des appareils.

## <a name="device-lifecycle"></a>Cycle de vie de l’appareil

Les étapes générales de la gestion des appareils sont courantes dans la plupart des projets IoT d’entreprise. Dans Azure IoT, il existe cinq phases dans le cycle de vie des appareils :

![Les cinq phases du cycle de vie des appareils Azure IoT : planifier, approvisionner, configurer, surveiller, mettre hors service](./media/iot-hub-device-management-overview/image5.png)

Dans chacune de ces cinq étapes, il existe plusieurs conditions destinées à l’opérateur des appareils qui doivent être respectées pour fournir une solution complète :

* **Planification** : permettre aux opérateurs de créer un schéma de métadonnées d’appareils afin qu’ils puissent facilement et précisément rechercher et cibler un groupe d’appareils pour les opérations de gestion en bloc. Vous pouvez utiliser la représentation physique d’appareil pour stocker les métadonnées de cet appareil sous la forme d’étiquettes et de propriétés.
  
    *Pour aller plus loin* : 
  * [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md)
  * [Comprendre les représentations d’appareils](iot-hub-devguide-device-twins.md)
  * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
  * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Approvisionnement** : approvisionner les nouveaux appareils en toute sécurité auprès d’IoT Hub et permettre aux opérateurs de détecter immédiatement les fonctionnalités des appareils.  Utilisez le registre des identités IoT Hub pour créer des informations d’identification et des identités d’appareils flexibles et effectuez cette opération en bloc à l’aide d’un travail. Créez les appareils afin qu’ils signalent leurs fonctionnalités et leur condition via les propriétés de l’appareil dans la représentation d’appareil.
  
    *Pour aller plus loin* : 
    * [Gérer les identités des appareils](iot-hub-devguide-identity-registry.md)
    * [Gestion en bloc des identités d’appareils](iot-hub-bulk-identity-mgmt.md)
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)
    * [Service Azure IoT Hub Device Provisioning](../iot-dps/index.yml)

* **Configurer** : rendre possibles les modifications de configuration et les mises à jour de microprogramme en bloc sur les appareils, tout en assurant intégrité et sécurité. Effectuez ces opérations de gestion d’appareils en bloc à l’aide des propriétés requises ou de méthodes directes et de travaux de diffusion.
  
    *Pour aller plus loin* :
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Configurer et surveiller des appareils IoT à grande échelle](./iot-hub-automatic-device-management.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Surveillance** : surveiller l’intégrité globale de la collection d’appareils, l’état des opérations en cours et avertir les opérateurs des problèmes susceptibles de nécessiter leur attention.  Appliquez les jumeaux d’appareil pour autoriser les appareils à signaler des conditions de fonctionnement en temps réel et l’état des opérations de mise à jour. Créez des rapports de tableau de bord puissants qui font remonter les problèmes les plus immédiats à l’aide de requêtes sur la représentation physique d’appareil. Protégez votre environnement IoT contre les menaces, avec plusieurs options de déploiement : local, connecté au Cloud ou hybride.
  
    *Pour aller plus loin* : 
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages](iot-hub-devguide-query-language.md)
    * [Configurer et surveiller des appareils IoT à grande échelle](./iot-hub-automatic-device-management.md)
    * [Azure Defender pour IoT pour les organisations afin de fournir une détection complète des menaces](../defender-for-iot/organizations/overview.md)
    * [Bonnes pratiques en matière de configuration d’appareil dans une solution IoT](iot-hub-configuration-best-practices.md)

* **Mise hors service** : remplacer ou retirer des appareils après une défaillance, un cycle de mise à niveau ou à la fin de leur durée de vie.  Utilisez la représentation d’appareil pour conserver les informations sur l’appareil si l’appareil physique est remplacé ou archivé en cas de mise hors service. Utilisez le registre des identités IoT Hub pour la révocation en toute sécurité des informations d’identification et des identités des appareils.
  
    *Pour aller plus loin* : 
    * [Guide pratique pour utiliser des propriétés de jumeau d’appareil](tutorial-device-twins.md)
    * [Gérer les identités des appareils](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Modèle de gestion des appareils

IoT Hub permet de mettre en œuvre l’ensemble suivant de modèles de gestion des appareils. Les [didacticiels de gestion des appareils](iot-hub-node-node-device-management-get-started.md) vous montrent plus en détail comment étendre ces modèles pour les adapter à votre situation et comment concevoir de nouveaux modèles basés sur ces modèles de base.

* **Redémarrage**: l’application principale informe l’appareil via une méthode directe qu’il a initié un redémarrage.  L’appareil utilise les propriétés signalées pour mettre à jour l’état de redémarrage de l’appareil.
  
    ![Graphique du modèle de redémarrage de la gestion des appareils](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Réinitialisation des paramètres d’usine**: l’application principale informe l’appareil via une méthode directe qu’il a initié une réinitialisation des paramètres d’usine. L’appareil utilise les propriétés signalées pour mettre à jour l’état de réinitialisation aux paramètres d’usine de l’appareil.
  
    ![Graphique du modèle de réinitialisation aux paramètres d’usine de la gestion des appareils](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configuration** : l’application principale utilise les propriétés souhaitées pour configurer le logiciel en cours d’exécution sur l’appareil. L’appareil utilise les propriétés signalées pour mettre à jour l’état de configuration de l’appareil.
  
    ![Graphique du modèle de configuration de la gestion des appareils](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Signalement de la progression et de l’état** : le serveur principal de solution exécute des requêtes de représentation d’appareil sur un ensemble d’appareils afin de rendre compte de l’état et de la progression des actions en cours d’exécution sur les appareils.
  
    ![Graphique du modèle de signalement de la progression et de l’état de la gestion des appareils](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>Mises à jour de l’appareil

La [Mise à jour de l’appareil pour IoT Hub](../iot-hub-device-update/understand-device-update.md) est une plateforme complète que les clients peuvent utiliser pour publier, distribuer et gérer des mises à jour par voie hertzienne pour tout, des petits capteurs aux appareils de niveau passerelle. La Mise à jour de l’appareil pour IoT Hub permet aux clients de répondre rapidement aux menaces de sécurité et de déployer des fonctionnalités pour répondre aux objectifs commerciaux sans entraîner de coûts supplémentaires de développement et de maintenance pour la construction de plateformes de mise à jour personnalisées.

La Mise à jour de l’appareil pour IoT Hub offre un déploiement de mises à jour optimisé et des opérations rationalisées grâce à l’intégration avec Azure IoT Hub. Avec la portée étendue via Azure IoT Edge, il fournit une solution hébergée dans le cloud qui connecte virtuellement tous les appareils. Il prend en charge une large gamme de systèmes d’exploitation IoT, notamment Linux et Azure RTOS (système d’exploitation en temps réel). Celle-ci peut s’étendre par le biais de l’open source. Cette API offre les fonctionnalités suivantes :

* Prise en charge de la mise à jour des appareils de périphérie, y compris les composants de niveau hôte d’Azure IoT edge
* Expérience utilisateur de gestion des mises à jour intégrée à Azure IoT Hub
* Lancement progressif des mises à jour par le biais de groupes d’appareils et de contrôles de planification des mises à jour
* API programmatiques pour activer des expériences d’automatisation et de personnalisation de portail
* Vues en un clin d’œil de la conformité et de l’état des mises à jour sur des flottes d’appareils hétérogènes
* Prise en charge des mises à jour d’appareil résilientes (A/B) pour assurer une restauration continue
* Mise en cache du contenu et prise en charge déconnectée des appareils, y compris les appareils qui se trouvent dans des configurations imbriquées, via le Cache connecté Microsoft intégré et l’intégration avec Azure IoT Edge
* Contrôles d’accès basés sur les rôles et les abonnements disponibles par le biais du portail Azure.com
* Fonctionnalités de sécurité cloud-to-edge complètes et contrôles de la confidentialité

Pour plus d’informations, consultez [Mise à jour de l’appareil pour IOT Hub](../iot-hub-device-update/index.yml).

## <a name="next-steps"></a>Étapes suivantes

Les fonctionnalités, les modèles et les bibliothèques de code fournis par IoT Hub pour la gestion des appareils vous permettent de créer des applications IoT qui respectent les exigences d’opérateur IoT en entreprise dans chaque phase du cycle de vie de l’appareil.

Pour plus d’informations sur les fonctionnalités de gestion des appareils dans IoT Hub, consultez le didacticiel [Prise en main de la gestion des appareils](iot-hub-node-node-device-management-get-started.md).
