---
title: Nouveautés
titleSuffix: Azure SQL Managed Instance
description: Découvrez les nouvelles fonctionnalités et améliorations apportées à la documentation pour Azure SQL Managed Instance.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 9af25cea1d0765aa088699421bb672ac05c3eed7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031363"
---
# <a name="whats-new-in-azure-sql-managed-instance"></a>Nouveautés d’Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article récapitule les modifications apportées à la documentation en lien avec les nouvelles fonctionnalités et les améliorations introduites dans les versions récentes d’[Azure SQL Managed Instance](https://azure.microsoft.com/updates/?product=sql-database&query=sql%20managed%20instance). Pour en savoir plus sur Azure SQL Managed Instance, consultez la [présentation](sql-managed-instance-paas-overview.md).


Pour en savoir plus sur Azure SQL Database, consultez [Nouveautés](../database/doc-changes-updates-release-notes-whats-new.md).


## <a name="preview"></a>Préversion

Le tableau suivant liste les fonctionnalités d’Azure SQL Managed Instance actuellement en préversion :

| Fonctionnalité | Détails |
| ---| --- |
| [Prise en charge de 16 To dans Critique pour l’entreprise](resource-limits.md#service-tier-characteristics) | Prise en charge d’une allocation d’espace pouvant atteindre 16 To sur SQL Managed Instance dans le niveau de service Critique pour l’entreprise avec la nouvelle génération de matériel Premium à mémoire optimisée. | 
|[Stratégies de point de terminaison](../../azure-sql/managed-instance/service-endpoint-policies-configure.md) | Configurez les comptes de stockage Azure accessibles à partir d’un sous-réseau SQL Managed Instance. Octroie une couche supplémentaire de protection contre l’exfiltration de données involontaire ou malveillantes.
| [Pools d’instances](instance-pools-overview.md) | Moyen pratique et économique de migrer des instances SQL plus petites vers le cloud. |
| [Fonctionnalité de liaison](link-feature.md)| Réplication en ligne de bases de données SQL Server hébergées n’importe où sur Azure SQL Managed Instance. |
| [Rétention des sauvegardes à long terme](long-term-backup-retention-configure.md) | Prise en charge de la conservation des sauvegardes à long terme jusqu’à 10 ans sur Azure SQL Managed Instance. |
| [Fenêtre de maintenance](../database/maintenance-window.md)| Cette fonctionnalité vous permet de configurer une planification de maintenance pour votre instance gérée Azure SQL. |
| [Génération de matériel de la série Premium à mémoire optimisée](resource-limits.md#service-tier-characteristics) | Déployez votre instance managée SQL vers la nouvelle génération de matériel de la série Premium à mémoire optimisée pour tirer parti des derniers processeurs Intel Ice Lake. La génération de matériel à mémoire optimisée offre un rapport mémoire/vCore plus élevé. | 
| [Migration avec le Service de relecture de journal](log-replay-service-migrate.md) | Migrer des bases de données depuis SQL Server vers SQL Managed Instance à l’aide du Service de relecture de journal. |
| [Génération de matériel de la série Premium](resource-limits.md#service-tier-characteristics) | Déployez votre instance managée SQL vers la nouvelle génération de matériel de la série Premium pour tirer parti des derniers processeurs Intel Ice Lake.  | 
| [Échange de messages entre instances de Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | Prise en charge de l’échange de messages entre instances à l’aide de Service Broker sur Azure SQL Managed Instance. |
| [Insights SQL](../../azure-monitor/insights/sql-insights-overview.md) | SQL Insights est une solution complète de surveillance de tout produit de la famille SQL Azure. SQL Insights utilise des vues de gestion dynamique pour exposer les données dont vous avez besoin pour surveiller l’intégrité, diagnostiquer les problèmes et optimiser les performances. |
| [Réplication transactionnelle](replication-transactional-overview.md) | Répliquez les modifications de vos tableaux dans d’autres bases de données dans SQL Managed Instance, SQL Database ou SQL Server. Ou mettez à jour vos tableaux lorsque certaines lignes sont modifiées dans d’autres instances de SQL Managed Instance ou SQL Server. Pour plus d’informations, consultez [Configurer la réplication dans Azure SQL Managed Instance](replication-between-two-instances-configure-tutorial.md). |
| [Détection de menaces](threat-detection-configure.md) | La détection de menaces vous avertit des menaces de sécurité détectées dans votre base de données. |
| [Indicateurs du Magasin des requêtes](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-mi-current&preserve-view=true) | Utilisez des indicateurs de requête pour optimiser l’exécution de vos requêtes avec la clause OPTION. |
|||

## <a name="general-availability-ga"></a>Disponibilité générale

Le tableau suivant liste les fonctionnalités d’Azure SQL Managed Instance qui sont passées de la préversion à la disponibilité générale (GA) au cours des 12 derniers mois : 

| Fonctionnalité | Mois en GA | Détails |
| ---| --- |--- |
|[Prise en charge de 16 To pour Usage général](resource-limits.md)| Novembre 2021 |  Prise en charge d’une allocation d’espace pouvant atteindre 16 To sur SQL Managed Instance dans le niveau de service Usage général. |
[Authentification Azure Active Directory uniquement](../database/authentication-azure-ad-only-authentication.md) | Novembre 2021 |  Il est désormais possible de restreindre l’authentification auprès de votre instance managée Azure SQL aux utilisateurs d’Azure Active Directory. |
| [Transactions distribuées](../database/elastic-transactions-overview.md) | Novembre 2021 | Les transactions de base de données distribuées pour Azure SQL Managed Instance vous permettent d’exécuter des transactions distribuées qui s’étendent sur plusieurs bases de données entre des instances. | 
|[Serveur lié - Authentification Azure AD par identité managée](/sql/relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql#h-create-sql-managed-instance-linked-server-with-managed-identity-azure-ad-authentication) |Novembre 2021 | Créez un serveur lié avec authentification par identité managée pour votre instance managée Azure SQL.|
|[Serveur lié - Authentification Azure AD directe](/sql/relational-databases/system-stored-procedures/sp-addlinkedserver-transact-sql#i-create-sql-managed-instance-linked-server-with-pass-through-azure-ad-authentication) |Novembre 2021 | Créez un serveur lié avec authentification Azure AD directe pour votre instance managée Azure SQL. |
| [Déplacer l’instance vers un autre sous-réseau](vnet-subnet-move-instance.md)| Novembre 2021 | Déplacez SQL Managed Instance vers un autre sous-réseau à l’aide du Portail Azure, Azure PowerShell ou Azure CLI.  |
|[Opérations de gestion d’audit](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  Mars 2021 | Les capacités d’audit d’Azure SQL vous permettent d’auditer les opérations effectuées par les ingénieurs du support de Microsoft quand ils accèdent à vos ressources SQL lors d’une demande de support, améliorant ainsi la transparence avec vos employés. |
|[Autorisations précises pour le masquage dynamique des données](../database/dynamic-data-masking-overview.md)| Mars 2021 | Le masquage dynamique des données permet d’empêcher les accès non autorisés à des données sensibles. Pour cela, les clients peuvent indiquer la quantité de données sensibles à exposer avec un impact minimal sur la couche Application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête, sur des champs de base de données désignés (les données dans la base de données ne sont pas modifiées). Il est désormais possible d’attribuer des autorisations précises pour les données qui ont été masquées dynamiquement. Pour plus d’informations, consultez [Masquage dynamique des données](../database/dynamic-data-masking-overview.md#permissions). |
|[Machine Learning Service](machine-learning-services-overview.md) | Mars 2021 | Machine Learning Services est une fonctionnalité d’Azure SQL Managed Instance qui fournit un apprentissage automatique en base de données prenant en charge les scripts Python et R. La fonctionnalité inclut les packages Microsoft Python et R pour l’analyse prédictive haute performance et l’apprentissage automatique. |
||| 

## <a name="documentation-changes"></a>Modifications apportées à la documentation

Découvrez les modifications importantes apportées à la documentation d’Azure SQL Managed Instance.

### <a name="november-2021"></a>Novembre 2021

| Modifications | Détails |
| --- | --- |
| **Prise en charge de 16 To pour Critique pour l’entreprise (préversion)** | Le niveau de service Critique pour l’entreprise de SQL Managed Instance offre désormais une capacité de stockage d’instance maximale pouvant atteindre 16 To avec les nouvelles générations de matériel (série Premium et série Premium à mémoire optimisée), actuellement en préversion. Consultez les [limites de ressources](resource-limits.md#service-tier-characteristics) pour en savoir plus. |
|**Prise en charge de 16 To pour Usage général (disponibilité générale)** | Le déploiement d’une instance de 16 To sur le niveau de service Usage général est désormais en disponibilité générale.  Consultez les [limites de ressources](resource-limits.md) pour en savoir plus. |
| **Authentification Azure AD uniquement (disponibilité générale)** |  La fonctionnalité permettant de restreindre l’authentification auprès de votre instance managée Azure SQL aux utilisateurs d’Azure Active Directory est désormais en disponibilité générale. Pour plus d’informations, consultez [Authentification avec Azure AD uniquement](../database/authentication-azure-ad-only-authentication.md). |
| **Transactions distribuées (disponibilité générale)** | La possibilité d’exécuter des transactions distribuées sur plusieurs instances managées est désormais en disponibilité générale. Pour en savoir plus, consultez [Transactions distribuées](../database/elastic-transactions-overview.md). |
|**Aperçu des stratégies de point de terminaison** | Il est désormais possible de configurer une stratégie de point de terminaison pour limiter l’accès d’un sous-réseau SQL Managed Instance à un compte stockage Azure. Cela accorde une couche supplémentaire de protection contre l’exfiltration de données involontaire ou malveillantes. Pour en savoir plus, consultez [Stratégies de point de terminaison](../../azure-sql/managed-instance/service-endpoint-policies-configure.md). |
|**Fonctionnalité de liaison (préversion)** | Utilisez la fonctionnalité de liaison de SQL Managed Instance pour répliquer des données de votre serveur SQL hébergé n’importe où sur Azure SQL Managed Instance et tirez parti des avantages d’Azure sans déplacer vos données vers Azure, par exemple pour déplacer vos charges de travail, assurer la reprise d’activité après sinistre ou migrer vers le cloud. Pour en savoir plus, consultez [Fonctionnalité de liaison pour SQL Managed Instance](link-feature.md). La fonctionnalité de liaison est actuellement en préversion publique limitée. |
| **Déplacer l’instance vers un autre sous-réseau GA** | Il est désormais possible de déplacer votre SQL Managed Instance vers un autre sous-réseau. Pour en savoir plus, consultez [Déplacer une instance vers un autre sous-réseau](vnet-subnet-move-instance.md). |
|**Nouvelles générations de matériel (préversion)** | Deux nouvelles générations de matériel sont désormais disponibles pour SQL Managed Instance : la série Premium et la série Premium à mémoire optimisée. Ces deux offres, qui tirent parti d’une nouvelle génération de matériel reposant sur les derniers processeurs Intel Ice Lake, se caractérisent par un rapport mémoire/vCore plus élevé qui permet de prendre en charge les applications de base de données les plus gourmandes en ressources. Dans le cadre de cette annonce, la génération de matériel Gen5 a été renommée « série Standard ». Les deux nouvelles générations de matériel Premium sont actuellement en préversion. Consultez les [limites de ressources](resource-limits.md#service-tier-characteristics) pour en savoir plus. |
| | | 


### <a name="october-2021"></a>Octobre 2021

| Modifications | Détails |
| --- | --- |
|**Répartition des nouveautés** | L’article regroupant précédemment les **nouveautés** a été divisé par produit : [Nouveautés de SQL Database](../database/doc-changes-updates-release-notes-whats-new.md) et [Nouveautés de SQL Managed Instance](doc-changes-updates-release-notes-whats-new.md). Vous pouvez identifier plus facilement les fonctionnalités actuellement en préversion et celles en disponibilité générale, ainsi que les modifications importantes apportées à la documentation. De plus, les [problèmes connus dans SQL Managed Instance](doc-changes-updates-known-issues.md) ont désormais leur propre page. | 
|  | | 


### <a name="june-2021"></a>Juin 2021

| Modifications | Détails |
| --- | --- |
|**Prise en charge de 16 To pour Usage général (préversion)** | Ajout de la prise en charge d’une allocation d’espace pouvant atteindre 16 To pour SQL Managed Instance dans le niveau de service Usage général. Consultez les [limites de ressources](resource-limits.md) pour en savoir plus. Cette offre d’instance est actuellement en préversion. | 
| **Sauvegarde en parallèle** | Il est désormais possible d’effectuer des sauvegardes en parallèle pour SQL Managed Instance dans le niveau Usage général, ce qui permet des sauvegardes plus rapides. Pour en savoir plus, consultez l’entrée de blog [Sauvegarde en parallèle pour de meilleures performances](https://techcommunity.microsoft.com/t5/azure-sql/parallel-backup-for-better-performance-in-sql-managed-instance/ba-p/2421762). |
| **Authentification Azure AD uniquement (préversion)** | Il est désormais possible de restreindre l’authentification auprès de votre instance managée Azure SQL aux utilisateurs d’Azure Active Directory. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Pour plus d’informations, consultez [Authentification avec Azure AD uniquement](../database/authentication-azure-ad-only-authentication.md). | 
| **Supervision de Resource Health** | Utilisez Resource Health pour surveiller l’état d’intégrité de votre instance gérée Azure SQL. Pour en savoir plus, consultez [Intégrité des ressources](../database/resource-health-to-troubleshoot-connectivity.md). |
| **Disponibilité générale des autorisations précises pour le masquage des données** | Les autorisations précises pour le masquage dynamique des données pour Azure SQL Managed Instance sont désormais en disponibilité générale (GA). Pour plus d’informations, consultez [Masquage dynamique des données](../database/dynamic-data-masking-overview.md#permissions). | 
|  | |


### <a name="april-2021"></a>Avril 2021

| Modifications | Détails |
| --- | --- |
| **Tables d’itinéraires définis par l’utilisateur (UDR)** | La configuration de sous-réseau assistée par le service pour Azure SQL Managed Instance utilise désormais les étiquettes de service pour les tables d’itinéraires définis par l’utilisateur (UDR). Pour en savoir plus, consultez l’[architecture de connectivité](connectivity-architecture-overview.md). |
|  |  |


### <a name="march-2021"></a>Mars 2021

| Modifications | Détails |
| --- | --- |
| **Opérations de gestion d’audit** | La capacité à auditer les opérations de SQL Managed Instance est désormais en disponibilité générale (GA). | 
| **Log Replay Service** | Il est désormais possible de migrer des bases de données de SQL Server vers Azure SQL Managed Instance à l’aide du service LRS (Log Replay Service). Pour en savoir plus, consultez [Migrer à l’aide de Log Replay Service](log-replay-service-migrate.md). Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. | 
| **Rétention des sauvegardes à long terme** | Prise en charge de la conservation des sauvegardes à long terme jusqu’à 10 ans sur Azure SQL Managed Instance. Pour en savoir plus, consultez [Rétention des sauvegardes à long terme](long-term-backup-retention-configure.md).|
| **Disponibilité générale de Machine Learning Services** | Machine Learning Services pour Azure SQL Managed Instance est désormais en disponibilité générale (GA). Pour en savoir plus, consultez [Machine Learning Services pour SQL Managed Instance](machine-learning-services-overview.md).| 
| **Fenêtre de maintenance** | Cette fonctionnalité actuellement en préversion vous permet de configurer une planification de maintenance pour votre instance gérée Azure SQL. Pour plus d’informations, consultez [Fenêtre de maintenance](../database/maintenance-window.md).|
| **Échange de messages de Service Broker** | Le composant Service Broker d’Azure SQL Managed Instance vous permet de composer vos applications à partir de services indépendants et autonomes, en fournissant la prise en charge native d’un échange de messages fiable et sécurisé entre les bases de données attachées au service. Actuellement en préversion. Pour en savoir plus, consultez [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker).
| **Insights SQL** | SQL Insights est une solution complète de surveillance de tout produit de la famille SQL Azure. SQL Insights utilise des vues de gestion dynamique pour exposer les données dont vous avez besoin pour surveiller l’intégrité, diagnostiquer les problèmes et optimiser les performances. Pour plus d’informations, consultez [SQL Insights](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

### <a name="2020"></a>2020

Les modifications suivantes ont été ajoutées à SQL Managed Instance et à la documentation en 2020 : 

| Modifications | Détails |
| --- | --- |
| **Audit des opérations de support** | L’audit de la capacité des opérations de support Microsoft vous permet d’auditer les opérations de support Microsoft lorsque vous avez besoin d’accéder à vos serveurs et/ou bases de données pendant une demande de support sur la destination de vos journaux d’audit (préversion). Pour en savoir plus, consultez [Auditer les opérations de support](../database/auditing-overview.md#auditing-of-microsoft-support-operations).|
| **Transactions élastiques** | Les transactions élastiques permettent les transactions de base de données distribuées couvrant plusieurs bases de données sur Azure SQL Database et Azure SQL Managed Instance. Des transactions élastiques ont été ajoutées pour permettre une migration sans heurts des applications existantes, ainsi que le développement d’applications mutualisées modernes reposant sur une architecture de base de données partitionnée verticalement ou horizontalement (préversion). Pour en savoir plus, consultez [Transactions distribuées](../database/elastic-transactions-overview.md#transactions-for-sql-managed-instance). | 
| **Redondance configurable du stockage de sauvegarde** | Il est désormais possible de configurer les options Stockage localement redondant (LRS) et Stockage redondant interzone (ZRS) pour la redondance du stockage de sauvegarde, ce qui offre davantage de flexibilité et de choix. Pour en savoir plus, consultez [Configurer la redondance du stockage de sauvegarde](../database/automated-backups-overview.md?tabs=managed-instance#configure-backup-storage-redundancy).|
| **Améliorations des performances des sauvegardes chiffrées par TDE** | Il est désormais possible de définir la période de rétention des sauvegardes de la restauration à un instant dans le passé (PITR), et la compression automatique des sauvegardes chiffrées à l’aide du chiffrement Transparent Data Encryption (TDE) est maintenant 30 % plus efficace dans l’utilisation de l’espace de stockage de sauvegarde, ce qui permet à l’utilisateur final de réaliser des économies. Pour en savoir plus, consultez [Modifier la PITR](../database/automated-backups-overview.md?tabs=managed-instance#change-the-short-term-retention-policy). |
| **Améliorations apportées à l’authentification Azure AD** | Automatisez la création d’utilisateurs à l’aide d’applications Azure AD et créez des utilisateurs invités Azure AD individuels (préversion). Pour en savoir plus, consultez [Lecteurs de répertoire dans Azure AD](../database/authentication-aad-directory-readers-role.md).|
| **Prise en charge mondiale de l’appairage de réseaux virtuels** | La prise en charge mondiale de l’appairage de réseaux virtuels a été ajoutée à SQL Managed Instance, améliorant ainsi l’expérience de géoréplication. Consultez [Géoréplication entre des instances gérées](../database/auto-failover-group-overview.md?tabs=azure-powershell#enabling-geo-replication-between-managed-instances-and-their-vnets). |
| **Hébergement de bases de données de catalogue SSRS** | SQL Managed Instance peut désormais héberger des bases de données de catalogue pour toutes les versions prises en charge de SQL Server Reporting Services (SSRS). | 
| **Améliorations majeures des performances** | Présentation des améliorations apportées aux performances de SQL Managed Instance, notamment l’amélioration du débit d’écriture dans le journal des transactions, l’amélioration des données et des IOPS des journaux pour les instances critiques pour l’entreprise et l’amélioration des performances de TempDB. Pour en savoir plus, consultez le blog de la communauté technique sur l’[amélioration des performances](https://techcommunity.microsoft.com/t5/azure-sql/announcing-major-performance-improvements-for-azure-sql-database/ba-p/1701256). 
| **Amélioration de l’expérience de gestion** | À l’aide de la nouvelle [API OPERATIONS](/rest/api/sql/2021-02-01-preview/managed-instance-operations), il est maintenant possible de vérifier la progression des opérations d’instance de longue durée. Pour plus d’informations, consultez [Opérations de gestion](management-operations-overview.md?tabs=azure-portal).
| **Prise en charge du Machine Learning** | Machine Learning Services avec prise en charge des langages R et Python est désormais pris en charge en préversion sur Azure SQL Managed Instance (préversion). Pour en savoir plus, consultez [Machine Learning avec SQL Managed Instance](machine-learning-services-overview.md). | 
| **Basculement initié par l’utilisateur** | Le basculement initié par l’utilisateur est désormais en disponibilité générale, ce qui vous permet d’initier manuellement un basculement automatique à l’aide de PowerShell, de commandes CLI et d’appels d’API, améliorant ainsi la résilience des applications. Pour en savoir plus, consultez [Test de la résilience](../database/high-availability-sla.md#testing-application-fault-resiliency). 
|  |  |



## <a name="known-issues"></a>Problèmes connus

Le contenu des problèmes connus a été déplacé vers un article dédié aux [problèmes connus de SQL Managed Instance](doc-changes-updates-known-issues.md). 


## <a name="contribute-to-content"></a>Contribuer au contenu

Pour contribuer à la documentation Azure SQL, consultez le [guide du contributeur Docs](/contribute/).
