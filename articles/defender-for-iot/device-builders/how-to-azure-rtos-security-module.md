---
title: Configurer et personnaliser le micro-agent Defender-IoT pour Azure RTOS
description: Découvrez comment configurer et personnaliser le micro-agent Defender-IoT pour Azure RTOS.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: dc6e522e1d9453264d92100c6e7e830506d8284b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306280"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos"></a>Configurer et personnaliser le micro-agent Defender-IoT pour Azure RTOS

Cet article explique comment configurer le micro-agent Defender pour IoT pour votre appareil Azure RTOS afin de répondre à vos besoins en réseau, bande passante et mémoire.

Vous devez sélectionner un fichier de distribution cible qui a une extension `*.dist`, à partir du répertoire `netxduo/addons/azure_iot/azure_iot_security_module/configs`.  

Lorsque vous utilisez un environnement de compilation CMake, vous devez définir un paramètre de ligne de commande sur `IOT_SECURITY_MODULE_DIST_TARGET` pour la valeur choisie. Par exemple : `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

Dans un IAR ou un autre environnement de compilation différent de CMake, vous devez ajouter le chemin `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` à tous les chemins inclus connus. Par exemple : `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Utilisez le fichier suivant pour configurer le comportement de votre appareil.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h**

Dans un environnement de compilation CMake, vous devez changer la configuration par défaut en modifiant le fichier `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist`. Utilisez le format CMake `set(ASC_XXX ON)` ou le fichier `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` pour tous les autres environnements. Par exemple : `#define ASC_XXX`.

Le comportement par défaut de chaque configuration est fourni dans les tableaux suivants : 

## <a name="general"></a>Général

| Nom | Type | Default | Détails |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Chaîne | defender-iot-micro-agent | Identificateur unique de l’appareil.  |
| SECURITY_MODULE_VERSION_(MAJEURE)(MINEURE)(PATCH)  | Nombre | 3.2.1 | Version. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Nombre  | 3 | Durée nécessaire au micro-agent Defender pour IoT pour envoyer le message de sécurité après un échec. (en secondes) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Numéro | 300 | Durée d’attente du micro-agent Defender pour IoT (en secondes). Si le délai est dépassé, l’état passe à Interrompu. |

## <a name="collection"></a>Collection

| Nom | Type | Default | Détails |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Numéro  | 30  | Décalage de l’intervalle de collecte au démarrage du collecteur. Lors du démarrage, la valeur sera ajoutée à la collecte du système afin d’éviter d’envoyer simultanément des messages à partir de plusieurs appareils.  |
| ASC_HIGH_PRIORITY_INTERVAL | Numéro | 10 | Intervalle du groupe à haute priorité du collecteur (en secondes). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Numéro | 30 | Intervalle du groupe à priorité moyenne du collecteur (en secondes). |
| ASC_LOW_PRIORITY_INTERVAL | Numéro | 145 440  | Intervalle du groupe à faible priorité du collecteur (en secondes). |

#### <a name="collector-network-activity"></a>Activité du réseau de collecteurs

Pour personnaliser la configuration de l’activité du réseau de collecteurs, utilisez ce qui suit :

| Nom | Type | Default | Détails |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtre l’activité de réseau `TCP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtre les événements de l’activité de réseau `UDP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtre les événements de l’activité de réseau `ICMP`. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Capture uniquement les paquets entrants en monodiffusion. Quand la valeur est false, capture également la diffusion et la multidiffusion. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | Envoie des événements vides du collecteur. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Numéro | 64 | Nombre maximal d’événements réseau IPv4 à stocker en mémoire. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Numéro | 64  | Nombre maximal d’événements réseau IPv6 à stocker en mémoire. |

### <a name="collectors"></a>Collecteurs
| Nom | Type | Default | Détails |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ACTIVÉ | Active le collecteur de pulsations. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ACTIVÉ | Active le collecteur d’activité réseau. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ACTIVÉ | Active le collecteur d’informations système.  |

Les indicateurs d’autres configurations sont avancés et présentent des fonctionnalités non prises en charge. Pour apporter des modifications ou pour plus d’informations, contactez le support technique.
 
## <a name="supported-security-alerts-and-recommendations"></a>Alertes et recommandations de sécurité prises en charge

Le micro-agent Defender pour IoT pour Azure RTOS prend en charge des alertes de sécurité et des recommandations spécifiques. Veillez à [passer en revue et à personnaliser les valeurs d’alerte et de recommandation pertinentes](concept-rtos-security-alerts-recommendations.md) pour votre service.

## <a name="log-analytics-optional"></a>Analytique des journaux d'activité (facultatif)

Vous pouvez activer et configurer Log Analytics pour investiguer les événements et les activités des appareils. Découvrez comment effectuer la configuration et consultez [Log Analytics avec le service Defender pour IoT](how-to-security-data-access.md#log-analytics) pour en savoir plus. 

## <a name="next-steps"></a>Étapes suivantes


- Évaluer et personnaliser les [alertes de sécurité et les recommandations](concept-rtos-security-alerts-recommendations.md) du micro-agent Defender-IoT pout Azure RTOS
- Consulter [API du micro-agent Defender-IoT pour Azure RTOS](azure-rtos-security-module-api.md) si nécessaire.
