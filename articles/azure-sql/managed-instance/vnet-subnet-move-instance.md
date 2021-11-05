---
title: Déplacer une instance managée vers un autre sous-réseau
titleSuffix: Azure SQL Managed Instance
description: Découvrez comment déplacer une instance Azure SQL Managed Instance vers un autre sous-réseau avec un temps d’arrêt limité pendant le basculement, généralement jusqu’à 10 secondes.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7adb9886bdeebc8aad7f8c200b21523e53651a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096770"
---
# <a name="move-azure-sql-managed-instance-across-subnets"></a>Déplacer une instance Azure SQL Managed Instance entre des sous-réseaux
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance doit être déployé dans un sous-réseau dédié au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) Azure. Le nombre d’instances managées pouvant être déployées dans le sous-réseau dépend de la taille du sous-réseau (plage de sous-réseau).

Cet article vous apprend à déplacer votre instance managée d’un sous-réseau à un autre, de la même façon que la mise à l’échelle de vCores ou la modification du niveau de service de l’instance. SQL Managed Instance est disponible pendant le déplacement, sauf pendant un temps d’arrêt réduit dû à un basculement à la fin de la mise à jour, généralement d’une durée maximale de 10 secondes, même si les transactions longues sont interrompues. 

Le déplacement de l’instance vers un autre sous-réseau déclenche les opérations de cluster virtuel suivantes :
- Le sous-réseau de destination génère ou redimensionne le cluster virtuel.
- Le cluster virtuel est supprimé ou défragmenté dans le sous-réseau source. 

Avant de migrer votre instance vers un autre sous-réseau, vous devez vous familiariser avec les concepts suivants : 
- [Déterminez la taille et la plage nécessaires du sous-réseau pour Azure SQL Managed Instance](vnet-subnet-determine-size.md).
- Choisissez entre déplacer l’instance vers un [nouveau sous-réseau](virtual-network-subnet-create-arm-template.md) ou [utiliser un sous-réseau existant](vnet-existing-add-subnet.md).
- Utilisez les [opérations de gestion](management-operations-overview.md) pour déployer automatiquement les nouvelles instances managées, mettre à jour les propriétés d’instance ou supprimer des instances. Il est possible de [superviser](management-operations-monitor.md) ces opérations de gestion. 



## <a name="requirements-and-limitations"></a>Conditions requises et limitations : 

Pour déployer une instance managée ou la déplacer vers un autre sous-réseau, le sous-réseau de destination doit présenter une certaine [configuration requise pour le réseau](connectivity-architecture-overview.md#service-aided-subnet-configuration).

### <a name="subnet-readiness"></a>Préparation du sous-réseau 

Avant de déplacer votre instance managée, vérifiez que le sous-réseau est marqué comme **prêt pour Managed Instance**. 

Dans l’interface utilisateur du **réseau virtuel** du portail Azure, les réseaux virtuels qui remplissent les prérequis pour une instance managée sont classés comme étant **prêts pour Managed Instance**. Les réseaux virtuels qui ont des sous-réseaux sur lesquels des instances managées sont déjà déployées affichent une icône avant le nom du réseau virtuel. Les sous-réseaux vides prêts pour une instance managée n’ont pas d’icône. 

Les sous-réseaux marqués comme **autres** sont vides et peuvent être utilisés pour une instance managée, mais vous devez d’abord répondre à la [configuration requise pour le réseau](connectivity-architecture-overview.md#service-aided-subnet-configuration). notamment :

- délégation au fournisseur de ressources Microsoft.Sql/managedInstances
- attachement d’une table de routage
- attachement d’un groupe de sécurité réseau

Une fois que toutes les conditions requises sont satisfaites, le sous-réseau passe de la catégorie **autre** à la catégorie **prêt pour Managed Instance** et peut être utilisé pour une instance managée. 

Les sous-réseaux marqués comme **non valides** ne peuvent pas être utilisés pour les instances managées nouvelles ou existantes, soit parce qu’ils sont déjà en cours d’utilisation (les instances utilisées pour les déploiements d’instances ne peuvent pas contenir d’autres ressources), soit le sous-réseau a une zone DNS différente (limitation du déplacement d’instance entre sous-réseaux). 

> [!div class="mx-imgBorder"]
> ![Capture d’écran de la liste déroulante du sous-réseau Azure SQL Managed Instance](./media/vnet-subnet-move-instance/subnet-grouping-per-state.png)


En fonction de la désignation et de l’état du sous-réseau, les ajustements suivants peuvent être apportés au sous-réseau de destination : 

- **Prêt pour Managed Instance (contient une instance SQL Managed Instance existante)**  : aucun ajustement n’est effectué. Ces sous-réseaux contiennent déjà des instances managées et toute modification apportée au sous-réseau peut avoir un impact sur les instances existantes. 
- **Prêt pour Managed Instance (vide)**  : le workflow valide toutes les règles requises dans le groupe de sécurité réseau et la table de routage, et ajoute toutes les règles nécessaires mais manquantes. <sup>1</sup>

> [!Note]
> <sup>1</sup> Les règles personnalisées ajoutées à la configuration du sous-réseau source ne sont pas copiées sur le sous-réseau de destination. Toute personnalisation de la configuration du sous-réseau source doit être répliquée manuellement sur le sous-réseau de destination. Une façon d’y parvenir consiste à utiliser la même table de routage et le même groupe de sécurité réseau pour les sous-réseaux source et de destination.


### <a name="destination-subnet-limitations"></a>Limitations concernant le sous-réseau de destination 

Tenez compte des limitations suivantes lorsque vous choisissez un sous-réseau de destination pour une instance existante : 

- Le sous-réseau de destination doit se trouver dans le même réseau virtuel que le sous-réseau source. 
- La zone DNS du sous-réseau de destination doit correspondre à la zone DNS du sous-réseau source, car la modification de la zone DNS d’une instance managée n’est pas prise en charge actuellement. 
- Les instances qui s’exécutent sur le matériel Gen4 doivent être mises à niveau vers une génération de matériel plus récente, car Gen4 est déprécié. La mise à niveau de la génération de matériel et le déplacement vers un autre sous-réseau peuvent être effectués en une seule opération. 


## <a name="operation-steps"></a>Étapes de l’opération

Le tableau suivant détaille les étapes de l’opération qui se produisent pendant l’opération de déplacement d’instance : 

|Nom de l’étape  |Description de l’étape  |
|----|---------|
|Validation des demandes |Valide les paramètres envoyés. En cas de détection d’erreurs de configuration, l’opération échoue avec une erreur. |
|Redimensionnement / création de cluster virtuel |Selon l’état du sous-réseau de destination, le cluster virtuel est créé ou redimensionné.  |
|Démarrage d’une nouvelle instance |Le processus SQL démarre sur le cluster virtuel déployé dans le sous-réseau de destination. |
|Amorçage des fichiers de base de données / attachement de fichiers de base de données |Selon le niveau de service, la base de données est amorcée ou les fichiers de base de données sont attachés. |
|Préparation du basculement et basculement |Une fois les données amorcées ou les fichiers de base de données réattachés, le système se prépare au basculement. Lorsque tout est prêt, le système effectue un basculement **avec un temps d’arrêt réduit**, généralement inférieur à 10 secondes.  |
|Nettoyage de l’ancienne instance SQL |Supprime l’ancien processus SQL du cluster virtuel source.  |
|Suppression de cluster virtuel |S’il s’agit de la dernière instance au sein du sous-réseau source, l’étape finale supprime le cluster virtuel de façon synchrone. Dans le cas contraire, le cluster virtuel est défragmenté de façon asynchrone.  |

Une explication détaillée des étapes de l’opération est disponible dans la [vue d’ensemble des opérations de gestion Azure SQL Managed Instance](management-operations-overview.md#management-operations-steps)

## <a name="move-the-instance"></a>Déplacer l’instance

Un déplacement d’instance entre sous-réseaux fait partie de l’opération de mise à jour d’instance. Les commandes d’API de mise à jour d’instance, Azure PowerShell et Azure CLI existantes ont été améliorées avec une propriété d’ID de sous-réseau. 

Dans le portail Azure, utilisez le champ de sous-réseau du panneau **Réseau** pour déplacer l’instance vers le sous-réseau de destination. Lorsque vous utilisez Azure PowerShell ou Azure CLI, fournissez un ID de sous-réseau différent dans la commande de mise à jour pour déplacer l’instance d’un sous-réseau existant vers le sous-réseau de destination. 

Pour obtenir une référence complète des commandes de gestion d’instance, consultez [Informations de référence sur l’API de gestion pour Azure SQL Managed Instance](api-references-create-manage-instance.md). 

# <a name="portal"></a>[Portail](#tab/azure-portal)

L’option permettant de choisir le sous-réseau d’instance se trouve dans le panneau **Réseau** du portail Azure. L’opération de déplacement d’instance démarre lorsque vous sélectionnez un sous-réseau et enregistrez vos modifications. 

La première étape de l’opération de déplacement consiste à préparer le sous-réseau de destination pour le déploiement, ce qui peut prendre plusieurs minutes. Une fois que le sous-réseau est prêt, l’opération de gestion du déplacement d’instance démarre et devient visible dans le portail Azure. 


> [!div class="mx-imgBorder"]
> ![Comment sélectionner un sous-réseau dans le panneau Réseau SQL Managed Instance](./media/vnet-subnet-move-instance/how-to-select-subnet.png)


Supervisez les opérations de déplacement d’instance à partir du panneau **Vue d’ensemble** du portail Azure. Sélectionnez la notification pour ouvrir un panneau supplémentaire contenant des informations sur l’étape actuelle, le nombre total d’étapes et un bouton pour annuler l’opération. 

> [!div class="mx-imgBorder"]
> ![Comment superviser l’opération de déplacement d’instance](./media/vnet-subnet-move-instance/monitor-subnet-move-operation.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande Azure PowerShell [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) pour déplacer une instance après avoir créé votre sous-réseau dans le même réseau virtuel que votre sous-réseau de destination. Si vous souhaitez utiliser un sous-réseau existant, indiquez le nom de ce sous-réseau dans la commande PowerShell.

Les exemples de commandes PowerShell de cette section préparent le sous-réseau de destination pour le déploiement de l’instance et déplacent l’instance managée. 


Utilisez la commande PowerShell suivante pour spécifier vos paramètres : 

```powershell-interactive
### PART 1 - DEFINE PARAMETERS

#Generating basic parameters
$currentSubscriptionID = 'subscription-id'
$sqlMIResourceGroupName = 'resource-group-name-of-sql-mi'
$sqlMIName = 'sql-mi-name'
$sqlMIResourceVnetName = 'vnet-name-of-sql-mi'
$destinationSubnetName = 'name-of-the-destination-subnet-for-sql-mi'
```

Ignorez cette commande si des instances sont déjà déployées sur votre sous-réseau. Si vous utilisez un nouveau sous-réseau, utilisez la commande Azure PowerShell suivante pour le préparer : 

```powershell-interactive
### PART 2 - PREPARE DESTINATION SUBNET

#Loading the url of script used for preparing the subnet for SQL MI deployment
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

#Generating destination subnet parameters
$parameters = @{
    subscriptionId = $currentSubscriptionID
    resourceGroupName = $sqlMIResourceGroupName
    virtualNetworkName = $sqlMIResourceVnetName
    subnetName = $destinationSubnetName
}

#Initiating subnet prepartion script
Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

> [!Note]
> Pour en savoir plus sur le script qui prépare le sous-réseau, consultez [Configurer un réseau virtuel existant pour Azure SQL Managed Instance](vnet-existing-add-subnet.md). 

La commande Azure PowerShell suivante déplace l’instance vers le sous-réseau source : 

```powershell-interactive
### PART 3 - MOVE INSTANCE TO THE NEW SUBNET

Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName"
```

La commande Azure PowerShell suivante déplace l’instance et fournit également un moyen de superviser la progression : 

```powershell-interactive
###PART 3 EXTENDED - MOVE INSTANCE AND MONITOR PROGRESS

# Extend the Set-AzSqlInstance command with -AsJob -Force parameters to be able to monitor the progress or proceed with script execution as moving the instance to another subnet is long running operation 
Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName" -AsJob -Force

$operationProgress = Get-AzSqlInstanceOperation -ManagedInstanceName $sqlMIName -ResourceGroupName $sqlMIResourceGroupName
#checking the operation step status
Write-Host "Checking the ongoing step" -ForegroundColor Yellow
$operationProgress.OperationSteps.StepsList
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande Azure CLI [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) pour déplacer votre instance vers un autre sous-réseau. 

Indiquez la destination en spécifiant l’ID de sous-réseau en tant que propriété `--subnet` ou le nom du réseau virtuel en tant que propriété `--vnet-name` et le nom du sous-réseau en tant que propriété `--subnet`. 

L’exemple suivant déplace l’instance managée vers un autre sous-réseau en spécifiant l’ID de sous-réseau : 


```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --subnet /subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVirtualNetworkName/subnets/destinationSubnetName
```

L’exemple suivant déplace l’instance managée vers un autre sous-réseau en spécifiant le nom du réseau virtuel et le nom du sous-réseau :

```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --vnet-name myVirtualNetworkName --subnet destinationSubnetName
```

Utilisez la commande suivante pour superviser la progression de l’opération de gestion : 

```azurecli-interactive
az sql mi op list -g myResourceGroup --mi mySqlManagedInstance
```
---

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](instance-create-quickstart.md).
- Pour accéder à la liste des fonctionnalités et à leur comparaison, consultez [Fonctionnalités SQL courantes](../database/features-comparison.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configuration de réseau virtuel SQL Managed Instance](connectivity-architecture-overview.md).
- Pour obtenir un guide de démarrage rapide qui crée une instance managée et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une instance managée](instance-create-quickstart.md).
- Pour accéder à un tutoriel expliquant comment utiliser Azure Database Migration Service pour la migration, consultez [Migration SQL Managed Instance à l’aide d’Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
