---
title: Intégrer avec les produits de sécurité Azure
description: Cet article explique comment connecter les services de sécurité Azure et Azure Purview pour bénéficier d’expériences de sécurité enrichies.
author: aashishr
ms.author: aashishr
ms.service: purview
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 7369f64f16724eb5c660b54a3d14a30046a41685
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894937"
---
# <a name="integrate-azure-purview-with-azure-security-products"></a>Intégrer Azure Purview avec les produits de sécurité Azure

Ce document explique comment connecter un compte Azure Purview avec divers produits de sécurité Azure afin d’enrichir les expériences de sécurité avec des classifications de données et des étiquettes de sensibilité.

## <a name="microsoft-defender-for-cloud"></a>Microsoft Defender pour le cloud
Azure Purview fournit des insights détaillées sur la sensibilité de vos données. Cela le rend précieux pour les équipes de sécurité utilisant Microsoft Defender pour le cloud afin de gérer la sécurité de l’organisation et d’assurer la protection contre les menaces pesant sur leurs charges de travail. Les ressources de données restant une cible de choix pour les acteurs malveillants, cela permet aux équipes de sécurité d’identifier, de hiérarchiser et de sécuriser les ressources de données sensibles dans leurs environnements cloud. L’intégration avec Azure Purview augmente la visibilité de la couche de données, ce qui permet aux équipes de sécurité de hiérarchiser les ressources contenant des données sensibles.

Pour tirer parti de cet [enrichissement dans Microsoft Defender pour le cloud](../security-center/information-protection.md), aucune étape supplémentaire n’est nécessaire dans Azure Purview. Commencez à explorer la [page d’inventaire](https://portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/25) des enrichissements de sécurité avec Microsoft Defender pour le cloud, qui présente la liste des sources de données avec des classifications et des étiquettes de sensibilité.

### <a name="supported-data-sources"></a>Sources de données prises en charge
L’intégration prend en charge les sources de données dans Azure et AWS. Les données sensibles découvertes dans ces ressources sont partagées avec Microsoft Defender pour le cloud :
- [Stockage Blob Azure](./register-scan-azure-blob-storage-source.md)
- [Azure Cosmos DB](./register-scan-azure-cosmos-database.md)
- [Explorateur de données Azure](./register-scan-azure-data-explorer.md)
- [Azure Data Lake Storage Gen1](./register-scan-adls-gen1.md)
- [Azure Data Lake Storage Gen2](./register-scan-adls-gen2.md)
- [Azure Files](./register-scan-azure-files-storage-source.md)
- [Azure Database pour MySQL](./register-scan-azure-mysql-database.md)
- [Base de données Azure pour PostgreSQL](./register-scan-azure-postgresql.md)
- [Azure SQL Database Managed Instance](./register-scan-azure-sql-database-managed-instance.md)
- [Pool SQL dédié Azure (anciennement SQL DW)](./register-scan-azure-synapse-analytics.md)
- [Azure SQL Database](./register-scan-azure-sql-database.md)
- [Azure Synapse Analytics (Espace de travail)](./register-scan-synapse-workspace.md)
- [Amazon S3](./register-scan-amazon-s3.md)

### <a name="known-issues"></a>Problèmes connus
1. Les informations de sensibilité des données ne sont actuellement pas partagées pour les sources hébergées dans des machines virtuelles, telles que SAP, Erwin et Teradata.
2. Les informations de sensibilité des données ne sont actuellement pas partagées pour Amazon RDS.
3. Les informations de sensibilité des données ne sont actuellement pas partagées pour les sources de données PaaS Azure inscrites à l’aide d’une chaîne de connexion. 
5. L’annulation de l’inscription de la source de données dans Azure Purview n’a pas pour effet de supprime l’enrichissement de la sensibilité des données dans Microsoft Defender pour le cloud.
6. La suppression du compte Azure Purview conservera l’enrichissement de la sensibilité des données pendant 30 jours dans Microsoft Defender pour le cloud.
7. Les classifications personnalisées définies dans le Centre de conformité Microsoft 365 ou dans Azure Purview ne sont pas partagées avec Microsoft Defender pour le cloud.

## <a name="faq"></a>Forum aux questions
### <a name="why-dont-i-see-the-aws-data-source-i-have-scanned-with-azure-purview-in-microsoft-defender-for-cloud"></a>**Pourquoi ne puis-je pas voir la source de données AWS que j’ai analysée avec Azure Purview dans Microsoft Defender pour le cloud ?**

Les sources de données doivent également être intégrées à Microsoft Defender pour le cloud. Apprenez-en davantage sur la [connexion de vos comptes AWS](../security-center/quickstart-onboard-aws.md) et l’affichage de vos sources de données AWS dans Microsoft Defender pour le cloud.

### <a name="why-dont-i-see-sensitivity-labels-in-microsoft-defender-for-cloud"></a>**Pourquoi ne puis-je pas voir les étiquettes de sensibilité dans Microsoft Defender pour le cloud ?**

Les ressources doivent être étiquetées dans Azure Purview avant que les étiquettes apparaissent dans Microsoft Defender pour le cloud. Vérifiez si les [prérequis des étiquettes de sensibilité](./how-to-automatically-label-your-content.md) sont en place. Une fois que vous avez analysé les données, les étiquettes s’affichent dans Azure Purview, puis automatiquement dans Microsoft Defender pour le cloud.

## <a name="next-steps"></a>Étapes suivantes
- [Expériences dans Microsoft Defender pour le cloud enrichies à l’aide de la sensibilité d’Azure Purview](../security-center/information-protection.md)
