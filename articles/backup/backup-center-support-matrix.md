---
title: Matrice de prise en charge du Centre de sauvegarde
description: Cet article résume les scénarios pris en charge par le Centre de sauvegarde pour chaque type de charge de travail.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 602884f3cf077e53061bd02dc5235ddbe3073075
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033548"
---
# <a name="support-matrix-for-backup-center"></a>Matrice de prise en charge du Centre de sauvegarde

Le Centre de sauvegarde offre aux entreprises une interface unique pour [gérer, surveiller, exploiter et analyser les sauvegardes à grande échelle](backup-center-overview.md). Cet article résume les scénarios pris en charge par le Centre de sauvegarde pour chaque type de charge de travail.

## <a name="supported-scenarios"></a>Scénarios pris en charge

| **Catégorie** | **Scénario**  | **Charges de travail prises en charge**  | **Limites** |
| -------------| ------------- | ----------------------- |------------|
| Surveillance   | Afficher tous les travaux | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | <li> 7 jours de travaux prêts à l'emploi. <br> <li> Chaque filtre/liste déroulante prend en charge un maximum de 1 000 éléments. Le Centre de sauvegarde peut donc être utilisé pour surveiller un maximum de 1 000 abonnements et de 1 000 coffres sur les différents locataires. |
| Surveillance | Afficher toutes les instances de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Identique à ce qui précède |
| Surveillance | Afficher toutes les stratégies de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Identique à ce qui précède |
| Surveillance | Afficher tous les coffres | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Identique à ce qui précède |
| Surveillance | Afficher les alertes Azure Monitor à grande échelle | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Consultez la documentation sur les [alertes](./backup-azure-monitoring-built-in-monitor.md#azure-monitor-alerts-for-azure-backup-preview) |
| Surveillance     |   Afficher les mesures de Sauvegarde Azure et écrire des règles d’alerte de métrique | <li>Azure VM   </li><li>SQL dans une machine virtuelle Azure </li><li> SAP HANA sur machine virtuelle Azure</li><li>Azure Files </li> |   Vous pouvez afficher les mesures de tous les coffres Recovery Services pour une région et un abonnement simultanément. L’affichage des mesures pour une plus grande étendue dans le Portail Azure n’est pas pris en charge actuellement. Les mêmes limites s’appliquent également à la configuration des règles d’alerte de métrique. Pour plus d’informations, consultez [Afficher les mesures dans le Portail Azure](metrics-overview.md#view-metrics-in-the-azure-portal).|
| Actions | Configurer une sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour la [Sauvegarde de machines virtuelles Azure](./backup-support-matrix-iaas.md) et la [Sauvegarde d'un serveur Azure Database pour PostgreSQL](backup-azure-database-postgresql-overview.md#support-matrix) |
| Actions | Restaurer une instance de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour la [Sauvegarde de machines virtuelles Azure](./backup-support-matrix-iaas.md) et la [Sauvegarde d'un serveur Azure Database pour PostgreSQL](backup-azure-database-postgresql-overview.md#support-matrix) |
| Actions | Create vault | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour le [coffre Recovery Services](./backup-support-matrix.md#vault-support) |
| Actions | Créer la stratégie de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour le [coffre Recovery Services](./backup-support-matrix.md#vault-support) |
| Actions | Exécuter une sauvegarde à la demande pour une instance de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour la [Sauvegarde de machines virtuelles Azure](./backup-support-matrix-iaas.md) et la [Sauvegarde d'un serveur Azure Database pour PostgreSQL](backup-azure-database-postgresql-overview.md#support-matrix) |
| Actions | Arrêter la sauvegarde pour une instance de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure <br><br> <li> Azure Files<br/><br/> <li>Objets blob Azure<br/><br/> <li>Azure Disques managés | Reportez-vous aux matrices de prise en charge pour la [Sauvegarde de machines virtuelles Azure](./backup-support-matrix-iaas.md) et la [Sauvegarde d'un serveur Azure Database pour PostgreSQL](backup-azure-database-postgresql-overview.md#support-matrix) |
| Actions | Exécuter un travail de restauration inter-région à partir du Centre de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA sur machine virtuelle Azure | Consultez la document sur la [restauration inter-régions](./backup-create-rs-vault.md#set-cross-region-restore) |
| Insights | Afficher les rapports de sauvegarde | <li> Machine virtuelle Azure <br><br> <li> SQL dans une machine virtuelle Azure <br><br> <li> SAP HANA dans une machine virtuelle Azure <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Agent Azure Backup (MARS) <br><br> <li> Serveur Azure Backup (MABS) | Reportez-vous aux [scénarios pris en charge pour les Rapports de sauvegarde](./configure-reports.md#supported-scenarios) |
| Gouvernance | Afficher et attribuer des stratégies Azure intégrées et personnalisées sous la catégorie « Sauvegarde » | N/A | N/A |
| Gouvernance | Afficher les sources de données non configurées pour la sauvegarde | <li> Machine virtuelle Azure <br><br> <li> Serveur Azure Database pour PostgreSQL | N/A |

## <a name="unsupported-scenarios"></a>Scénarios non pris en charge

| **Catégorie** | **Scénario**  |
|--------------|---------------|
| Actions | La configuration des paramètres de coffre à grande échelle n’est pas prise en charge actuellement par le centre de sauvegarde |

## <a name="next-steps"></a>Étapes suivantes

* [Passer en revue la matrice de prise en charge du service Sauvegarde Azure](./backup-support-matrix.md)
* [Passer en revue la matrice de prise en charge de la sauvegarde des machines virtuelles Azure](./backup-support-matrix-iaas.md)
* [Passer en revue la matrice de prise en charge de la sauvegarde d'un serveur Azure Database pour PostgreSQL](backup-azure-database-postgresql-overview.md#support-matrix)