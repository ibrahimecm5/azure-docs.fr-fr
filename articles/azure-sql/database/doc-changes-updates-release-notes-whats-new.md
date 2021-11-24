---
title: Nouveautés
titleSuffix: Azure SQL Database
description: Découvrez les nouvelles fonctionnalités et améliorations apportées à la documentation d’Azure SQL Database.
services: sql-database
author: MashaMSFT
ms.author: mathoma
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=2, references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: e57adde4df10a942baff7b7032706a991d46050c
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556170"
---
# <a name="whats-new-in-azure-sql-database"></a>Nouveautés d’Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article récapitule les modifications apportées à la documentation en lien avec les nouvelles fonctionnalités et les améliorations introduites dans les versions récentes d’[Azure SQL Database](https://azure.microsoft.com/products/azure-sql/database/). Pour en savoir plus sur Azure SQL Database, consultez la [vue d’ensemble](sql-database-paas-overview.md). 

Pour Azure SQL Managed Instance, consultez les [nouveautés](../managed-instance/doc-changes-updates-release-notes-whats-new.md).

> [!div class="nextstepaction"]
> [Enquête pour améliorer Azure SQL !](https://aka.ms/AzureSQLSurveyNov2021)



## <a name="preview"></a>Préversion 

Le tableau suivant liste les fonctionnalités d’Azure SQL Database actuellement en préversion : 

| Fonctionnalité | Détails |
| ---| --- |
| [Modifier la capture de données](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | La capture des changements de données (CDC) vous permet de suivre toutes les modifications apportées à une base de données. Bien que cette fonctionnalité soit disponible pour SQL Server depuis un certain temps, son utilisation avec Azure SQL Database est actuellement en préversion. |
| [Tâches élastiques](elastic-jobs-overview.md) | Cette fonctionnalité remplace SQL Server Agent pour Azure SQL Database sous la forme d’une offre PaaS.  |
| [Requêtes élastiques](elastic-query-overview.md) | Cette fonctionnalité permet d’exécuter des requêtes sur plusieurs bases de données dans Azure SQL Database. |
| [Transactions élastiques](elastic-transactions-overview.md) | Les transactions élastiques vous permettent d’exécuter des transactions distribuées entre plusieurs bases de données cloud dans Azure SQL Database. |
| [Registre](ledger-overview.md) | Cette fonctionnalité d’Azure SQL Database vous permet d’attester cryptographiquement auprès d’autres parties, comme des auditeurs ou d’autres professionnels, que vos données n’ont pas été falsifiées. | 
| [Fenêtre de maintenance](maintenance-window.md)| Cette fonctionnalité vous permet de configurer une planification de maintenance pour votre base de données Azure SQL. |
| [Éditeur de requête dans le portail Azure](connect-query-portal.md) | L’éditeur de requête du portail vous permet d’exécuter des requêtes sur votre base de données Azure SQL directement à partir du [portail Azure](https://portal.azure.com).|
| [Indicateurs du Magasin des requêtes](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true) | Utilisez des indicateurs de requête pour optimiser l’exécution de vos requêtes avec la clause OPTION. |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md)|Azure SQL Analytics est une solution cloud avancée permettant de superviser dans un seul affichage les performances de toutes vos bases de données Azure SQL à grande échelle et sur plusieurs abonnements. Azure SQL Analytics collecte et visualise des métriques de performances clés à l’aide d’une intelligence intégrée pour résoudre les problèmes de performances.|
| [Insights SQL](../../azure-monitor/insights/sql-insights-overview.md) |  SQL Insights est une solution complète de surveillance de tout produit de la famille SQL Azure. SQL Insights utilise des vues de gestion dynamique pour exposer les données dont vous avez besoin pour surveiller l’intégrité, diagnostiquer les problèmes et optimiser les performances.|
| [Configuration redondante interzone pour le niveau Usage général](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | Cette fonctionnalité utilise [Zones de disponibilité Azure](../../availability-zones/az-overview.md#availability-zones) pour répliquer des bases de données sur plusieurs emplacements physiques au sein d’une région Azure. En sélectionnant la [redondance de zone](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview), vous pouvez rendre vos bases de données à usage général et vos pools élastiques résilients à un ensemble de défaillances beaucoup plus important, notamment les pannes graves de centre de données, sans aucune modification de la logique d’application. La fonctionnalité n’est actuellement disponible que dans le niveau Usage général. | 
|||

## <a name="general-availability-ga"></a>Disponibilité générale

Le tableau suivant liste les fonctionnalités d’Azure SQL Database qui sont passées de la préversion à la disponibilité générale (GA) au cours des 12 derniers mois : 

| Fonctionnalité | Mois en GA | Détails |
| ---| --- |--- |
| [Authentification Azure Active Directory uniquement](authentication-azure-ad-only-authentication.md) | Novembre 2021 | Il est possible de configurer Azure SQL Database pour autoriser uniquement l’authentification Azure Active Directory. | 
| [Principal de service AAD](authentication-aad-service-principal.md) |  Septembre 2021 | Azure Active Directory (Azure AD) prend en charge la création d’utilisateurs dans Azure SQL Database pour le compte d’applications Azure AD (principaux de service).| 
| [Opérations de gestion d’audit](../database/auditing-overview.md#auditing-of-microsoft-support-operations) |  Mars 2021 | Les fonctionnalités d’audit d’Azure SQL vous permettent d’auditer les opérations effectuées par les ingénieurs du support technique de Microsoft quand ils accèdent à vos ressources SQL au cours d’une demande de support, améliorant ainsi la transparence avec vos employés. | 
|||| 


## <a name="documentation-changes"></a>Modifications apportées à la documentation

Découvrez les modifications importantes apportées à la documentation d’Azure SQL Database.

### <a name="november-2021"></a>Novembre 2021

| Modifications | Détails |
| --- | --- |
| **Authentification avec Azure AD uniquement** | La fonctionnalité permettant de restreindre l’authentification auprès de votre base de données Azure SQL aux utilisateurs d’Azure Active Directory est désormais en disponibilité générale. Pour plus d’informations, consultez [Authentification avec Azure AD uniquement](../database/authentication-azure-ad-only-authentication.md). | 
| | | 


### <a name="october-2021"></a>Octobre 2021

| Modifications | Détails |
| --- | --- |
|**Répartition des nouveautés** | L’article regroupant précédemment les **nouveautés** a été divisé par produit : [Nouveautés de SQL Database](doc-changes-updates-release-notes-whats-new.md) et [Nouveautés de SQL Managed Instance](../managed-instance/doc-changes-updates-release-notes-whats-new.md). Vous pouvez identifier plus facilement les fonctionnalités actuellement en préversion et celles en disponibilité générale, ainsi que les modifications importantes apportées à la documentation. De plus, les [problèmes connus dans SQL Managed Instance](../managed-instance/doc-changes-updates-known-issues.md) ont désormais leur propre page.  | 

### <a name="september-2021"></a>Septembre 2021

| Modifications | Détails |
| --- | --- |
| **Prise en charge des fenêtres de maintenance pour les zones de disponibilité** | Vous pouvez désormais utiliser la [fonctionnalité Fenêtre de maintenance](maintenance-window.md) si votre base de données Azure SQL est déployée dans une zone de disponibilité. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.  | 
|||


### <a name="july-2021"></a>Juillet 2021

| Modifications | Détails |
| --- | --- |
| **Authentification avec Azure AD uniquement** | Il est désormais possible de restreindre l’authentification auprès de votre base de données Azure SQL aux utilisateurs d’Azure Active Directory. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Pour plus d’informations, consultez [Authentification avec Azure AD uniquement](authentication-azure-ad-only-authentication.md). | 
|||

### <a name="june-2021"></a>Juin 2021

| Modifications | Détails |
| --- | --- |
| **Indicateurs du Magasin des requêtes** | Il est désormais possible d’utiliser des indicateurs de requête pour optimiser l’exécution de vos requêtes avec la clause OPTION. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Pour plus d’informations, consultez [Indicateurs du Magasin des requêtes](/sql/relational-databases/performance/query-store-hints?view=azuresqldb-current&preserve-view=true). | 
|||

### <a name="may-2021"></a>Mai 2021

| Modifications | Détails |
| --- | --- |
| **Modifier la capture de données** | La capture des changements de données (CDC) avec Azure SQL Database est désormais en préversion. Pour plus d’informations, consultez [Capture des changements de données](/sql/relational-databases/track-changes/about-change-data-capture-sql-server). | 
| **Registre SQL Database** | Le registre SQL Database, désormais en préversion, vous permet d’attester cryptographiquement auprès d’autres parties, comme des auditeurs ou d’autres professionnels, que vos données n’ont pas été falsifiées. Pour plus d’informations, consultez [Registre](ledger-overview.md). | 
|||

### <a name="march-2021"></a>Mars 2021

| Modifications | Détails |
| --- | --- |
 | **Fenêtre de maintenance** | Cette fonctionnalité actuellement en préversion vous permet de configurer une planification de maintenance pour votre base de données Azure SQL. Pour plus d’informations, consultez [Fenêtre de maintenance](maintenance-window.md).|
| **Insights SQL** | SQL Insights est une solution complète de surveillance de tout produit de la famille SQL Azure. SQL Insights utilise des vues de gestion dynamique pour exposer les données dont vous avez besoin pour surveiller l’intégrité, diagnostiquer les problèmes et optimiser les performances. Pour plus d’informations, consultez [Insights SQL](../../azure-monitor/insights/sql-insights-overview.md). | 
||| 

## <a name="contribute-to-content"></a>Contribuer au contenu

Pour contribuer à la documentation Azure SQL, consultez le [guide du contributeur Docs](/contribute/).
