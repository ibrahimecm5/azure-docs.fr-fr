---
title: Azure Database pour MySQL - Serveur flexible - Maintenance planifiée - Portail Azure
description: Découvrez comment configurer les paramètres de maintenance planifiée pour Azure Database pour MySQL – Serveur flexible à partir du portail Azure.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: bf6eb62e75a378db8fae70f2317ccd4f072a7f75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460642"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Gérer les paramètres de maintenance planifiée dans Azure Database pour MySQL – Serveur flexible

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Vous pouvez spécifier des options de maintenance pour chaque serveur flexible dans votre abonnement Azure. Les options incluent la planification de maintenance et les paramètres de notification pour les événements de maintenance à venir et terminés.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

- Un [Azure Database pour MySQL – Serveur flexible](quickstart-create-server-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Spécifier les options de planification de la maintenance

1. Dans la page du serveur MySQL, sous le titre **Paramètres**, choisissez **Maintenance** pour ouvrir les options de maintenance planifiée.
2. La planification par défaut (gérée par le système) est un jour aléatoire de la semaine et une fenêtre de 60 minutes pour le début la maintenance entre 23 h et 7 H, heure du serveur local. Si vous souhaitez personnaliser cette planification, choisissez **Planification personnalisée**. Vous pouvez ensuite sélectionner un jour par défaut de la semaine et une fenêtre de 60 minutes pour l’heure de début de la maintenance.

## <a name="notifications-about-scheduled-maintenance-events"></a>Notifications relatives aux événements de maintenance planifiée

Vous pouvez utiliser Azure Service Health pour [afficher les notifications](../../service-health/service-notifications.md) sur la maintenance planifiée à venir et effectuée sur votre serveur flexible. Vous pouvez également [configurer](../../service-health/resource-health-alert-monitor-guide.md) des alertes dans Azure Service Health pour obtenir des notifications sur les événements de maintenance.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir la [maintenance planifiée dans Azure Database pour MySQL – Serveur flexible](concepts-maintenance.md)
* En savoir plus sur [Azure Service Health](../../service-health/overview.md)
