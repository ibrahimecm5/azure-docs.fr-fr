---
title: Exemples Azure CLI - Azure Database pour MySQL - Serveur flexible
description: Cet article liste les exemples de code Azure CLI qui sont disponibles pour interagir avec Azure Database pour MySQL - Serveur flexible.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 8ae33838b4dff052006f1124eea3dbec59cc7666
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844266"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server"></a>Exemples Azure CLI pour Azure Database pour MySQL - Serveur flexible

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Le tableau suivant comprend des liens vers des exemples de scripts Azure CLI pour Azure Database pour MySQL - Serveur flexible.

| Exemple de lien | Description  |
|---|---|
|**Créer un serveur et s’y connecter**||
| [Créer un serveur et activer la connectivité d’accès public](scripts/sample-cli-create-connect-public-access.md) | Crée une instance d’Azure Database pour MySQL - Serveur flexible, configure une règle de pare-feu au niveau du serveur (méthode de connectivité d’accès public) et se connecte au serveur. |
| [Créer un serveur et activer la connectivité d’accès privé (intégration VNet)](scripts/sample-cli-create-connect-private-access.md) | Crée une instance d’Azure Database pour MySQL - Serveur flexible dans un réseau virtuel (méthode de connectivité d’accès privé) et se connecte au serveur via une machine virtuelle appartenant au réseau virtuel. |
|**Superviser et mettre à l’échelle**||
| [Superviser les métriques et mettre à l’échelle un serveur](scripts/sample-cli-monitor-and-scale.md) | Supervise et met à l’échelle une instance Azure Database pour MySQL - Serveur flexible (scale-up ou scale-down) de manière à accommoder les changements de besoins en performances. |
|**Sauvegarde et restauration**||
| [Restaurer un serveur](scripts/sample-cli-restore-server.md) | Restaure une instance d’Azure Database pour MySQL - Serveur flexible à un point antérieur dans le temps. |
|**Haute disponibilité**||
| [Configurer la haute disponibilité redondante interzone](scripts/sample-cli-zone-redundant-ha.md) | Active la haute disponibilité redondante interzone pendant la création d’une instance Azure Database pour MySQL - Serveur flexible.|
| [Configurer la haute disponibilité dans la même zone](scripts/sample-cli-same-zone-ha.md) | Active la haute disponibilité dans la même zone pendant la création d’une instance d’Azure Database pour MySQL - Serveur flexible.|
|**Gérer le serveur**||
| [Redémarrer, arrêter et démarrer un serveur](scripts/sample-cli-restart-stop-start.md)| Effectue des opérations de redémarrage, d’arrêt et de démarrage sur une instance d’Azure Database pour MySQL - Serveur flexible. |
| [Modifier les paramètres du serveur](scripts/sample-cli-change-server-parameters.md) | Modifie les paramètres de serveur d’une instance d’Azure Database pour MySQL - Serveur flexible. |
|**Réplication**||
| [Créer des réplicas en lecture](scripts/sample-cli-read-replicas.md) | Crée et gère les réplicas en lecture dans une instance d’Azure Database pour MySQL - Serveur flexible. |
|**Configurer les journaux**||
| [Configurer les journaux d’audit](scripts/sample-cli-audit-logs.md) | Configure les journaux d’audit dans une instance d’Azure Database pour MySQL - Serveur flexible. |
| [Configurer des journaux de requêtes lentes](scripts/sample-cli-slow-query-logs.md) | Configure les journaux de requêtes lentes dans une instance d’Azure Database pour MySQL - Serveur flexible. |

