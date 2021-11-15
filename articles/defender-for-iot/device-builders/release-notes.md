---
title: Nouveautés d’Azure Defender pour IoT pour les fabricants d’appareils
description: Découvrez les versions et fonctionnalités les plus récentes de Defender pour IoT pour les fabricants d’appareils.
ms.topic: overview
ms.date: 11/02/2021
ms.openlocfilehash: 4d6d931082f55a08667da20283d28c655a9fb584
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438585"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>Nouveautés d’Azure Defender pour IoT pour les fabricants d’appareils  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

Cet article répertorie les nouvelles fonctionnalités et améliorations apportées à Defender pour IoT.

Ces fonctionnalités sont en PRÉVERSION. Les [conditions supplémentaires pour les préversions Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent d’autres conditions juridiques qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou qui ne sont pas encore en disponibilité générale.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Versioning et support d’Azure Defender pour IoT

La liste ci-dessous inclut la prise en charge, les stratégies avec modification cassante pour Defender pour IoT et les versions d’Azure Defender pour IoT actuellement disponibles.

## <a name="september-2021"></a>Septembre 2021

**Version 3.11** :

- **[Collecteur de connexions](concept-event-aggregation.md#login-collector-event-based-collector)**  : Les collecteurs de connexions rassemblent les connexions, les déconnexions et les échecs de tentatives de connexion des utilisateurs. Par exemple, SSH et Telnet.

- **[Collecteur d’informations système](concept-event-aggregation.md#system-information-trigger-based-collector)**  : Le collecteur d’informations système rassemble les informations relatives au système d’exploitation et au matériel de l’appareil.

- **[Agrégation d’événements](concept-event-aggregation.md#how-does-event-aggregation-work)**  : L’agent Defender pour IoT agrège les événements tels que les processus, les connexions, les événements réseau qui réduisent le nombre de messages envoyés et les coûts, tout en préservant la sécurité de votre appareil.  

- **[Configuration de jumeau](concept-micro-agent-configuration.md)**  : Le comportement du micro-agent est configuré par un ensemble de propriétés du jumeau de module. (Par exemple, la fréquence d’envoi des événements et le mode d’agrégation.) Vous pouvez configurer le micro-agent en fonction de vos besoins.

## <a name="march-2021"></a>Mars 2021

### <a name="device-builder---new-micro-agent-public-preview"></a>Générateur d’appareils - Nouveau micro-agent (préversion publique)

Un nouveau module Générateur d’appareils est disponible. Le module, désigné sous le terme de micro-agent, offre les éléments suivants :

- **Intégration à Azure IoT Hub et Azure Defender pour IoT** : renforcez la sécurité des points de terminaison directement sur vos appareils IoT en l’intégrant à l’option d’analyse fournie par Azure IoT Hub et Azure Defender pour IoT.

- **Options de déploiement flexibles avec prise en charge des systèmes d’exploitation IoT standard** : possibilité de déploiement en tant que package binaire ou en tant que code source modifiable, avec prise en charge des systèmes d’exploitation IoT standard, tels que Linux et Azure RTOS.

- **Ressources minimales requises sans dépendances du noyau du système d’exploitation** : faible encombrement, faible consommation du processeur et aucune dépendance du noyau du système d’exploitation.

- **Gestion de la posture de sécurité** : surveillez de manière proactive la posture de sécurité de vos appareils IoT.

- **Détection continue des menaces IoT/OT en temps réel** : détectez les menaces, telles que les botnets, les tentatives d’attaque par force brute, les cryptomineurs et l’activité réseau suspecte

La documentation du micro-agent Defender-IoT déconseillé sera déplacée dans le dossier *Solution basée sur les agents pour les générateurs d’appareils > Classique*.

Cet ensemble de fonctionnalités est disponible avec la version cloud de la préversion publique actuelle.

## <a name="next-steps"></a>Étapes suivantes

[Présentation de la solution basée sur un agent pour les fabricants d'appareils](architecture-agent-based.md)
