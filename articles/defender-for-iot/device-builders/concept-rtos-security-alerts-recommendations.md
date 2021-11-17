---
title: Alertes et suggestions personnalisables intégrées au micro-agent Defender-IoT pour Azure RTOS
description: Découvrez les alertes de sécurité et corrections suggérées en lien avec l’utilisation du micro-agent Defender-IoT Azure IoT pour Azure RTOS.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: ad6cd708bfdb53068895abc72abb3092e82db818
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293551"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Alertes et suggestions de sécurité du micro-agent Defender-IoT pour Azure RTOS (préversion)

Le micro-agent Defender-IoT pour Azure RTOS bénéficie de capacités avancées d’analyse des données et de renseignements sur les menaces. Il les exploite pour surveiller en permanence le bon fonctionnement de votre solution IoT afin de vous avertir s’il détecte une activité potentiellement malveillante ou des modifications suspectes du système. Il vous permet également de créer des alertes personnalisées basées sur vos références et vos connaissances sur le comportement prévu pour vos appareils.

Les alertes du micro-agent Defender-IoT pour Azure RTOS indiquent des compromissions potentielles nécessitant une investigation et une correction. Une suggestion du micro-agent Defender-IoT pour Azure RTOS identifie une posture de sécurité faible pour vous permettre d’y remédier en effectuant les mises à jour nécessaires. 

Cet article dresse une liste des alertes et des suggestions intégrées au module de sécurité et déclenchées en fonction des réglages par défaut. Vous pouvez les personnaliser avec vos propres valeurs, selon vos références ou le comportement prévu pour vos appareils. 

Si vous souhaitez en savoir plus sur la personnalisation des alertes dans le service Azure Defender pour IoT, consultez l’article [Alertes de sécurité personnalisables d’Azure Defender pour IoT](concept-customizable-security-alerts.md). Les alertes et suggestions spécifiques personnalisables en lien avec l’utilisation du micro-agent Defender-IoT pour Azure RTOS sont détaillées dans les tableaux suivants. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Alertes de sécurité prises en charge du micro-agent Defender-IoT pour Azure RTOS

### <a name="device-related-security-alerts"></a>Alertes de sécurité concernant un appareil

|Activité d’alerte de sécurité concernant un appareil  |Nom de l’alerte  |
|---------|---------|
|Adresse IP| Détection d’une communication avec une adresse IP suspecte|
|Empreinte du certificat de l’appareil X.509|Non-concordance de l’empreinte du certificat de l’appareil X.509|
|Certificat X.509| Le certificat X.509 a expiré|
|Jeton SAP| Jeton SAS expiré|
|Jeton SAP| Signature de jeton SAS non valide|

### <a name="iot-hub-related-security-alerts"></a>Alertes de sécurité concernant un hub IoT

|Activité d’alerte de sécurité concernant un hub IoT  |Nom de l’alerte  |
|---------|---------|
|Ajouter un certificat    |  Détection d’une tentative infructueuse d’ajout de certificat à un hub IoT       |
|Ajout ou modification d’un paramètre de diagnostic    | Détection d’une tentative d’ajout ou de modification d’un paramètre de diagnostic d’un hub IoT      |
|Supprimer un certificat    |  Détection d’une tentative infructueuse de suppression d’un certificat d’un hub IoT       |
|Supprimer un paramètre de diagnostic    |  Détection d’une tentative de suppression d’un paramètre de diagnostic d’un hub IoT      |
|Certificat supprimé    | Détection de la suppression d’un certificat d’un hub IoT        |
|Nouveau certificat     |  Détection de l’ajout d’un nouveau certificat à un hub IoT       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Alertes personnalisables prises en charge du micro-agent Defender-IoT pour Azure RTOS

### <a name="device-related-customizable-alerts"></a>Alertes personnalisables concernant un appareil

|Activité concernant un appareil |Nom de l’alerte  |
|---------|---------|
|Connexions actives|Le nombre de connexions actives ne correspond pas aux valeurs autorisées|
|Messages cloud-à-appareil dans le protocole **MQTT**|Le nombre de messages cloud-à-appareil dans le protocole **MQTT** ne correspond pas aux valeurs autorisées|
|Connexion sortante| Une connexion sortante vers une adresse IP qui n’est pas autorisée|

### <a name="hub-related-customizable-alerts"></a>Alertes personnalisables concernant un hub 

|Activité concernant un hub  |Nom de l’alerte  |
|---------|---------|
|Vidages de file d’attente de commandes     |  Le nombre de vidages de file d’attente de commandes ne correspond pas aux valeurs autorisées       |
|Messages cloud-à-appareil dans le protocole **MQTT**    |  Le nombre de messages cloud-à-appareil dans le protocole **MQTT** ne correspond pas aux valeurs autorisées       |
|Messages appareil-à-cloud dans le protocole **MQTT**    | Le nombre de messages appareil-à-cloud dans le protocole **MQTT** ne correspond pas aux valeurs autorisées        |
|Appel de méthode directe     |  Le nombre d’appels de méthode directe ne correspond pas aux valeurs autorisées       |
|Messages cloud-à-appareil rejetés dans le protocole **MQTT**     |   Le nombre de messages cloud-à-appareil rejetés dans le protocole **MQTT** ne correspond pas aux valeurs autorisées      |
|Mises à jour des modules jumeaux     |  Le nombre de mises à jour des modules jumeaux ne correspond pas aux valeurs autorisées       |
|Opérations non autorisées    |  Le nombre d’opérations non autorisées ne correspond pas aux valeurs autorisées       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Suggestions prises en charge du micro-agent Defender-IoT pour Azure RTOS

### <a name="device-related-recommendations"></a>Suggestions concernant un appareil

|Activité concernant un appareil  |Nom de la recommandation |
|---------|---------|
|Informations d'authentification    |  Informations d’authentification identiques utilisées par plusieurs appareils       |

### <a name="hub-related-recommendations"></a>Suggestions concernant un hub

|Activité concernant un hub IoT  |Nom de la recommandation |
|---------|---------|
|Stratégie de filtre IP   |  La stratégie de filtre IP par défaut doit être définie sur **deny** (refuser)  |
|Règle de filtre IP| La règle de filtre IP inclut une grande plage d’adresses IP|
|Journaux de diagnostics|Suggestion d’activation des journaux de diagnostics dans un hub IoT|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>Toutes les alertes et suggestions de Defender pour IoT

Pour obtenir la liste des alertes et suggestions du service Defender pour IoT, consultez les pages [Alertes de sécurité de Defender pour IoT](concept-security-alerts.md) et [Suggestions de sécurité](concept-recommendations.md).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Micro-agent Defender IoT pour Azure RTOS](quickstart-azure-rtos-security-module.md)
- [Configurer et personnaliser le micro-agent Defender-IoT pour Azure RTOS](how-to-azure-rtos-security-module.md)
- Consulter [API du micro-agent Defender-IoT pour Azure RTOS](azure-rtos-security-module-api.md)
