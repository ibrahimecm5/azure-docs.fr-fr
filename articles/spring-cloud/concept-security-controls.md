---
title: Contrôles de sécurité pour le service Azure Spring Cloud
description: Utilisez les contrôles de sécurité intégrés dans le service Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: f980c67e22ca031617e9614d2e8facd2e9a9dafa
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485694"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Contrôles de sécurité pour le service Azure Spring Cloud

**Cet article s'applique à :** ✔️ Java ✔️ C#

Des contrôles de sécurité sont intégrés dans le service Azure Spring Cloud.

Un contrôle de sécurité est une qualité ou une caractéristique d’un service Azure qui lui permet de prévenir, détecter et répondre aux failles de sécurité.  Pour chaque contrôle, nous utilisons *Oui* ou *Non* pour indiquer s’il est actuellement en place pour le service.  Nous utilisons *N/A* pour un contrôle qui n’est pas applicable au service.

**Contrôles de sécurité de la protection des données**

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | La source et les artefacts chargés par l’utilisateur, les paramètres du serveur de configuration, les paramètres de l’application et les données dans le stockage persistant sont stockés dans Stockage Azure qui chiffre automatiquement le contenu au repos.<br><br>Le cache du serveur de configuration, les fichiers binaires du runtime générés à partir de la source téléchargée et les journaux des applications consignés pendant la durée de vie de l'application sont enregistrés sur un disque managé Azure qui chiffre automatiquement le contenu au repos.<br><br>Les images conteneur générées à partir de la source chargée par l’utilisateur sont enregistrées dans Azure Container Registry qui chiffre automatiquement le contenu de l’image au repos. | [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md)<br><br>[Chiffrement côté serveur de disques managés Azure](../virtual-machines/disk-encryption.md)<br><br>[Stockage des images conteneur dans Azure Container Registry](../container-registry/container-registry-storage.md) |
| Chiffrement en transit | Oui | Les points de terminaison publics de l’application utilisateur utilisent le protocole HTTPs pour le trafic entrant par défaut. |  |
| Appels d’API chiffrés | Oui | Les appels de gestion pour configurer le service Azure Spring Cloud se font via des appels Azure Resource Manager sur HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Contrôles de sécurité d’accès réseau**

| Contrôle de sécurité | Oui/Non | Notes | Documentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Étiquette du service | Oui | Utilisez l’étiquette de service **AzureSpringCloud** pour définir les contrôles d’accès réseau sortant sur des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules) ou [Pare-feu Azure](../firewall/service-tags.md) pour autoriser le trafic vers les applications dans Azure Spring Cloud. | [Balises de service](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Déployer votre première application Spring Boot dans Azure Spring Cloud](./quickstart.md)
