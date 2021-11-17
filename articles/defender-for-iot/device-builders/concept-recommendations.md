---
title: Recommandations de sécurité pour IoT Hub
description: Découvrez le concept des recommandations de sécurité et comment elles sont utilisées dans Defender pour IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 5a4d93c30a01cc0fa93cbb3e4835bf8a86ccc622
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331582"
---
# <a name="security-recommendations-for-iot-hub"></a>Recommandations de sécurité pour IoT Hub

Defender pour IoT analyse vos ressources Azure et appareils IoT, et fournit des suggestions de sécurité visant à réduire votre surface d’attaque.
Les recommandations de sécurité sont actionnables et visent à aider les clients à se conformer aux bonnes pratiques de sécurité.

Vous trouverez dans cet article une liste de recommandations qui peuvent être déclenchées sur votre hub IoT.

## <a name="built-in-recommendations-in-iot-hub"></a>Recommandations intégrées dans IoT Hub

Les alertes de recommandation offrent des insights et des suggestions d’actions visant à améliorer la posture de sécurité de l’environnement.

### <a name="high-severity"></a>Niveau de gravité Élevé

| severity | Name | source de données | Description | RecommendationType |
|--|--|--|--|--|
| Élevé | Informations d’identification d’authentification identiques utilisées par plusieurs appareils | IoT Hub | Des informations d’authentification IoT Hub sont utilisées par plusieurs appareils, Cela peut indiquer qu’un appareil illégitime emprunte l’identité d’un appareil légitime et expose également le risque d’emprunt d’identité d’appareil par un acteur malveillant. | IoT_SharedCredentials |
| Élevé | Autorisations de haut niveau configurées dans le jumeau de modèle IoT Edge pour le module IoT Edge | IoT Hub | Le module IoT Edge est configuré pour s’exécuter en mode privilégié, avec des fonctionnalités Linux étendues ou avec un accès réseau au niveau de l’hôte (envoyer/recevoir des données à l’ordinateur hôte). | IoT_PrivilegedDockerOptions |

### <a name="medium-severity"></a>Niveau de gravité Moyen

| severity | Name | source de données | Description | RecommendationType |
|--|--|--|--|--|
| Moyenne | Principal de service non utilisé avec le référentiel ACR | IoT Hub | Le schéma d’authentification utilisé pour extraire un module IoT Edge à partir d’un référentiel ACR n’utilise pas l’authentification du principal de service. | IoT_ACRAuthentication |
| Moyenne | Mise à niveau de la suite de chiffrement TLS requise | IoT Hub | Configurations TLS non sécurisées détectées. Mise à niveau immédiate de la suite de chiffrement TLS recommandée. | IoT_VulnerableTLSCipherSuite |
| Moyenne | La stratégie de filtre IP par défaut devrait être de refuser | IoT Hub | La configuration du filtre IP doit avoir des règles définies pour le trafic autorisé et refuser tout autre trafic par défaut. | IoT_IPFilter_DenyAll |
| Moyenne | La règle de filtre IP inclut une grande plage d’adresses IP | IoT Hub | La plage d’adresses IP source d’une des règles de filtre IP autorisable est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes. | IoT_IPFilter_PermissiveRule |
| Moyenne | Règles recommandées pour le filtre IP | IoT Hub | Nous vous recommandons de changer votre filtre IP en fonction des règles suivantes, obtenues par le comportement de votre IotHub | IoT_RecommendedIpRulesByBaseLine |
| Moyenne | SecurityGroup a des paramètres de module incohérents | IoT Hub | Dans ce groupe de sécurité d’appareils, un appareil anormal a des paramètres de module IoT Edge incohérents par rapport au reste du groupe de sécurité. | IoT_InconsistentModuleSettings |

### <a name="low-severity"></a>Gravité faible

| severity | Name | source de données | Description | RecommendationType |
|--|--|--|--|--|
| Faible | La mémoire du Hub IoT Edge peut être optimisée | IoT Hub | Optimisez l’utilisation de la mémoire de votre IoT Edge Hub en désactivant les en-têtes de protocole pour tous les protocoles qui ne sont pas utilisés par les modules Edge dans votre solution. | IoT_EdgeHubMemOptimize |
| Faible | Aucune journalisation configurée pour le module IoT Edge | IoT Hub | La journalisation est désactivée pour ce module de IoT Edge. | IoT_EdgeLoggingOptions |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [alertes de sécurité des appareils Defender pour IoT classiques](agent-based-security-alerts.md)
