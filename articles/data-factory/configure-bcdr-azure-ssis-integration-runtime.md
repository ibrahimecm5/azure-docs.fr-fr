---
title: Configurer un runtime d’intégration Azure-SSIS pour la continuité d’activité et reprise d’activité (BCDR)
description: Cet article explique comment configurer un runtime d’intégration Azure-SSIS dans Azure Data Factory avec un groupe de basculement Azure SQL Database/Managed Instance pour la continuité d’activité et reprise d’activité (BCDR).
services: data-factory
ms.service: data-factory
ms.subservice: integration-services
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: 4ed454ae92fff2a0a5bfd6437b0ee1ef10d194c0
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846232"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Configurer un runtime d’intégration Azure-SSIS pour la continuité d’activité et reprise d’activité (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Vous pouvez combiner Azure SQL Database/Managed Instance et SQL Server Integration Services (SSIS) dans Azure Data Factory (ADF) en tant que solution PaaS recommandée pour une migration SQL Server. Vous pouvez déployer vos projets SSIS dans une base de données de catalogues SSIS (SSISDB) hébergée par Azure SQL Database/Managed Instance, et exécuter vos packages SSIS sur le runtime d’intégration (IR) Azure SSIS dans ADF.

Pour la continuité d’activité et reprise d’activité (BCDR), vous pouvez configurer Azure SQL Database/Managed Instance avec un [groupe de géo-réplication/basculement](../azure-sql/database/auto-failover-group-overview.md), où la SSISDB dans une région Azure primaire avec accès en lecture-écriture (rôle principal) sera répliquée en continu vers une région secondaire avec accès en lecture seule (rôle secondaire). Quand un incident se produit dans la région primaire, un basculement est déclenché, de sorte que les SSISDB primaire et secondaire échangent leur rôles.

Pour BCDR, vous pouvez également configurer une paire de runtimes d’intégration Azure-SSIS de secours, qui fonctionne en synchronisation avec le groupe de basculement Azure SQL Database/Managed Instance. Cela vous permet d’avoir une paire de runtimes d’intégration Azure-SSIS opérationnels. À tout moment, un seul runtime peut accéder à la SSISDB primaire pour extraire et exécuter des packages, ainsi qu’écrire des journaux d’exécution de package (rôle principal), tandis que l’autre ne peut faire de même que pour des packages déployés ailleurs, par exemple dans Azure Files (rôle secondaire). Lors du basculement de SSISDB, les runtimes d’intégration Azure-SSIS primaire et secondaire échangent également leurs rôles et, si les deux sont en cours d’exécution, le temps d’arrêt est presque nul.

Cet article explique comment configurer un runtime d’intégration Azure-SSIS avec un groupe de basculement Azure SQL Database/Managed Instance pour BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Configurer une paire de runtimes d’intégration Azure-SSIS de secours avec un groupe de basculement Azure SQL Database

Pour configurer une paire de runtimes d’intégration Azure-SSIS de secours qui fonctionne en synchronisation avec un groupe de basculement Azure SQL Database, procédez comme suit.

1. À l’aide de l’interface utilisateur du portail Azure ou d’ADF, vous pouvez créer un runtime d’intégration Azure-SSIS (Azure-SSIS IR) avec votre serveur Azure SQL Database principal pour héberger la SSISDB dans la région primaire. Si vous disposez déjà d’un Azure-SSIS IR attaché à la SSIDB hébergée par votre serveur Azure SQL Database principal, et qu’il est toujours en cours d’exécution, vous devez l’arrêter avant de le reconfigurer. Ce sera votre Azure-SSIS IR principal.

   Quand [vous choisissez d’utiliser une SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, activez également la case à cocher **Utiliser la paire de runtimes d’intégration Azure-SSIS de secours avec le basculement de SSISDB**. Pour **Nom de la paire de secours**, entrez un nom pour identifier votre paire de runtimes d’intégration Azure-SSIS primaire et secondaire. Lorsque vous terminez la création de votre Azure-SSIS IR principal, celui-ci est démarré et attaché à une SSISDB principale qui sera créée pour vous avec un accès en lecture-écriture. Si vous venez de le reconfigurer, vous devez le redémarrer.

1. Le portail Azure vous permet de vérifier si la SSISDB principale a été créée dans la page **Vue d’ensemble** de votre serveur Azure SQL Database principal. Une fois la base de données créée, vous pouvez [créer un groupe de basculement pour vos serveurs Azure SQL Database principal et secondaire et y ajouter la SSISDB](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group) sur la page **Groupes de basculement**. Une fois votre groupe de basculement créé, vous pouvez vérifier si la SSISDB principale a été répliquée sur un groupe de basculement secondaire avec un accès en lecture seule sur la page **Vue d’ensemble** de votre serveur Azure SQL Database secondaire.

1. L’interface utilisateur du portail Azure ou d’ADF vous permet de créer un autre Azure-SSIS IR avec votre serveur Azure SQL Database secondaire pour héberger la SSISDB dans la région secondaire. Ce sera votre Azure-SSIS IR secondaire. Pour une BCDR complète, assurez-vous que toutes les ressources dont elle dépend sont également créées dans la région secondaire, par exemple, Stockage Azure pour le stockage des scripts/fichiers de configuration personnalisés, ADF pour l’orchestration/planification des exécutions de package, etc.

   Quand [vous choisissez d’utiliser une SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, activez également la case à cocher **Utiliser la paire de runtimes d’intégration Azure-SSIS de secours avec le basculement de SSISDB**. Pour le **Nom de la paire de secours**, entrez le même nom pour identifier votre paire de runtimes d’intégration Azure-SSIS primaire et secondaire. Lorsque vous terminez la création de votre Azure-SSIS IR secondaire, celui-ci est démarré et attaché à la SSISDB secondaire.

1. Si vous souhaitez que le temps d’arrêt occasionné par le basculement de la SSISDB soit proche de zéro, assurez-vous que les deux runtimes d’intégration Azure-SSIS sont en cours d’exécution. Seul votre Azure-SSIS IR principal peut accéder à la SSISDB principale pour extraire et exécuter des packages, ainsi que pour écrire des journaux d’exécution de package, tandis que votre Azure-SSIS IR secondaire ne peut le faire que pour des packages déployés ailleurs, par exemple, dans Azure Files.

   Si vous souhaitez réduire le coût d’exécution, vous pouvez arrêter votre Azure-SSIS IR secondaire après sa création. Quand le basculement de SSISDB se produit, vos runtimes d’exécution Azure-SSIS principal et secondaire échangent leurs rôles. Si votre Azure-SSIS IR principal est arrêté, vous devez le redémarrer. Selon qu’il est injecté dans un réseau virtuel et la méthode d’injection utilisée, son exécution prend entre 5 minutes ou de 20 à 30 minutes.

1. Si vous [utilisez ADF pour l’orchestration et la planification d’exécutions de package](./how-to-invoke-ssis-package-ssis-activity.md), assurez-vous que tous les pipelines ADF appropriés, avec les activités Exécuter le Package SSIS et les déclencheurs associés, sont copiés vers votre ADF secondaire avec les déclencheurs initialement désactivés. Lorsque le basculement de SSISDB se produit, vous devez les activer.

1. Vous pouvez [tester votre groupe de basculement Azure SQL Database](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover) et vérifier sur la [page de surveillance de l’Azure-SSIS IR dans le portail ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) si vos runtimes d’intégration Azure-SSIS principal et secondaire ont échangé leurs rôles. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Configurer une paire de runtimes d’intégration Azure-SSIS IR de secours avec un groupe de basculement Azure SQL Managed Instance

Pour configurer une paire de runtimes d’intégration Azure-SSIS IR de secours qui fonctionne en synchronisation avec un groupe de basculement Azure SQL Managed Instance, procédez comme suit.

1. La portail Azure vous permet de [créer un groupe de basculement pour vos instances managées Azure SQL principale et secondaire](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal) sur la page **Groupes de basculement** de votre Azure SQL Managed Instance principale.

1. L’interface utilisateur du portail Azure ou d’ADF vous permet de créer un Azure-SSIS IR avec votre Azure SQL Managed Instance principale pour héberger la SSISDB dans la région primaire. Si vous disposez déjà d’un Azure-SSIS IR attaché à la SSIDB hébergée par votre Azure SQL Managed Instance principale, et qu’il est toujours en cours d’exécution, vous devez l’arrêter avant de le reconfigurer. Ce sera votre Azure-SSIS IR principal.

   Quand [vous choisissez d’utiliser une SSISDB](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb) dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, activez également la case à cocher **Utiliser la paire de runtimes d’intégration Azure-SSIS de secours avec le basculement de SSISDB**. Pour **Nom de la paire de secours**, entrez un nom pour identifier votre paire de runtimes d’intégration Azure-SSIS primaire et secondaire. Lorsque vous terminez la création de votre Azure-SSIS IR principal, celui-ci est démarré et attaché à une SSISDB principale qui sera créée pour vous avec un accès en lecture-écriture. Si vous venez de le reconfigurer, vous devez le redémarrer. Vous pouvez également vérifier si la SSISDB principale a été répliquée sur un groupe de basculement secondaire avec un accès en lecture seule sur la page **Vue d’ensemble** de votre Azure SQL Managed Instance.

1. L’interface utilisateur du portail Azure ou d’ADF vous permet de créer un autre Azure-SSIS IR avec votre Azure SQL Managed Instance secondaire pour héberger la SSISDB dans la région secondaire. Ce sera votre Azure-SSIS IR secondaire. Pour une BCDR complète, assurez-vous que toutes les ressources dont elle dépend sont également créées dans la région secondaire, par exemple, Stockage Azure pour le stockage des scripts/fichiers de configuration personnalisés, ADF pour l’orchestration/planification des exécutions de package, etc.

   Quand [vous choisissez d’utiliser une SSISDB](./create-azure-ssis-integration-runtime-portal.md#creating-ssisdb) dans la page **Paramètres de déploiement** du volet **Installation du runtime d’intégration**, activez également la case à cocher **Utiliser la paire de runtimes d’intégration Azure-SSIS de secours avec le basculement de SSISDB**. Pour le **Nom de la paire de secours**, entrez le même nom pour identifier votre paire de runtimes d’intégration Azure-SSIS primaire et secondaire. Lorsque vous terminez la création de votre Azure-SSIS IR secondaire, celui-ci est démarré et attaché à la SSISDB secondaire.

1. Azure SQL Managed Instance peut sécuriser des données sensibles dans des bases de données, telles que SSISDB, en les chiffrant à l’aide de la clé principale de base de données (DMK). Celle-ci est à son tour chiffrée à l’aide de la clé principale du service (SMK) par défaut. Depuis septembre 2021, SMK est répliqué à partir de votre Azure SQL Managed Instance principale vers votre instance secondaire lors de la création d'un groupe de basculement. Si votre groupe de basculement a été créé avant, supprimez toutes les bases de données utilisateur, y compris SSISDB, de votre Azure SQL Managed Instance secondaire, puis recréez votre groupe de basculement.

1. Si vous souhaitez que le temps d’arrêt occasionné par le basculement de la SSISDB soit proche de zéro, assurez-vous que les deux runtimes d’intégration Azure-SSIS sont en cours d’exécution. Seul votre Azure-SSIS IR principal peut accéder à la SSISDB principale pour extraire et exécuter des packages, ainsi que pour écrire des journaux d’exécution de package, tandis que votre Azure-SSIS IR secondaire ne peut le faire que pour des packages déployés ailleurs, par exemple, dans Azure Files.

   Si vous souhaitez réduire le coût d’exécution, vous pouvez arrêter votre Azure-SSIS IR secondaire après sa création. Quand le basculement de SSISDB se produit, vos runtimes d’exécution Azure-SSIS principal et secondaire échangent leurs rôles. Si votre Azure-SSIS IR principal est arrêté, vous devez le redémarrer. Selon qu’il est injecté dans un réseau virtuel et la méthode d’injection utilisée, son exécution prend entre 5 minutes ou de 20 à 30 minutes.

1. Si vous [utilisez un agent Azure SQL Managed Instance pour l’orchestration et la planification des exécutions de package](./how-to-invoke-ssis-package-managed-instance-agent.md), assurez-vous que tous les travaux SSIS pertinents, avec leurs étapes et les planifications associées, sont copiés dans votre Azure SQL Managed Instance secondaire avec les planifications initialement désactivées. Dans SSMS, procédez comme suit.

   1. Pour chaque travail SSIS, cliquez avec le bouton droit, puis sélectionnez dans le menu déroulant les éléments **Générer un script du travail en tant que**, **CREATE To** et **Nouvelle fenêtre de l’’éditeur de requête** pour générer son script.

      :::image type="content" source="media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png" alt-text="Générer un script de travail SSIS":::

   1. Pour chaque script de travail SSIS généré, recherchez la commande pour exécuter la procédure stockée `sp_add_job` et modifier/supprimer l’attribution de valeur à l’argument `@owner_login_name` si nécessaire.

   1. Pour chaque script de travail SSIS mis à jour, exécutez-le sur votre Azure SQL Managed Instance secondaire pour copier le travail avec ses étapes et les planifications associées.

   1. À l’aide du script suivant, créez un travail T-SQL pour activer ou désactiver les planifications de travaux SSIS en fonction du rôle de SSISDB principale/secondaire, respectivement dans vos instances managées Azure SQL primaire et secondaire, et exécutez-le régulièrement. Lors du basculement de SSISDB, les planifications de travaux SSIS désactivées sont activées, et vice versa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Si vous [utilisez ADF pour l’orchestration et la planification d’exécutions de package](./how-to-invoke-ssis-package-ssis-activity.md), assurez-vous que tous les pipelines ADF appropriés, avec les activités Exécuter le Package SSIS et les déclencheurs associés, sont copiés vers votre ADF secondaire avec les déclencheurs initialement désactivés. Lorsque le basculement de SSISDB se produit, vous devez les activer.

1. Vous pouvez [tester votre groupe de basculement Azure SQL Managed Instance](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover) et vérifier sur la [page de surveillance de l’Azure-SSIS IR dans le portail ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) si vos runtimes d’intégration Azure-SSIS principal et secondaire ont échangé leurs rôles. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Attacher un nouvel Azure-SSIS IR à une SSISDB existante hébergée par Azure SQL Database/Managed Instance

Si un incident se produit, qui a un impact sur votre Azure-SSIS IR mais pas sur Azure SQL Database/Managed Instance dans la même région, vous pouvez remplacer l’Azure-SSIS IR par un nouveau dans une autre région. Pour attacher votre SSISDB hébergée par Azure SQL Database/Managed Instance à un nouvel Azure-SSIS IR, procédez comme suit.

1. Si votre Azure-SSIS IR est toujours en cours d’exécution, vous devez commencer par l’arrêter à l’aide de l’interface utilisateur du portail Azure ou d’ADF, ou d’Azure PowerShell. Si l’incident affecte également ADF dans la même région, vous pouvez ignorer cette étape.

1. À l’aide de SSMS, exécutez la commande suivante pour la SSISDB dans votre Azure SQL Database/Managed Instance pour mettre à jour les métadonnées qui autorisent les connexions à partir de votre nouvel ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. À l’aide de l’[interface utilisateur du portail Azure ou d’ADF](./create-azure-ssis-integration-runtime-portal.md), ou d’[Azure PowerShell](./create-azure-ssis-integration-runtime-powershell.md), créez votre nouvel ADF/Azure-SSIS IR nommé *YourNewADF*/ *YourNewAzureSSISIR* dans une autre région. Si vous utilisez l’interface utilisateur du portail Azure ou d’ADF, vous pouvez ignorer l’erreur de test de connexion dans la page **Paramètres de déploiement** du volet **Configuration du runtime d’intégration**.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez envisager ces autres options de configuration pour votre Azure-SSIS IR :

- [Configurer des magasins de packages pour votre Azure-SSIS IR](./create-azure-ssis-integration-runtime-portal.md#creating-azure-ssis-ir-package-stores)

- [Configurer des configurations personnalisées pour votre Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Configurer l’injection de réseau virtuel pour votre Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Configurer un IR auto-hébergé en tant que proxy pour votre Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
