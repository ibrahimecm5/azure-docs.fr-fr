---
title: Déployer des packages SSIS
description: Découvrez comment déployer et exécuter des packages SSIS dans Azure Data Factory avec le runtime intégré Azure-SSIS.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/22/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32025ab757698448f9d8a36467c09e9b501f206
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842945"
---
# <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Après la configuration de votre runtime d’intégration Azure-SSIS, vous pouvez déployer et exécuter des packages directement dans Azure.

## <a name="using-ssisdb"></a>Utilisation de SSISDB

Si vous utilisez le catalogue SSISDB, vous pouvez y déployer vos packages et les exécuter sur votre runtime d’intégration Azure-SSIS à l’aide des outils SSDT ou SSMS compatibles avec Azure. Ces outils se connectent à votre serveur de base de données via son point de terminaison : 

- Pour un serveur Azure SQL Database, le format du point de terminaison de serveur est `<server name>.database.windows.net`.
- Pour une instance managée avec un point de terminaison privé, le format du point de terminaison de serveur est `<server name>.<dns prefix>.database.windows.net`.
- Pour une instance managée avec un point de terminaison public, le format du point de terminaison de serveur est `<server name>.public.<dns prefix>.database.windows.net,3342`. 

## <a name="using-file-system-azure-files-or-msdb"></a>Utilisation du système de fichiers, de fichiers Azure ou de MSDB

Si vous n’utilisez pas SSISDB, vous pouvez déployer vos packages sur un système de fichiers, Azure Files ou MSDB, dont l’hébergement est assuré par Azure SQL Managed Instance, puis les exécuter sur Azure-SSIS IR à l’aide des utilitaires en ligne de commande [dtutil](/sql/integration-services/dtutil-utility) et [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Pour plus d’informations, consultez [Déployer des projets ou des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

Dans les deux cas, vous pouvez également exécuter vos packages déployés sur Azure-SSIS IR à l’aide de l’activité Exécuter le package SSIS dans des pipelines Data Factory. Pour plus d’informations, consultez [Appeler une exécution de package SSIS comme activité Data Factory de première classe](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez comment configurer un IR Azure-SSIS à l’aide du portail Azure](create-azure-ssis-integration-runtime-portal.md).
- [Découvrez comment approvisionner un IR Azure-SSIS à l'aide d'Azure PowerShell](create-azure-ssis-integration-runtime-powershell.md).
- [Découvrez comment approvisionner un Azure-SSIS IR à l’aide d’un modèle Azure Resource Manager](create-azure-ssis-integration-runtime-resource-manager-template.md).

Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation :

- [Runtime d’intégration Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Cet article fournit des informations sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS.
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer votre Azure-SSIS IR. Il vous montre également comment effectuer un scale-out de votre IR Azure-SSIS en ajoutant des nœuds.
- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
