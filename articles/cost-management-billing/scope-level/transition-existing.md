---
title: Migrer vers Azure Hybrid Benefit managé de manière centralisée
description: Cet article décrit des modifications et plusieurs scénarios de transition pour illustrer la transition vers Azure Hybrid Benefit managé de manière centralisée.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: be701e090a7fd993f476cd45194b73368c589dc5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223989"
---
# <a name="transition-to-centrally-managed-azure-hybrid-benefit"></a>Migrer vers Azure Hybrid Benefit managé de manière centralisée

Lorsque vous passez à Azure Hybrid Benefit managé de manière centralisée, il n’est plus nécessaire de configurer l’avantage au niveau de la ressource. Cet article décrit les modifications et plusieurs scénarios de transition pour illustrer le résultat. Pour mieux comprendre comment la nouvelle expérience de gestion des licences au niveau de l’étendue applique les licences et les remises à vos ressources, consultez [Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?](overview-azure-hybrid-benefit-scope.md)

## <a name="changes-to-individual-resource-configuration"></a>Modifications apportées à la configuration des ressources individuelles

Lorsque vous attribuez des licences à un abonnement à l’aide de la nouvelle expérience, les modifications s’affichent dans le portail Azure. Par la suite, vous ne pourrez pas gérer l’avantage au niveau de la ressource. Toutes les modifications que vous apportez au niveau de l’étendue remplacent les paramètres au niveau de la ressource individuelle.

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="Capture d’écran montrant les informations de remplacement de la licence SQL." lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> Si vous modifiez les paramètres d’Azure Hybrid Benefit à l’aide de PowerShell, d’une API ou en dehors du portail Azure sur une ressource, la modification de vos paramètres est enregistrée. Toutefois, elle n’aura aucun effet tant que l’abonnement ou le compte de facturation de cette ressource est couvert par des licences attribuées à une étendue. Si vous refusez la gestion de niveau étendue d’Azure Hybrid Benefit en supprimant toutes les attributions de licence, vos remises sur les licences seront de nouveau déterminées par les paramètres d’Azure Hybrid Benefit sur chaque ressource.

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>Vérifier le nombre de licences SQL que vous utilisez avant la transition

Lorsque vous vous inscrivez à l’expérience de gestion de niveau étendue d’Azure Hybrid Benefit, vous verrez votre utilisation actuelle d’Azure Hybrid Benefit qui est activée pour les ressources individuelles. Pour plus d’informations sur l’expérience globale, consultez [Créer des attributions de licence SQL Server pour Azure Hybrid Benefit](create-sql-license-assignments.md). Si vous êtes un contributeur d’abonnement et que vous n’avez pas le rôle Administrateur de facturation requis, vous pouvez analyser l’utilisation des différents types de licences SQL Server dans Azure à l’aide d’un script PowerShell. Le script génère un instantané de l’utilisation sur plusieurs abonnements ou sur l’ensemble du compte. Pour plus d’informations et pour obtenir des exemples d’utilisation du script, consultez l’exemple de [script PowerShell sql-license-usage](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit). Une fois que vous avez exécuté le script, identifiez votre administrateur de facturation et discutez avec lui de la possibilité de transférer la gestion d’Azure Hybrid Benefit au niveau de l’abonnement ou du compte de facturation.

> [!NOTE]
> Le script inclut la prise en charge des licences de base normalisées (NCL). 

## <a name="hadr-benefit-for-sql-server-vms"></a>Avantage HADR pour les machines virtuelles SQL Server

La nouvelle expérience du portail Azure prend entièrement en charge l’avantage de la haute disponibilité et de la récupération d’urgence (HADR) pour les machines virtuelles SQL Server. Si votre machine virtuelle SQL Server est configurée en tant que réplica HADR, aucune autre action n’est requise. Pour plus d’informations sur le fonctionnement de l’avantage HADR pour les machines virtuelles SQL Server, consultez [Coexistence de SQL Server HADR et Azure Hybrid Benefit managé de manière centralisée](sql-server-hadr-licenses.md).

## <a name="transition-scenario-examples"></a>Exemples de scénarios de transition

Examinez les exemples de scénarios de transition suivants qui correspondent le mieux à votre situation.

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Migrer des charges de travail SQL de l’environnement local vers le cloud en utilisant SQL Database avec Azure Hybrid Benefit

- **Charges de travail SQL Server locales :** Deux machines à 16 cœurs hébergeant des bases de données critiques SQL Server Entreprise seront migrées vers le cloud.
- **Destination Azure pour la migration des charges de travail :** Après analyse, il est décidé que les charges de travail seront exécutées sur 2 instances gérées Azure SQL à 16 cœurs.
- **Licences à attribuer à Azure :** Compte tenu des points précédents, 32 licences de base SQL Server Entreprise avec Software Assurance seront attribuées à Azure.
- **Action recommandée :** Utilisez la nouvelle expérience du portail Azure pour attribuer 32 licences de base SQL Server Entreprise à l’abonnement approprié ou au compte de facturation global.
- **Résultat :** Une migration facile à gérer et à coût optimisé des charges de travail SQL Server de l’environnement local vers le cloud.

> [!NOTE] 
> Azure Hybrid Benefit permet aux licences attribuées à Azure de continuer à être utilisées localement pendant 180 jours maximum, à mesure que les charges de travail sont migrées, testées et déployées.

### <a name="simplify-license-management-by-transitioning-to-centralized-scope-level-management-of-azure-hybrid-benefit"></a>Simplifier la gestion des licences en passant à la gestion centralisée au niveau de l’étendue d’Azure Hybrid Benefit

- **Ressources SQL Server en cours d’exécution :** Une ressource Azure SQL Database critique pour l’entreprise de 64 cœurs est en cours d’exécution, avec Azure Hybrid Benefit sélectionné.
- **Licences disponibles à attribuer à Azure** : Votre équipe d’approvisionnement confirme qu’il y a plus de 64 licences de base SQL Server Entreprise avec Software Assurance qui ne sont pas utilisées localement.
- **Action recommandée :** Utilisez la nouvelle expérience de gestion de niveau étendue d’Azure Hybrid Benefit pour attribuer 64 licences de base SQL Server Entreprise à Azure. Ou si vous prévoyez que l’utilisation augmentera bientôt, attribuez plus de licences le cas échéant.
- **Résultat :** La transition couvrira le coût du logiciel SQL Server ; il ne devrait donc pas y avoir de changement dans votre facturation associée.

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>Économiser davantage en attribuant plus de licences SQL Server pour couvrir plus de ressources Azure SQL

- **Ressources SQL Server en cours d’exécution :** Deux ressources Azure SQL Database critique pour l’entreprise de 16 cœurs sont en cours d’exécution, avec Azure Hybrid Benefit sélectionné sur une seule d’entre elles.
- **Licences disponibles à attribuer à Azure** : Selon votre équipe d’approvisionnement, 48 licences de base SQL Server Entreprise avec Software Assurance ne sont pas utilisées localement ou dans Azure.
- **Action recommandée :** Utilisez l’expérience de gestion de niveau étendue d’Azure Hybrid Benefit pour attribuer les 48 licences de base SQL Server Entreprise. Cela représente une augmentation de 16 licences par rapport à la sélection d’Azure Hybrid Benefit au niveau des ressources.
- **Résultat :** Étant donné que davantage de licences achetées en dehors d’Azure sont utilisées, les montants facturés dans Azure devraient être réduits. En outre, comme le coût annuel de la Software Assurance est inférieur au coût annuel de SQL Server au tarif du paiement à l’utilisation, le coût total de l’utilisation de SQL Server est réduit.

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>Restaurer la conformité en cas d’utilisation excessive d’Azure Hybrid Benefit

- **Ressources SQL Server en cours d’exécution :** Trois bases de données SQL Usage général à 8 cœurs et une machine virtuelle SQL Server Entreprise à 16 cœurs sont en cours d’exécution, avec Azure Hybrid Benefit sélectionné sur toutes. Le nombre de licences Azure Hybrid Benefit requises pour couvrir ces ressources est de 24 cœurs d’édition Standard + 16 cœurs SQL Server Entreprise OU 88 SQL Server Standard (+ 0 SQL Server Entreprise) OU 22 SQL Server Entreprise (+ 0 SQL Server Standard). En effet, une licence de base SQL Server Entreprise et quatre licences de base SQL Server Standard peuvent couvrir la même utilisation d’Azure Hybrid Benefit sur tous les types de ressources Azure SQL. Consultez les règles d’Azure Hybrid Benefit dans l’article [Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?](overview-azure-hybrid-benefit-scope.md) pour en savoir plus sur cette flexibilité.
- **Licences disponibles à attribuer à Azure** : Selon votre équipe d’approvisionnement, 64 licences de base SQL Server Standard avec Software Assurance ne sont pas utilisées localement ou dans Azure. C’est moins que le nombre requis de 22 licences de base SQL Server Entreprise ou 88 licences de base SQL Server Standard.
- **Action recommandée :** Pour restaurer la conformité, identifiez 6 licences de base SQL Server Entreprise ou 24 licences de base SQL Server Standard avec Software Assurance et attribuez-les, ainsi que les 64 licences de base SQL Server Standard déjà confirmées, à Azure en utilisant l’expérience de gestion de niveau étendue d’Azure Hybrid Benefit.
- **Résultat :** La non-conformité est éliminée et Azure Hybrid Benefit est utilisé de manière optimale pour réduire les coûts.
- **Autre solution :** N’attribuez à Azure que les 64 licences de base SQL Server Standard disponibles. Vous serez en conformité, mais comme ces licences sont insuffisantes pour couvrir toute l’utilisation d’Azure SQL, vous devrez payer des frais de paiement à l’utilisation.
## <a name="next-steps"></a>Étapes suivantes

- Suivez le tutoriel [Optimiser Azure Hybrid Benefit managé de manière centralisée pour SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Passez à la gestion des licences de niveau étendue en [créant des attributions de licence SQL Server](create-sql-license-assignments.md).
- Passez en revue les [questions fréquentes (FAQ) sur Azure Hybrid Benefit managé de manière centralisée](faq-azure-hybrid-benefit-scope.yml).