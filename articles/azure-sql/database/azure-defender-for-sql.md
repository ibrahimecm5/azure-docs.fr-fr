---
title: Microsoft Defender pour SQL
description: Apprenez-en plus sur les fonctionnalités permettant de gérer les vulnérabilités de votre base de données et de détecter les activités anormales pouvant indiquer une menace ciblant votre base de données dans Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 06/07/2021
ms.openlocfilehash: 77c9e9fc02ba5fdb5f67cf14495103b352196f0c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347901"
---
# <a name="microsoft-defender-for-sql"></a>Microsoft Defender pour SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Microsoft Defender pour SQL est un package unifié de fonctionnalités de sécurité SQL avancées. Microsoft Defender pour le cloud est disponible pour Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics. Il inclut des fonctionnalités permettant de découvrir et d’atténuer les vulnérabilités de votre base de données, et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>Quels sont les avantages de Microsoft Defender pour SQL ?

Microsoft Defender pour le cloud fournit un ensemble de capacités avancées de sécurité SQL, dont l’évaluation des vulnérabilités SQL et la protection avancée contre les menaces.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et corriger de potentielles vulnérabilités de base de données. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- [Advanced Threat Protection](threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL Azure et de modèles d’accès anormaux à la base de données. Les alertes Advanced Threat Protection fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Activez Microsoft Defender pour SQL une seule fois pour activer toutes ces fonctionnalités incluses. D’un seul clic, vous pouvez activer Microsoft Defender pour toutes les bases de données sur votre [serveur](logical-servers.md) dans Azure ou dans votre instance gérée SQL. L’activation ou la gestion des paramètres de Microsoft Defender pour le cloud nécessite d’appartenir au rôle [Gestionnaire de sécurité SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) ou à l’un des rôles administrateur de base de données ou de serveur.

Pour plus d’informations sur la tarification de Microsoft Defender pour SQL, consultez la [page de tarification de Microsoft Defender pour le cloud](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-microsoft-defender-for-cloud"></a>Activer Microsoft Defender pour le cloud

Il existe plusieurs façons d’activer des plans Microsoft Defender. Vous pouvez les activer au niveau de l’abonnement (**recommandé**) à partir de :

- [Microsoft Defender pour le cloud](#enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud)
- [Activer les plans Defender par programmation avec l’API REST, Azure CLI, PowerShell ou Azure Policy](#enable-microsoft-defender-plans-programatically)

Vous pouvez également les activer au niveau de la ressource, comme décrit dans [Activer Microsoft Defender pour Azure SQL Database au niveau de la ressource](#enable-microsoft-defender-for-azure-sql-database-at-the-resource-level).

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud"></a>Activer Microsoft Defender pour Azure SQL Database au niveau de l’abonnement à partir de Microsoft Defender pour le cloud
Pour activer Microsoft Defender pour Azure SQL Database au niveau de l’abonnement à partir de Microsoft Defender pour le cloud :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Defender pour le cloud**.
1. Dans le menu de Defender pour le cloud, sélectionnez **Tarification et paramètres**.
1. Sélectionnez l’abonnement approprié.
1. Changez le paramètre de plan sur **Activé**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Activation de Microsoft Defender pour Azure SQL Database au niveau de l’abonnement.":::

1. Sélectionnez **Enregistrer**.


### <a name="enable-microsoft-defender-plans-programatically"></a>Activer les plans Microsoft Defender par programmation 

La flexibilité d’Azure offre plusieurs méthodes programmatiques pour l’activation des plans Microsoft Defender. 

Utilisez l’un des outils suivants pour activer Microsoft Defender pour votre abonnement : 

| Méthode       | Instructions                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| API REST     | [API pricings](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-resource-level"></a>Activer Microsoft Defender pour Azure SQL Database au niveau de la ressource

Nous vous recommandons d’activer les plans Microsoft Defender au niveau de l’abonnement, ce qui peut aider à créer des ressources non protégées. Toutefois, si vous avez une raison organisationnelle d’activer Microsoft Defender pour le cloud au niveau du serveur, procédez comme suit :

1. À partir du [portail Azure](https://portal.azure.com), ouvrez votre serveur ou instance managée.
1. Sous l’en-tête **Sécurité**, sélectionnez **Defender pour le cloud**.
1. Sélectionnez **Activer Microsoft Defender pour SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Activer Microsoft Defender pour SQL à partir des bases de données Azure SQL.":::

> [!NOTE]
> Un compte de stockage est automatiquement créé et configuré pour stocker les résultats d’analyse de votre **évaluation des vulnérabilités**. Si vous avez déjà activé Microsoft Defender pour un autre serveur dans le même groupe de ressources et la même région, alors le compte de stockage existant est utilisé.
>
> Le coût de Microsoft Defender pour SQL est aligné sur la tarification du niveau Standard de Microsoft Defender pour le cloud par nœud, où un nœud désigne l’ensemble du serveur ou de l’instance gérée. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur ou l’instance gérée avec Microsoft Defender pour le cloud. Vous pouvez tester Microsoft Defender pour le cloud par le biais d’une évaluation gratuite.

## <a name="manage-microsoft-defender-for-cloud-settings"></a>Gérer les paramètres de Microsoft Defender pour le cloud

Pour afficher et gérer les paramètres de Microsoft Defender pour le cloud :

1. Dans la zone **Sécurité** de votre serveur ou de votre instance gérée, sélectionnez **Defender pour le cloud**.

    Sur cette page, vous verrez l’état de Microsoft Defender pour SQL :

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Vérification de l’état de Microsoft Defender pour SQL dans les bases de données Azure SQL.":::

1. Si Microsoft Defender pour SQL est activé, vous verrez un lien **Configurer** comme illustré dans le graphique précédent. Pour modifier les paramètres de Microsoft Defender pour SQL, sélectionnez **Configurer**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Paramètres pour Microsoft Defender pour SQL.":::

1. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md)
- En savoir plus sur [Advanced Threat Protection](threat-detection-configure.md)
- Découvrez-en plus sur [Microsoft Defender pour le cloud](../../security-center/security-center-introduction.md)
