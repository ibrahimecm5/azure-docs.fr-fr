---
title: Nouveautés de Sauvegarde Azure
description: En savoir plus sur les nouvelles fonctionnalités de Sauvegarde Azure.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: 398833135c97d3d067e7e2b29704ffd6bad25bbd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020423"
---
# <a name="whats-new-in-azure-backup"></a>Nouveautés de Sauvegarde Azure

Sauvegarde Azure s’améliore constamment et publie de nouvelles fonctionnalités qui améliorent la protection de vos données dans Azure. Ces nouvelles fonctionnalités étendent la protection de vos données aux nouveaux types de charges de travail, améliorent la sécurité et améliorent la disponibilité de vos données de sauvegarde. Elles ajoutent également de nouvelles fonctionnalités de gestion, de surveillance et d’automatisation.

Pour en savoir plus sur les nouvelles versions, vous pouvez créer un signet sur cette page ou [vous abonner aux mises à jour ici](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Résumé des mises à jour

- Octobre 2021
  - [Métriques de Sauvegarde Azure et alertes de métriques (préversion)](#azure-backup-metrics-and-metrics-alerts-in-preview)
- Juillet 2021
  - [La prise en charge du niveau Archive pour SQL Server dans une machine virtuelle Azure pour le service Sauvegarde Azure est désormais généralement disponible](#archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available)
- Mai 2021
  - [La sauvegarde pour les objets BLOB Azure est désormais en disponibilité générale](#backup-for-azure-blobs-is-now-generally-available)
- Avril 2021
  - [Améliorations apportées au chiffrement à l’aide de clés gérées par le client pour Sauvegarde Azure (préversion)](#enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview)
- Mars 2021
  - [La sauvegarde des disques Azure est désormais en disponibilité générale](#azure-disk-backup-is-now-generally-available)
  - [Le Centre de sauvegarde est désormais en disponibilité générale](#backup-center-is-now-generally-available)
  - [Prise en charge du niveau archive pour le service Sauvegarde Azure (en préversion)](#archive-tier-support-for-azure-backup-in-preview)
- Février 2021
  - [Sauvegarde pour objets blob Azure (en préversion)](#backup-for-azure-blobs-in-preview)
- Janvier 2021
  - [Sauvegarde de disque Azure (en préversion)](#azure-disk-backup-in-preview)
  - [Chiffrement au repos à l’aide de clés gérées par le client (disponibilité générale)](#encryption-at-rest-using-customer-managed-keys)
- Novembre 2020
  - [Modèle Azure Resource Manager pour une sauvegarde de partage de fichiers Azure (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Sauvegardes incrémentielles pour les bases de données SAP HANA sur des machines virtuelles Azure (en préversion)](#incremental-backups-for-sap-hana-databases-in-preview)
- Septembre 2020
  - [Centre de sauvegarde (en préversion)](#backup-center-in-preview)
  - [Sauvegarder Azure Database pour PostgreSQL (en préversion)](#backup-azure-database-for-postgresql-in-preview)
  - [Sauvegarde et restauration sélectives sur disque](#selective-disk-backup-and-restore)
  - [Restauration inter-région pour les bases de données SQL Server et SAP HANA sur des machines virtuelles Azure (en préversion)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Prise en charge de la sauvegarde des machines virtuelles comptant jusqu’à 32 disques (disponibilité générale)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Expérience de configuration de la sauvegarde simplifiée pour SQL dans les machines virtuelles Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Sauvegarde SAP HANA sur des machines virtuelles RHEL Azure (en préversion)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Stockage redondant interzone (ZRS) pour les données de sauvegarde (en préversion)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Suppression réversible pour les charges de travail SQL Server et SAP HANA dans les machines virtuelles Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-backup-metrics-and-metrics-alerts-in-preview"></a>Métriques de Sauvegarde Azure et alertes de métriques (préversion)

La Sauvegarde Azure fournit maintenant, avec [Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics), un ensemble de métriques intégrées qui vous permettent d’effectuer un monitoring de l’intégrité de vos sauvegardes. Vous pouvez également configurer des règles qui déclenchent des alertes lorsque les métriques dépassent les seuils définis.

Voici les principales fonctionnalités offertes par la Sauvegarde Azure :
 
- Possibilité d’afficher des métriques prêtes à l’emploi liées à la sauvegarde et à la restauration de l’intégrité des éléments de sauvegarde, ainsi que les tendances associées
- Possibilité d’écrire des règles d’alerte personnalisées sur ces métriques pour effectuer un monitoring efficace de l’intégrité des éléments de sauvegarde
- Possibilité d’acheminer les alertes de métriques déclenchées suivant différents canaux de notification pris en charge par Azure Monitor (e-mail, ITSM, Webhook, applications logiques, etc.)
 
Actuellement, la Sauvegarde Azure prend en charge les métriques intégrées pour les types de charges de travail suivants :

- Azure VM
- Bases de données SQL dans une machine virtuelle Azure
- Bases de données SAP HANA dans une machine virtuelle Azure
- Azure Files

Pour plus d’informations, consultez [Monitoring de l’intégrité des sauvegardes avec les métriques de la Sauvegarde Azure (préversion)](metrics-overview.md).

## <a name="archive-tier-support-for-sql-server-in-azure-vm-for-azure-backup-is-now-generally-available"></a>La prise en charge du niveau Archive pour SQL Server dans une machine virtuelle Azure pour le service Sauvegarde Azure est désormais généralement disponible

Le service Sauvegarde Azure vous permet de déplacer vos points de rétention à long terme pour machines virtuelles Azure et SQL Server dans des machines virtuelles Azure vers le niveau Archive à faible coût. Vous pouvez également opérer une restauration à partir des points de récupération dans le niveau Archive du coffre.

En plus de la possibilité de déplacer les points de récupération :

- Sauvegarde Azure fournit des recommandations pour déplacer un ensemble spécifique de points de récupération pour les sauvegardes de machines virtuelles Azure, qui garantissent des économies.
- Vous avez la possibilité de déplacer tous les points de récupération d’un élément de sauvegarde particulier en même temps à l’aide d’exemples de scripts.
- Vous pouvez afficher l’utilisation du stockage Archive dans le tableau de bord du coffre.

Pour plus d’informations, consultez [Prise en charge du niveau de stockage archive](./archive-tier-support.md).

## <a name="backup-for-azure-blobs-is-now-generally-available"></a>La sauvegarde pour les objets BLOB Azure est désormais en disponibilité générale

La sauvegarde opérationnelle des objets BLOB Azure est une solution gérée de protection des données qui permet de protéger les données de vos objets BLOB de blocs contre différentes situations de perte de données, telles que les altérations, la suppression d’objets BLOB et la suppression accidentelle de comptes de stockage.

En tant que solution de sauvegarde opérationnelle, les données de sauvegarde sont stockées localement dans le compte de stockage source et peuvent être récupérées à partir d’un point dans le temps sélectionné, ce qui vous donne un moyen simple et économique de protéger vos données BLOB. Pour ce faire, la solution utilise la fonctionnalité de restauration à un instant dans le passé des objets BLOB disponible dans le stockage d’objets BLOB.

La sauvegarde opérationnelle pour les objets BLOB s’intègre aux outils de gestion de Sauvegarde Azure, notamment le centre de sauvegarde, pour vous aider à gérer la protection de vos données BLOB efficacement et à grande échelle. Outre les fonctionnalités précédemment disponibles, vous pouvez maintenant configurer et gérer la sauvegarde opérationnelle des objets BLOB à l’aide de la vue **Protection des données** des comptes de stockage, ou [via PowerShell](backup-blobs-storage-account-ps.md). La sauvegarde vous offre également une expérience améliorée pour la gestion des attributions de rôle requises pour la configuration de la sauvegarde opérationnelle.

Pour plus d’informations, consultez [Vue d’ensemble de la sauvegarde opérationnelle pour les objets BLOB Azure](blob-backup-overview.md).

## <a name="azure-disk-backup-is-now-generally-available"></a>La sauvegarde des disques Azure est désormais en disponibilité générale

Le service Sauvegarde Azure permet la gestion de cycle de vie des captures instantanées pour les disques managés Azure en automatisant la création régulière de captures instantanées. Celles-ci sont conservées pendant les durées configurées via la stratégie de sauvegarde.

Pour plus d’informations, consultez [Vue d’ensemble de la sauvegarde des disques Azure](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>Le centre de sauvegarde est désormais en disponibilité générale

Le centre de sauvegarde simplifie la gestion de la protection des données à grande échelle en vous permettant de découvrir, d’administrer, de superviser, d’exécuter et d’optimiser la gestion des sauvegardes dans une seule console centrale.

Pour plus d’informations, consultez [Vue d’ensemble du centre de sauvegarde](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Prise en charge du niveau archive pour le service Sauvegarde Azure (en préversion)

Le service Sauvegarde Azure vous permet désormais de réduire les coûts des sauvegardes de conservation à long terme avec la disponibilité du niveau Archive pour les machines virtuelles Azure et de SQL Server dans les machines virtuelles Azure.

Pour plus d’informations, consultez [Prise en charge du niveau Archive (préversion)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Sauvegarde pour les objets blob Azure (en préversion)

La sauvegarde opérationnelle pour objets blob est une solution de protection des données locale gérée qui vous permet de protéger vos objets blob de blocs contre différentes situations de perte de données telles que des altérations de données, des suppressions de blobs et la suppression accidentelle de compte de stockage. Les données sont stockées localement dans le compte de stockage source proprement dit, et peuvent être récupérées au point dans le temps sélectionné si nécessaire. La solution offre ainsi un moyen simple, sécurisé et économique de protéger vos objets blob.

La sauvegarde opérationnelle pour objets blob s’intègre avec le centre de sauvegarde, entre autres fonctionnalités de gestion des sauvegardes, pour fournir un volet unique pour vous aider à gérer, surveiller, exploiter et analyser les sauvegardes à grande échelle.

Pour plus d’informations, consultez [Vue d’ensemble de la sauvegarde opérationnelle pour les objets blob Azure (en préversion)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Sauvegarde de disque Azure (en préversion)

La sauvegarde de disques Azure offre une solution clé en main qui fournit une gestion du cycle de vie des instantanés pour les [disques managés Azure](../virtual-machines/managed-disks-overview.md) en automatisant la création périodique d’instantanés et en les conservant pour une durée configurée à l’aide d’une stratégie de sauvegarde. Vous pouvez gérer les instantanés des disques sans aucun coût d’infrastructure et sans avoir recours à aucun script personnalisé ni aucune surcharge de gestion. Il s’agit d’une solution de sauvegarde avec cohérence en cas de plantage qui effectue une sauvegarde ponctuelle d’un disque managé au moyen d’[instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) avec une prise en charge de plusieurs sauvegardes par jour. Il s’agit également d’une solution sans agent qui n’a pas d’impact sur les performances des applications de production. Elle prend en charge la sauvegarde et la restauration des disques du système d’exploitation et des données (y compris des disques partagés), qu’ils soient ou non actuellement attachés à une machine virtuelle Azure en cours d’exécution.

Pour plus d’informations, consultez [Sauvegarde de disque Azure (en préversion)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Chiffrement au repos à l’aide de clés gérées par le client

La prise en charge du chiffrement au repos à l’aide de clés gérées par le client est désormais généralement disponible. Vous avez ainsi la possibilité de chiffrer les données de sauvegarde dans vos coffres Recovery Services à l’aide de vos propres clés stockées dans des coffres de clés Azure. La clé de chiffrement utilisée pour le chiffrement des sauvegardes dans le coffre Recovery Services peut être différente de celle utilisée pour le chiffrement de la source. Les données sont protégées à l’aide d’une clé de chiffrement des données basée sur l’algorithme AES 256, qui est à son tour protégée à l’aide de vos clés stockées dans le Key Vault. Par rapport au chiffrement à l’aide de clés gérées par la plateforme (qui est disponible par défaut), cela vous offre davantage de contrôle sur vos clés et peut vous aider à mieux répondre à vos besoins en matière de conformité.

Pour plus d’informations, consultez [Chiffrement des données de sauvegarde à l’aide de clés gérées par le client](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modèle Azure Resource Manager pour une sauvegarde AFS

Sauvegarde Azure prend désormais en charge la configuration de la sauvegarde pour les partages de fichiers Azure existants à l’aide d’un modèle Azure Resource Manager (ARM). Le modèle configure la protection d’un partage de fichiers Azure existant en spécifiant les détails appropriés pour le coffre et la stratégie de sauvegarde Recovery Services. Il crée éventuellement un coffre et une stratégie de sauvegarde Recovery Services, puis enregistre le compte de stockage contenant le partage de fichiers dans le coffre Recovery Services.

Pour plus d’informations, consultez [Modèles Azure Resource Manager pour la Sauvegarde Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Sauvegardes incrémentielles pour les bases de données SAP HANA (en préversion)

Sauvegarde Azure prend maintenant en charge les sauvegardes incrémentielles pour les bases de données SAP HANA hébergées sur des machines virtuelles Azure. Cela permet d’obtenir des sauvegardes plus rapides et plus rentables de vos données SAP HANA.

Pour plus d’informations, consultez [diverses options disponibles lors de la création d’une stratégie de sauvegarde](./sap-hana-faq-backup-azure-vm.yml) et la procédure de [création d’une stratégie de sauvegarde pour les bases de données SAP HANA](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Centre de sauvegarde (en préversion)

Sauvegarde Azure a activé une nouvelle fonctionnalité de gestion native pour gérer l’ensemble de votre espace de sauvegarde à partir d’une console centrale. Le centre de sauvegarde vous offre la possibilité de surveiller, d’utiliser, de gérer et d’optimiser la protection des données à grande échelle de façon cohérente et unifiée avec les expériences de gestion natives d’Azure.

Avec le centre de sauvegarde, vous bénéficiez d’une vue agrégée de votre inventaire sur les abonnements, les emplacements, les groupes de ressources, les coffres et même les locataires à l’aide d’Azure Lighthouse. Le centre de sauvegarde est également un centre d’action dans lequel vous pouvez déclencher vos activités de sauvegarde, telles que la configuration de la sauvegarde, la restauration, la création de stratégies ou de coffres, le tout à partir d’un seul emplacement. En outre, grâce à une intégration transparente à Azure Policy, vous pouvez désormais gérer votre environnement et suivre la conformité du point de vue de la sauvegarde. Les stratégies Azure intégrées spécifiques à Sauvegarde Azure vous permettent également de configurer des sauvegardes à grande échelle.

Pour plus d’informations, consultez [Vue d’ensemble du centre de sauvegarde](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Sauvegarder Azure Database pour PostgreSQL (en préversion)

Sauvegarde Azure et les services de base de données Azure constituent ensemble une solution de sauvegarde d’entreprise pour Azure PostgreSQL (maintenant en préversion). Vous pouvez désormais répondre à vos besoins de conformité et de protection des données avec une stratégie de sauvegarde contrôlée par le client qui permet la rétention des sauvegardes pendant maximum 10 ans. Vous disposez ainsi d’un contrôle granulaire pour gérer les opérations de sauvegarde et de restauration au niveau de la base de données individuelle. De même, vous pouvez effectuer des restaurations dans les versions PostgreSQL ou dans le stockage d’objets Blob en toute simplicité.

Pour plus d’informations, consultez [Sauvegarde Azure Database pour PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Sauvegarde et restauration sélectives sur disque

Sauvegarde Azure prend en charge la sauvegarde de tous les disques (système d’exploitation et données) d’une machine virtuelle à travers la solution de sauvegarde de machine virtuelle. Désormais, en utilisant la fonctionnalité de sauvegarde et restauration sélectives de disques, vous pouvez sauvegarder un sous-ensemble de disques de données d’une machine virtuelle. Cela offre une solution efficace et économique pour vos besoins en sauvegarde et restauration. Chaque point de récupération contient uniquement les disques inclus dans l’opération de sauvegarde.

Pour plus d’informations, consultez [Sauvegarde et restauration sélectives sur disque pour les machines virtuelles Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Restauration inter-région pour SQL Server et SAP HANA (en préversion)

Grâce à l’introduction de la restauration inter-région, vous pouvez maintenant initier des restaurations dans une région secondaire à votre gré pour réduire les réels problèmes de temps d’arrêt dans une région principale pour votre environnement. Ainsi, la région secondaire effectue la restauration sous le contrôle total du client. Sauvegarde Azure utilise les données sauvegardées répliquées dans la région secondaire pour de telles restaurations.

Désormais, en plus de la prise en charge de la restauration inter-région pour les machines virtuelles Azure, la fonctionnalité a été étendue à la restauration des bases de données SQL et SAP HANA dans les machines virtuelles Azure également.

Pour plus d’informations, consultez [Restauration inter-région pour les bases de données SQL](restore-sql-database-azure-vm.md#cross-region-restore) et [Restauration inter-région pour les bases de données SAP HANA](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Prise en charge de la sauvegarde de machines virtuelles avec un maximum de 32 disques

Jusqu’à présent, Sauvegarde Azure prenait en charge 16 disques managés par machine virtuelle. Désormais, Sauvegarde Azure prend en charge la sauvegarde d’un maximum de 32 disques managés par machine virtuelle.

Pour plus d’informations, consultez [Matrice de prise en charge de stockage de machine virtuelle](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configuration de sauvegarde plus simple pour SQL dans les machines virtuelles Azure

La configuration de sauvegardes pour votre SQL Server dans les machines virtuelles Azure est désormais encore plus facile avec la configuration de sauvegarde en ligne intégrée au volet des machines virtuelles du portail Azure. En quelques étapes, vous pouvez activer la sauvegarde de vos SQL Server pour protéger toutes les bases de données existantes, ainsi que celles qui sont ajoutées ultérieurement.

Pour plus d’informations, consultez [Sauvegarder un SQL Server à partir du volet de la machine virtuelle](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Sauvegarde SAP HANA sur des machines virtuelles RHEL Azure (en préversion)

Sauvegarde Azure est la solution de sauvegarde native pour Azure et est certifiée BackInt par SAP. Sauvegarde Azure a maintenant ajouté la prise en charge de Red Hat Enterprise Linux (RHEL), l’un des systèmes d’exploitation Linux les plus courants exécutant SAP HANA.

Pour plus d’informations, consultez [Matrice de prise en charge du scénario de sauvegarde de base de données SAP HANA](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Stockage redondant interzone (ZRS) pour les données de sauvegarde (en préversion)

Stockage Azure offre un bon compromis entre hautes performances, haute disponibilité et haute résilience des données grâce à ses diverses options de redondance. La Sauvegarde Azure permet d’étendre ces avantages aux données de sauvegarde, en offrant la possibilité de stocker les sauvegardes dans un stockage localement redondant (LRS, Locally Redundant Storage) et un stockage géoredondant (GRS, Geo-Redundant Storage). Maintenant, il existe des options de durabilité supplémentaires avec la prise en charge supplémentaire du stockage redondant interzone (ZRS).

Pour plus d’informations, consultez [Définir la redondance du stockage pour le coffre Recovery Services](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Suppression réversible pour les charges de travail SQL Server et SAP HANA

Les préoccupations en matière de risques de sécurité, comme les logiciels malveillants, le ransomware et les intrusions, sont de plus en plus nombreuses. Ces problèmes de sécurité peuvent coûter cher, à la fois en termes d’argent et de données. Pour vous protéger contre ces attaques, Sauvegarde Azure fournit des fonctionnalités de sécurité visant à protéger les données de sauvegarde après la suppression.

Une fonctionnalité de ce type est la suppression réversible. Avec la suppression réversible, même si un intervenant malveillant supprime une sauvegarde (ou même si les données de sauvegarde sont accidentellement supprimées), les données de sauvegarde sont conservées pendant 14 jours supplémentaires, ce qui permet la récupération de cet élément de sauvegarde sans perte de données. La conservation des données de sauvegarde pendant 14 jours supplémentaires à l’état « suppression réversible » n’engendre aucun frais pour vous.

Actuellement, en plus de la prise en charge de la suppression réversible pour les machines virtuelles Azure, les charges de travail SQL Server et SAP HANA dans les machines virtuelles Azure sont également protégées avec la suppression réversible.

Pour plus d’informations, consultez [Suppression réversible pour les charges de travail SQL Server sur une machine virtuelle Azure et SAP HANA sur une machine virtuelle Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="enhancements-to-encryption-using-customer-managed-keys-for-azure-backup-in-preview"></a>Améliorations apportées au chiffrement à l’aide de clés gérées par le client pour Sauvegarde Azure (préversion)

Sauvegarde Azure offre désormais des capacités améliorées (en préversion) pour gérer le chiffrement avec des clés gérées par le client. Sauvegarde Azure vous permet d’introduire vos propres clés pour chiffrer les données de sauvegarde dans les coffres Recovery Services, ce qui vous offre un meilleur contrôle.

- Prend en charge les identités managées affectées par l’utilisateur pour accorder des autorisations aux clés afin de gérer le chiffrement des données dans le coffre Recovery Services.
- Permet le chiffrement avec des clés gérées par le client lors de la création d’un coffre Recovery Services.
  >[!NOTE]
  >Cette fonctionnalité est actuellement en préversion limitée. Pour vous inscrire, remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) et écrivez-nous à l’adresse [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
- Vous permet d’utiliser des stratégies Azure pour auditer et appliquer le chiffrement à l’aide de clés gérées par le client.
>[!NOTE]
>- Les capacités ci-dessus sont prises en charge par le portail Azure uniquement, PowerShell n’est pas pris en charge actuellement.<br>Si vous utilisez PowerShell pour gérer les clés de chiffrement pour Sauvegarde, nous vous déconseillons de mettre à jour les clés à partir du portail.<br>Si vous mettez à jour la clé à partir du portail, vous ne pourrez plus utiliser PowerShell pour mettre à jour la clé de chiffrement, jusqu’à ce qu’une mise à jour de PowerShell prenant en charge le nouveau modèle soit disponible. Toutefois, vous pouvez continuer à mettre à jour la clé depuis le portail Azure.
>- Vous pouvez utiliser la stratégie d’audit pour auditer des coffres dont le chiffrement à l’aide de clés gérées par le client (CMK) est activé après le 01/04/2021.  
>- Pour les coffres avec chiffrement CMK activé avant cette date, la stratégie risque de ne pas s’appliquer ou d’afficher des résultats négatifs erronés (autrement dit, ces coffres peuvent être signalés comme non conformes, même si le chiffrement CMK est activé). [Plus d’informations](encryption-at-rest-with-cmk.md#using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview)

Pour plus d’informations, consultez [Chiffrement pour Sauvegarde Azure à l’aide de clés gérées par le client](encryption-at-rest-with-cmk.md). 

## <a name="next-steps"></a>Étapes suivantes

- [Aide et bonnes pratiques de Sauvegarde Azure](guidance-best-practices.md)
