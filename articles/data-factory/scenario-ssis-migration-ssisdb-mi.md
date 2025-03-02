---
title: Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données
description: Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 4696a5405be18ca5ef0f95df9eea20756c02cacf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852196"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migration SSIS avec Azure SQL Managed Instance en tant que destination des charges de travail de base de données

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Lorsque vous procédez à la migration de charges de travail de base de données d’une instance SQL Server vers Azure SQL Managed Instance, vous devez être familiarisé avec [Azure Database Migration Service](../dms/dms-overview.md) (DMS) et les [topologies de réseau pour les migrations SQL Managed Instance avec DMS](../dms/resource-network-topologies.md).

Cet article traite de la migration de packages SSIS (SQL Server Integration Service) stockés dans le catalogue SSIS (SSISDB) et des travaux de SQL Server Agent qui planifient les exécutions des packages SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrer le catalogue SSIS (SSISDB)

La migration SSISDB peut être effectuée à l’aide de DMS comme décrit dans l’article : [Migrer des packages SSIS vers SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Travaux SSIS vers l'agent SQL Managed Instance

SQL Managed Instance présente un planificateur natif de première classe tout comme SQL Server Agent en local.  Vous pouvez [Exécuter des packages SSIS en utilisant l’agent Azure SQL Managed Instance](how-to-invoke-ssis-package-managed-instance-agent.md).

Aucun outil de migration n’est encore disponible pour les travaux SSIS. Ils doivent donc être migrés de SQL Server Agent en local vers l’agent SQL Managed Instance par le biais de scripts ou d’une copie manuelle.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Azure Data Factory](./introduction.md).
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Topologies réseau pour les migrations SQL Managed Instance avec DMS](../dms/resource-network-topologies.md)
- [Migrer des packages SSIS vers une instance SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Étapes suivantes

- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Exécuter des packages SSIS déployés sur Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)