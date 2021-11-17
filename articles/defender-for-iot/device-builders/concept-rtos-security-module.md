---
title: Explication conceptuelle des fondements du micro-agent Defender-IoT pour Azure RTOS
description: Découvrez les fondements des concepts et du flux de travail du micro-agent Defender-IoT pour Azure RTOS.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 084c7130c9fe033fb96fcb0a2e3c2820ef2c77e5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278946"
---
# <a name="defender-iot-micro-agent-for-azure-rtos"></a>Micro-agent Defender-IoT pour Azure RTOS

Cet article vous aide à mieux comprendre le micro-agent Defender-IoT pour Azure RTOS, notamment ses fonctionnalités et ses avantages, et propose des liens vers des ressources de configuration et de référence pertinentes. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Micro-agent Defender-IoT pour Azure RTOS

Le micro-agent Defender-IoT pour Azure RTOS offre une solution de sécurité complète pour les appareils Azure RTOS dans le cadre de l’offre NetX Duo. Dans l’offre NetX Duo, Azure RTOS comprend le micro-agent Defender-IoT Azure Iot intégré. Une fois activé, celui-ci fournit une couverture contre les menaces courantes sur vos appareils avec système d’exploitation en temps réel (RTOS).

Le micro-agent Defender-IoT pour Azure RTOS s’exécute en arrière-plan et offre une expérience utilisateur fluide, tout en envoyant des messages de sécurité à l’aide des connexions uniques de chaque client à son IoT Hub. Le micro-agent Defender-IoT pour Azure RTOS est activé par défaut.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo est une pile réseau TCP/IP avancée de niveau professionnel, conçue spécifiquement pour les applications IoT et en temps réel profondément intégrées. Azure RTOS NetX Duo est une pile réseau double IPv4 et IPv6 offrant un ensemble complet de protocoles, notamment la sécurité et le cloud. En savoir plus sur les solutions de [NetX Duo d’Azure RTOS](/azure/rtos/netx-duo/).

Le module offre les fonctionnalités suivantes :

- **Détection des activités malveillantes sur les réseaux**
- **Établissement d’une ligne de base du comportement de l’appareil basé sur des alertes personnalisées**
- **Amélioration de l’hygiène de sécurité des appareils**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Micro-agent Defender-IoT pour l’architecture Azure RTOS

Le micro-agent Defender-IoT pour Azure RTOS est initialisé par la plateforme intergicielle Azure IoT, et utilise des clients IoT Hub pour envoyer une télémétrie de sécurité au hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagramme d’état et flux d’informations du micro-agent Defender-IoT Azure IoT":::

Le micro-agent Defender-IoT pour Azure RTOS surveille l’activité et les informations suivantes des appareils à l’aide de trois collecteurs :
- Activité réseau **TCP**, **UDP** et **ICM** de l’appareil
- Informations système telles que les versions **ThreadX** et **NetX Duo**
- Événements de pulsation

Chaque collecteur est lié à un groupe de priorité et chaque groupe de priorité a son propre intervalle avec comme valeurs possibles **faible**, **moyenne** et **haute**. Les intervalles affectent l’intervalle de temps pendant lequel les données sont collectées et envoyées.

Chaque intervalle de temps est configurable et les connecteurs IoT peuvent être activés et désactivés afin de [personnaliser votre solution](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Alertes et suggestions de sécurité prises en charge

Le micro-agent Defender pour IoT pour Azure RTOS prend en charge des alertes de sécurité et des suggestions spécifiques. Veillez à [passer en revue et à personnaliser les valeurs d’alerte et de suggestion pertinentes](concept-rtos-security-alerts-recommendations.md) pour votre service une fois la configuration initiale effectuée.

## <a name="ready-to-begin"></a>Vous êtes prêt à commencer ?

Le micro-agent Defender-IoT pour Azure RTOS est fourni en téléchargement gratuit pour vos appareils IoT. Le service Defender pour IoT est disponible avec un essai gratuit de 30 jours par abonnement Azure. [Téléchargez le micro-agent Defender-IoT maintenant](https://github.com/azure-rtos/azure-iot-preview/releases) et commençons. 

## <a name="next-steps"></a>Étapes suivantes

- Prise en main du micro-agent Defender-IoT pour Azure RTOS [conditions préalables et configuration](quickstart-azure-rtos-security-module.md).
- En savoir plus sur le micro-agent Defender-IoT pour Azure RTOS [alertes de sécurité et support de suggestions](concept-rtos-security-alerts-recommendations.md). 
- Utiliser l’[API de référence](azure-rtos-security-module-api.md) du micro-agent Defender-IoT pour Azure RTOS.
