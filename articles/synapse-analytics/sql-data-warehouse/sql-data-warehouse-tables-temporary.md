---
title: tables temporaires ;
description: Conseils de base pour l’utilisation de tables temporaires dans le pool SQL dédié mettant en évidence les principes des tables temporaires au niveau de la session.
services: synapse-analytics
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/02/2021
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: b63b6f017771325dd752905502bd8feb6479f5c4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500713"
---
# <a name="temporary-tables-in-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tables temporaires dans un pool SQL dédié dans Azure Synapse Analytics

Cet article contient des conseils de base pour l’utilisation des tables temporaires et met en évidence les principes des tables temporaires au niveau de la session. 

L’utilisation des informations de cet article peut vous aider à modulariser votre code, améliorant ainsi sa réutilisabilité et sa facilité de maintenance.

## <a name="what-are-temporary-tables"></a>Qu’est-ce que les tables temporaires ?

Les tables temporaires sont utiles lors du traitement des données, notamment lors d’une transformation lorsque les résultats intermédiaires sont temporaires. Dans le pool SQL dédié, les tables temporaires existent au niveau de la session.  

Elles sont visibles seulement pour la session dans laquelle elles ont été créées et sont automatiquement supprimées quand cette session se ferme.  

Les tables temporaires offrent un gain de performances, car leurs résultats sont écrits en local et non dans un stockage distant.

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tables temporaires dans le pool SQL dédié

Dans la ressource de pool SQL dédié, les tables temporaires offrent un gain de performances, car leurs résultats sont écrits en local et non dans un stockage distant.

### <a name="create-a-temporary-table"></a>Créer une table temporaire

Les tables temporaires sont créées en faisant simplement précéder le nom de votre table de `#`.  Par exemple :

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Vous pouvez également utiliser `CTAS` pour créer des tables temporaires à l’aide de la même approche :

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` est une commande puissante et présente l’avantage d’être efficace dans son utilisation de l’espace de journal des transactions. 

## <a name="drop-temporary-tables"></a>Déposer des tables temporaires
Lorsqu’une nouvelle session est créée, aucune table temporaire ne doit exister.  

Si vous appelez la même procédure stockée qui crée une table temporaire avec le même nom, pour vous assurer de la réussite de vos instructions `CREATE TABLE`, une simple vérification d’existence préalable avec `DROP` peut être utilisée comme dans l’exemple suivant :

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Pour la cohérence de codage, il convient d’utiliser ce modèle pour les tables et les tables temporaires.  Il est également judicieux d’utiliser `DROP TABLE` pour supprimer les tables temporaires lorsque vous avez fini de les utiliser dans votre code.  

Dans le développement de procédure stockée, il est courant de voir les commandes de suppression regroupées à la fin d’une procédure pour s’assurer que ces objets sont nettoyés.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularize-code"></a>Modulariser le code
Étant donné que les tables temporaires peuvent être visibles à n’importe quel point d’une session utilisateur, cette fonctionnalité peut vous aider à modulariser le code de votre application.  

Par exemple, la procédure stockée suivante génère le langage DDL pour mettre à jour toutes les statistiques dans la base de données par nom de statistique.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    #stats_ddl
;
GO
```

À ce stade, la seule action qui s’est produite est la création d’une procédure stockée qui génère une table temporaire, `#stats_ddl`, avec des instructions DDL.  

Cette procédure stockée supprime une table `#stats_ddl` existante pour ne pas provoquer un échec si elle est exécutée plusieurs fois dans une session.  

Toutefois, étant donné l’absence de `DROP TABLE` à la fin de la procédure stockée, lorsque la procédure stockée se termine, elle quitte la table créée afin de pouvoir être lue en dehors de la procédure stockée.  

Dans le pool SQL dédié, contrairement à d’autres bases de données SQL Server, il est possible d’utiliser la table temporaire en dehors de la procédure qui l’a créée.  Les tables temporaires du pool SQL dédié sont utilisables à **n’importe quel point** de la session. Cette fonctionnalité peut optimiser la facilité de gestion et la modularité du code comme dans l’exemple suivant :

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitations relatives aux tables temporaires
Le pool SQL dédié impose quelques restrictions lors de l’implémentation de tables temporaires.  Actuellement, seules les tables temporaires de la session sont prises en charge.  Les tables temporaires globales ne sont pas prises en charge.  

Par ailleurs, il n’est pas possible de créer des vues sur des tables temporaires.  Les tables temporaires peuvent être créées uniquement avec une distribution par hachage ou par tourniquet (round robin).  La distribution de tables temporaires répliquées n’est pas prise en charge. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le développement de tables, consultez l’article [Conception de tables à l’aide d’un pool SQL dédié](sql-data-warehouse-tables-overview.md).

