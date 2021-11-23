---
title: Découverte et classification des données
description: Découverte et classification des données pour Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/24/2021
tags: azure-synapse
ms.openlocfilehash: 4fd6360d1d549cd5c184dd5a1f3105d238ab9319
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335800"
---
# <a name="data-discovery--classification"></a>Découverte et classification des données
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

L’option Découverte et classification des données est conçue pour Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics. Elle offre des fonctionnalités de base pour la découverte, la classification, l’étiquetage et le signalement des données sensibles dans vos bases de données.

Vos données les plus sensibles peuvent inclure des informations commerciales, financières, médicales ou personnelles. Elles peuvent servir d’infrastructure pour :

- Contribuer à respecter les normes en matière de confidentialité des données et d’exigences pour la conformité réglementaire.
- Différents scénarios de sécurité, comme la supervision (audit) de l’accès à des données sensibles
- Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.

> [!NOTE]
> Pour plus d’informations sur SQL Server local, consultez [Découverte et classification de données SQL](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Présentation de Découverte et classification des données

La fonction Découverte et classification des données prend actuellement en charge les fonctionnalités suivantes :

- **Découverte et recommandations :** Le moteur de classification analyse votre base de données et identifie les colonnes contenant des données potentiellement sensibles. Il vous offre ensuite un moyen simple d’examiner et d’appliquer la classification recommandée via le portail Azure.

- **Étiquetage :** Vous pouvez appliquer des étiquettes de classification de sensibilité de manière permanente aux colonnes à l’aide de nouveaux attributs de métadonnées qui ont été ajoutés au moteur de base de données SQL Server. Vous pouvez ensuite utiliser ces métadonnées pour des scénarios d’audit basés sur la sensibilité.

- **Sensibilité du jeu de résultats de requête :** La sensibilité du jeu de résultats de requête est calculée en temps réel à des fins d’audit.

- **Visibilité :** Vous pouvez afficher l’état de classification de la base de données dans un tableau de bord détaillé dans le portail Azure. En outre, vous pouvez télécharger un rapport au format Excel pour l’utiliser à des fins de conformité et d’audit, et d’autres besoins.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Découvrir, classer et étiqueter des colonnes contenant des données sensibles

Cette section décrit les étapes pour :

- Découvrir, classer et étiqueter les colonnes qui contiennent des données sensibles dans votre base de données.
- Afficher l’état actuel de la classification de votre base de données et exporter des rapports.

La classification comprend deux attributs de métadonnées :

- **Étiquettes** : Les principaux attributs de classification utilisés pour définir le niveau de sensibilité des données stockées dans la colonne.  
- **Types d'informations** : Attributs qui fournissent des informations plus granulaires sur le type des données stockées dans la colonne.

### <a name="define-and-customize-your-classification-taxonomy"></a>Définir et personnaliser votre taxonomie de classification

La fonctionnalité Découverte et classification des données comprend un ensemble intégré d’étiquettes de sensibilité, ainsi qu’un ensemble intégré de types d’informations et de logiques de découverte. Vous pouvez personnaliser cette taxonomie, et de définir un jeu et un classement de constructions de classification spécialement pour votre environnement.

Vous définissez et personnalisez votre taxonomie de classification dans un emplacement central pour l’ensemble de votre organisation Azure. Cet emplacement se trouve dans [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md), dans le cadre de votre stratégie de sécurité. Seules les personnes disposant de droits d’administration pour le groupe d’administration racine de l’organisation peuvent effectuer cette tâche.

Dans le cadre de la gestion des stratégies, vous pouvez définir des étiquettes personnalisées, les classer et les associer à un ensemble de types d’informations. Vous pouvez également ajouter vos propres types d’informations personnalisés et les configurer avec des modèles de chaîne. Les modèles sont ajoutés à la logique de détection pour identifier ce type de données dans vos bases de données.

Pour plus d’informations, consultez [Personnaliser la stratégie de protection des informations SQL dans Microsoft Defender pour le cloud (préversion)](../../security-center/security-center-info-protection-policy.md).

Une fois la stratégie au niveau de l’organisation définie, vous pouvez continuer à classer les bases de données individuelles à l’aide de votre stratégie personnalisée.

### <a name="classify-your-database"></a>Classer votre base de données

> [!NOTE]
> L’exemple ci-dessous utilise Azure SQL Database, mais vous devez sélectionner le produit dans lequel vous souhaitez configurer Découverte et classification des données.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Accédez à **Découverte et classification des données** sous l’en-tête **Sécurité** du volet Azure SQL Database. L’onglet Vue d’ensemble contient un résumé de l’état actuel de la classification de la base de données. Le résumé comprend une liste détaillée de toutes les colonnes classifiées, que vous pouvez également filtrer pour afficher uniquement des parties de schéma, des types d’informations et des étiquettes spécifiques. Si vous n’avez pas encore classifié de colonne, [passez à l’étape 4](#step-4).

    ![Vue d’ensemble](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Pour télécharger un rapport au format Excel, sélectionnez **Exporter** dans le menu supérieur du volet.

1. <a id="step-4"></a>Pour commencer à classer vos données, sélectionnez l’onglet **Classification** de la page **Découverte et classification des données**.

    Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de classifications de colonnes recommandées.

1. Afficher et appliquer les recommandations de classification :

   - Pour afficher la liste des classifications de colonnes recommandées, sélectionnez le panneau de recommandations en bas du volet.

   - Pour accepter une recommandation associée à une colonne spécifique, sélectionnez la case dans la colonne de gauche de la ligne concernée. Pour marquer toutes les recommandations comme étant acceptées, activez la case à cocher la plus à gauche dans l’en-tête de table de recommandations.

   - Pour appliquer les recommandations sélectionnées, sélectionnez **Accepter les recommandations sélectionnées**.

   ![Recommandations relatives à la classification](./media/data-discovery-and-classification-overview/recommendation.png)

1. Vous pouvez aussi, en guise d’alternative, classifier manuellement des colonnes ou, en plus de la classification basée sur les recommandations :

   1. Sélectionnez **Ajouter une classification** dans le menu en haut du volet.

   1. Dans la fenêtre contextuelle qui apparaît, sélectionnez le schéma, la table et la colonne que vous souhaitez classifier, ainsi que l’étiquette de sensibilité et le type d’informations.

   1. Sélectionnez **Ajouter une classification** en bas de la fenêtre contextuelle.

   ![Ajouter manuellement une classification](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Pour terminer votre classification et étiqueter de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification, sélectionnez **Enregistrer** dans la page **Classification**.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Audit de l’accès aux données sensibles

Un aspect important de la classification est la possibilité de superviser l’accès aux données sensibles. [L’audit Azure SQL](../../azure-sql/database/auditing-overview.md) a été amélioré pour inclure un nouveau champ nommé `data_sensitivity_information` dans le journal d’audit. Ce champ enregistre les classifications (étiquettes) de sensibilité des données retournées par une requête. Voici un exemple :

[![Journal d’audit](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png#lightbox)

Ce sont les activités qui peuvent être auditées à l’aide des informations de sensibilité :
- ALTER TABLE ... DROP COLUMN
- BULK INSERT
- Suppression
- INSERT
- MERGE
- UPDATE
- UPDATETEXT
- WRITETEXT
- DROP TABLE
- BACKUP
- DBCC CloneDatabase
- SELECT INTO
- INSERT INTO EXEC
- TRUNCATE TABLE
- DBCC SHOW_STATISTICS
- sys.dm_db_stats_histogram

Utilisez [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql) pour renvoyer des informations à partir d’un fichier d’audit stocké dans un compte stockage Azure.

## <a name="permissions"></a><a id="permissions"></a>Autorisations

Ces rôles intégrés peuvent lire la classification des données d’une base de données :

- Propriétaire
- Lecteur
- Contributeur
- Gestionnaire de sécurité SQL
- Administrateur de l'accès utilisateur

Voici les actions requises pour lire la classification des données d’une base de données :

- Microsoft.Sql/servers/databases/currentSensitivityLabels/*
- Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*
- Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*

Ces rôles intégrés peuvent modifier la classification des données d’une base de données :

- Propriétaire
- Contributeur
- Gestionnaire de sécurité SQL

Voici l’action requise pour modifier la classification des données d’une base de données :

- Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*

En savoir plus sur les autorisations en fonction du rôle dans [Azure RBAC](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a>Gérer les classifications

Vous pouvez utiliser T-SQL, une API REST ou PowerShell pour gérer les classifications.

### <a name="use-t-sql"></a>Utiliser T-SQL

Vous pouvez utiliser T-SQL pour ajouter/supprimer des classifications de colonne, ainsi que pour récupérer toutes les classifications pour la base de données entière.

> [!NOTE]
> Quand vous utilisez T-SQL pour gérer les étiquettes, aucune validation n’est effectuée pour vérifier que les étiquettes que vous ajoutez à une colonne existent dans la stratégie de protection des informations de l’organisation (ensemble des étiquettes qui apparaissent dans les recommandations du portail). C’est donc à vous de valider.

Pour plus d’informations sur l’utilisation de T-SQL pour les classifications, consultez les références suivantes :

- Pour ajouter/mettre à jour la classification d’une ou plusieurs colonnes : [ADD SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Pour supprimer la classification d’une ou plusieurs colonnes : [DROP SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Pour voir toutes les classifications sur la base de données : [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Utiliser des cmdlets PowerShell
Gérez les classifications et les recommandations pour Azure SQL Database et Azure SQL Managed Instance à l'aide de PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Cmdlets PowerShell pour Azure SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Cmdlets PowerShell pour Azure SQL Managed Instance

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Utiliser l’API REST

Vous pouvez utiliser l’API REST pour gérer par programme les classifications et les recommandations. L’API REST publiée prend en charge les opérations suivantes :

- [Créer ou mettre à jour](/rest/api/sql/sensitivitylabels/createorupdate) : Crée ou met à jour l’étiquette de sensibilité de la colonne spécifiée.
- [Supprimer](/rest/api/sql/sensitivitylabels/delete) : Supprime l’étiquette de sensibilité de la colonne spécifiée.
- [Désactiver les recommandations](/rest/api/sql/sensitivitylabels/disablerecommendation) : Désactive les recommandations de sensibilité sur la colonne spécifiée.
- [Activer une recommandation](/rest/api/sql/sensitivitylabels/enablerecommendation) : Active les recommandations de sensibilité sur la colonne spécifiée. (Les recommandations sont activées par défaut sur toutes les colonnes.)
- [Obtenir](/rest/api/sql/sensitivitylabels/get) : Obtient l’étiquette de sensibilité de la colonne spécifiée.
- [Afficher la liste actuelle par base de données](/rest/api/sql/sensitivitylabels/listcurrentbydatabase) : Obtient les étiquettes de sensibilité actuelles de la base de données spécifiée.
- [Liste recommandée par base de données](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) : Obtient les étiquettes de sensibilité recommandées pour la base de données spécifiée.

## <a name="retrieve-classifications-metadata-using-sql-drivers"></a>Récupérer les métadonnées de classification à l’aide de pilotes SQL

Vous pouvez utiliser les pilotes SQL suivants pour récupérer les métadonnées de classification :

- [Pilote ODBC](/sql/connect/odbc/data-classification)
- [Pilote OLE DB](/sql/connect/oledb/features/using-data-classification)
- [Pilote JDBC](/sql/connect/jdbc/data-discovery-classification-sample)
- [Pilotes Microsoft SQL Server pour PHP](/sql/connect/php/release-notes-php-sql-driver)

## <a name="faq---advanced-classification-capabilities"></a>Questions fréquentes (FAQ) - Fonctionnalités de classification avancées

**Question** : [Azure Purview](../../purview/overview.md) remplacera-t-il la fonctionnalité Découverte et classification des données SQL ou celle-ci sera-t-elle bientôt mise hors service ?
**Réponse** : Nous continuons à prendre en charge la fonctionnalité Découverte et classification des données SQL et vous encourageons à adopter [Azure Purview](../../purview/overview.md), qui offre des fonctionnalités plus riches en termes de classification avancée et de gouvernance des données. Si nous décidons de retirer un service, une fonctionnalité, une API ou une référence SKU, vous recevez des notifications préalables indiquant comment opérer une migration ou une transition. Découvrez-en plus sur les stratégies de cycle de vie Microsoft ici.

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez configurer [l’audit Azure SQL](../../azure-sql/database/auditing-overview.md) pour effectuer la surveillance et l’audit de l’accès à vos données sensibles classifiées.
- Pour une présentation incluant la découverte et la classification des données, consultez [Découverte, classification, étiquetage et protection des données SQL | Données exposées](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Pour classifier vos bases de données Azure SQL et Azure Synapse Analytics avec des étiquettes Azure Purview à l’aide de commandes T-SQL, consultez [Classifier vos données Azure SQL à l’aide d’étiquettes Azure Purview](../../sql-database/scripts/sql-database-import-purview-labels.md).
