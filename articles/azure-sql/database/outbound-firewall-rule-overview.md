---
title: Règles de pare-feu pour le trafic sortant (préversion)
description: Présentation de la fonctionnalité de règles de pare-feu pour le trafic sortant.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
ms.reviewer: vanto
ms.date: 11/10/2021
ms.openlocfilehash: d69536d8ed72dbd3793dd2481897d4534dbc2fdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350493"
---
# <a name="outbound-firewall-rules-for-azure-sql-database-and-azure-synapse-analytics-preview"></a>Règles de pare-feu relatives au trafic sortant pour Azure SQL Database et Azure Synapse Analytics (préversion)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)] 

Les règles de pare-feu relatives au trafic sortant limitent le trafic réseau du serveur logique Azure SQL à une liste définie par le client de comptes de stockage Azure et de serveurs logiques Azure SQL. Toute tentative d'accès à des comptes de stockage ou à des instances de SQL Database qui ne figurent pas dans cette liste est refusée. Les fonctionnalités Azure SQL Database suivantes prennent en charge cette fonctionnalité :

- [Audit](auditing-overview.md)
- [Évaluation des vulnérabilités](sql-vulnerability-assessment.md)
- [I/E service](database-import-export-azure-services-off.md)
- OPENROWSET
- Insertion en bloc
- [Requête élastique](elastic-query-overview.md)

> [!IMPORTANT]
> Cet article s’applique à la fois à Azure SQL Database et au [pool SQL dédié (anciennement SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) dans Azure Synapse Analytics. Ces paramètres s’appliquent à toutes les bases de données SQL Database et bases de données de pool SQL dédié (anciennement SQL DW) associées au serveur. Pour simplifier, le terme « base de données » fait référence aux bases de données dans Azure SQL Database et Azure Synapse Analytics. De même, toute référence à un « serveur » fait référence au [serveur SQL Server logique](logical-servers.md) qui héberge Azure SQL Database et le pool SQL dédié (anciennement SQL DW) dans Azure Synapse Analytics. Cet article ne s’applique *pas* à Azure SQL Managed Instance ni aux pools SQL dédiés dans les espaces de travail Azure Synapse Analytics.

## <a name="set-outbound-firewall-rules-in-the-azure-portal"></a>Définir des règles de pare-feu pour le trafic sortant sur le portail Azure

1. Accédez à la section **Mise en réseau du trafic sortant** dans le panneau **Pare-feux et réseaux virtuels** de votre instance d'Azure SQL Database, puis sélectionnez **Configurer les restrictions liées à la mise en réseau du trafic sortant**.

   ![Capture d’écran de la section Mise en réseau du trafic sortant][1]  

   Le panneau suivant s’ouvre sur le côté droit :

   ![Capture d'écran du panneau Mise en réseau du trafic sortant dans lequel rien n'est sélectionné][2]  

1. Cochez la case **Restreindre la mise en réseau du trafic sortant**, puis ajoutez le nom de domaine complet des comptes Stockage (ou des instances de SQL Database) à l’aide du bouton **Ajouter un domaine**.

   ![Capture d'écran du panneau Mise en réseau du trafic sortant montrant comment ajouter un nom de domaine complet][3]  

1. Une fois que vous avez terminé, vous accédez à un écran semblable à celui présenté ci-dessous. Sélectionnez **OK** pour appliquer ces paramètres.

   ![Capture d’écran du panneau Mise en réseau du trafic sortant après l’ajout des noms de domaine complets][4]  

## <a name="set-outbound-firewall-rules-using-powershell"></a>Créer des règles de pare-feu pour le trafic sortant à l’aide de PowerShell

> [!IMPORTANT]
> Azure SQL Database prend toujours en charge le module PowerShell Azure Resource Manager, mais tous les développements futurs seront destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Le script suivant nécessite le [module Azure PowerShell](/powershell/azure/install-az-ps).

Le script PowerShell suivant montre comment modifier le paramètre de mise en réseau du trafic sortant (à l’aide de la propriété **RestrictOutboundNetworkAccess**) :

```powershell
# Get current settings for Outbound Networking
(Get-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>).RestrictOutboundNetworkAccess

# Update setting for Outbound Networking
$SecureString = ConvertTo-SecureString "<ServerAdminPassword>" -AsPlainText -Force

Set-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -SqlAdministratorPassword $SecureString  -RestrictOutboundNetworkAccess "Enabled"
```

Utilisez ces cmdlets PowerShell pour configurer des règles de pare-feu pour le trafic sortant.

```powershell
# List all Outbound Firewall Rules
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>

# Add an Outbound Firewall Rule
New-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN testOBFR1

# List a specific Outbound Firewall Rule
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>

#Delete an Outbound Firewall Rule
Remove-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>
```

## <a name="set-outbound-firewall-rules-using-the-azure-cli"></a>Définir des règles de pare-feu pour le trafic sortant à l’aide d’Azure CLI

> [!IMPORTANT]
> Tous les scripts évoqués dans cette section nécessitent l’[interface de ligne de commande Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI dans un interpréteur de commandes Bash

Le script Azure CLI suivant montre comment modifier le paramètre de mise en réseau du trafic sortant (à l’aide de la propriété **RestrictOutboundNetworkAccess**) dans un interpréteur de commandes Bash :

```azurecli-interactive
# Get current setting for Outbound Networking 
az sql server show -n sql-server-name -g sql-server-group --query "RestrictOutboundNetworkAccess"

# Update setting for Outbound Networking
az sql server update -n sql-server-name -g sql-server-group --set RestrictOutboundNetworkAccess="Enabled"
```

Utilisez les commandes CLI suivantes afin de configurer des règles de pare-feu pour le trafic sortant.

```azurecli-interactive
# List a server's outbound firewall rules.
az sql server outbound-firewall-rule list -g sql-server-group -s sql-server-name

# Create a new outbound firewall rule
az sql server outbound-firewall-rule create -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Show the details for an outbound firewall rule.
az sql server outbound-firewall-rule show -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Delete the outbound firewall rule.
az sql server outbound-firewall-rule delete -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN
```

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la sécurité Azure SQL Database, consultez [Sécurisation de votre base de données](security-overview.md)
- Pour obtenir une vue d’ensemble de la connectivité Azure SQL Database, consultez [Architecture de connectivité Azure SQL](connectivity-architecture.md).

<!--Image references-->
[1]: media/outbound-firewall-rules/Step1.jpg
[2]: media/outbound-firewall-rules/Step2.jpg
[3]: media/outbound-firewall-rules/Step3.jpg
[4]: media/outbound-firewall-rules/Step4.jpg
