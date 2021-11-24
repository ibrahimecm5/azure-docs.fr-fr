---
title: Métriques et supervision Azure HPC Cache
description: Comment voir les statistiques relatives à votre cache Azure HPC
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: femila
ms.openlocfilehash: cc6846118607e2d6bc50d63035f1ce209758f55c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331278"
---
# <a name="cache-metrics-and-monitoring"></a>Mesures et supervision du cache

Le portail Azure dispose de plusieurs visualisations intégrées que vous pouvez utiliser pour superviser les performances de votre cache Azure HPC.

Cet article explique où trouver les visualisations et ce qu’elles montrent.

Quatre pages du portail HPC Cache montrent les données de performances du cache :

* **Vue d'ensemble**
* **Métriques**
* **Rapport du cache**
* **État du client**

Ouvrez les pages **Métriques**, **Rapport du cache** et **État du client** à partir du menu **Supervision** situé dans la barre latérale. La page **Vue d’ensemble** du cache est le premier élément du menu de la barre latérale.

![Capture d’écran du menu Supervision situé à gauche de l’interface du portail Azure pour HPC Cache.](media/monitoring-menu.png)

## <a name="overview-page"></a>Page Vue d’ensemble

La page **Vue d’ensemble** affiche des graphes correspondant aux statistiques de cache de base : débit du cache, opérations par seconde et latence. Les graphes se trouvent au bas de la page, sous la section **Éléments principaux**.

![capture d’écran de trois graphiques linéaires montrant les statistiques mentionnées ci-dessus pour un exemple de cache](media/hpc-cache-overview-stats.png)

Ces graphiques font partie des outils de surveillance et d’analyse intégrés d’Azure. Découvrez-en plus sur ces capacités dans la [documentation Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="metrics-page"></a>Page Métriques

La page **Métriques** de HPC Cache est une autre fonctionnalité Azure standard. Suivez les conseils fournis dans la page pour créer vos propres graphiques et en savoir plus sur les fonctionnalités.

## <a name="cache-report"></a>Rapport du cache

Le **Rapport de cache** est un ensemble personnalisé de graphiques qui montrent l’espace utilisé, l’espace libre et la fréquence de recyclage de votre cache.

Par défaut, la page montre des données agrégées pour toutes les cibles de stockage du cache, mais vous pouvez utiliser le contrôle **Stockage cible** pour afficher les données de consommation du cache pour une cible de stockage particulière à la place. Vous pouvez également personnaliser l’intervalle de temps à afficher.

![Capture d’écran de la page Rapport du cache.](media/cache-report.png)

* L’**Espace de cache utilisé** correspond à la quantité d’espace de votre stockage de cache qui est utilisé pour stocker les fichiers demandés par les ordinateurs clients. Si vous sélectionnez une cible de stockage unique, elle montre uniquement l’espace utilisé pour les fichiers de ce système de stockage back-end.

* L’**Espace disponible dans le cache** correspond à la capacité disponible restante dans le stockage du cache.

* La **Fréquence de recyclage du cache** représente la fréquence à laquelle les anciens fichiers sont supprimés du cache pour libérer de l’espace pour les fichiers récemment demandés. Pour une cible de stockage unique, ce calcul comprend uniquement les fichiers stockés sur cette cible.

## <a name="client-status"></a>État du client

La page **État du client** liste les ordinateurs clients qui sont connectés à votre cache Azure HPC.

![Capture d’écran de la page État du client.](media/client-status.png)

Vous pouvez personnaliser la table à l’aide de filtres pour montrer une adresse ou une plage d’adresses de clients spécifique, une cible de stockage spécifique ou une adresse de montage de cache individuelle.

Les types de connexion indiqués dans le tableau doivent être en rapport avec les connexions clientes à la cible de stockage :

* `azure_https_backend` est une connexion cliente sécurisée à un système de Stockage Blob Azure standard.
* `mount_backend` est une connexion NFS à un système NFS matériel ou à un conteneur d’objets blob compatible NFS.
* `nfs_backend` est semblable à la connexion `mount_backend`, mais représente une connexion distincte utilisée par un appel de procédure distante interne à NFS au lieu d’une connexion lancée par l’opération de montage du client.

Ces valeurs peuvent être utiles lors du débogage de problèmes de connexion entre les clients et le cache HPC.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [outils de mesures et statistiques Azure](../azure-monitor/index.yml)
* [Gérer le cache](hpc-cache-manage.md)
