---
title: Gérer des machines virtuelles SQL Server dans Azure à partir du portail Azure | Microsoft Docs
description: Apprenez à accéder à la ressource Machine virtuelle SQL sur le portail Azure pour une machine virtuelle SQL Server hébergée sur Azure afin de modifier les paramètres SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: pamela
ms.reviewer: mathoma
ms.custom: ignite-fall-2021
ms.openlocfilehash: 16b2a041dc384e8c04df077638a51db1a7823da2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072107"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>Gérer des machines virtuelles SQL Server à partir du portail Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Sur le [portail Azure](https://portal.azure.com), la ressource [**Machines virtuelles SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) est un service de gestion indépendant permettant de gérer SQL Server sur des machines virtuelles Azure qui ont été inscrites avec l'extension SQL Server IaaS Agent. Vous pouvez utiliser cette ressource pour consulter simultanément l'ensemble de vos machines virtuelles SQL Server et modifier les paramètres dédiés à SQL Server : 

![Ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)

Le point de gestion de la ressource **Machines virtuelles SQL** est différent de la ressource **Machine virtuelle** utilisée pour gérer la machine virtuelle, par exemple pour la démarrer, l'arrêter ou la redémarrer. 


## <a name="prerequisite"></a>Prérequis 

La ressource **Machines virtuelles SQL** est uniquement disponible pour les machines virtuelles SQL Server qui ont été [inscrites avec l'extension SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-resource"></a>Accéder à la ressource

Pour accéder à la ressource **Machines virtuelles SQL**, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Tous les services**. 
1. Entrez **machines virtuelles SQL** dans la zone de recherche.
1. (Facultatif) : Sélectionnez l’étoile en regard de **Machines virtuelles SQL** pour ajouter cette option à votre menu **Favoris**. 
1. Sélectionnez **Machines virtuelles SQL**. 

   ![Rechercher les machines virtuelles SQL Server dans tous les services](./media/manage-sql-vm-portal/sql-vm-search.png)

1. Le portail liste toutes les machines virtuelles SQL Server disponibles dans l’abonnement. Sélectionnez celle que vous voulez gérer pour ouvrir la ressource **Machines virtuelles SQL**. Utilisez la zone de recherche si votre machine virtuelle SQL Server n’apparaît pas. 

   ![Toutes les machines virtuelles SQL Server disponibles](./media/manage-sql-vm-portal/all-sql-vms.png)

   La sélection de votre machine virtuelle SQL Server ouvre la ressource **Machines virtuelles SQL** : 


   ![Afficher la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> La ressource **Machines virtuelles SQL** est destinée aux paramètres SQL Server dédiés. Sélectionnez le nom de la machine virtuelle dans la zone **Machine virtuelle** pour ouvrir les paramètres qui sont propres à la machine virtuelle, mais pas exclusifs à SQL Server. 


## <a name="license-and-edition"></a>Licence et édition 

Utilisez la page **Configurer** de la ressource Machine virtuelle SQL afin de remplacer les métadonnées de votre licence SQL Server par **Paiement à l'utilisation**, **Azure Hybrid Benefit** ou **HA/DR** pour votre [réplica Azure gratuit dédié à la récupération d'urgence](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).



![Modifier la version et l'édition des métadonnées des machines virtuelles SQL Server sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

Vous pouvez également modifier l'édition de SQL Server à partir de la page **Configurer**, en choisissant par exemple **Entreprise**, **Standard** ou **Développeur**. 

La modification des métadonnées de licence et d'édition sur le portail Azure n'est prise en charge qu'une fois que la version et l'édition de SQL Server ont été modifiées en interne sur la machine virtuelle. Pour plus d'informations, consultez Modifier la [version](change-sql-server-version.md) et l'[édition](change-sql-server-edition.md) de SQL Server sur des machines virtuelles Azure. 

## <a name="storage"></a>Stockage 

Utilisez la page **Configurer** de la ressource Machines virtuelles SQL pour étendre vos lecteurs de données, de journaux et de tempdb. 

![Étendre le stockage sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>Application de correctifs

Utilisez la page **Mise à jour corrective** de la ressource Machines virtuelles SQL pour activer la mise à jour corrective automatique de votre machine virtuelle et installer automatiquement les mises à jour de Windows et SQL Server marquées comme importantes. Vous pouvez également y configurer un calendrier de maintenance, comme l'exécution quotidienne des mises à jour correctives, ainsi qu'une heure de début locale pour la maintenance, et une fenêtre de maintenance. 


![Configurer la mise à jour corrective automatisée et un calendrier sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


Pour plus d'informations, consultez [Mise à jour corrective automatisée](automated-patching.md). 



## <a name="backups"></a>Sauvegardes

Utilisez la page **Sauvegardes** de la ressource Machines virtuelles SQL pour configurer vos paramètres de sauvegarde automatisée, comme la durée de rétention, le compte de stockage à utiliser, le chiffrement, la sauvegarde des bases de données système et la planification des sauvegardes. 

![Configurer la sauvegarde automatisée et la planification sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

Pour plus d'informations, consultez [Mise à jour corrective automatisée](automated-backup.md). 


## <a name="high-availability-preview"></a>Haute disponibilité (préversion)

Utilisez la page **Haute disponibilité** de la ressource Machines virtuelles SQL pour créer un cluster de basculement Windows Server et configurer un groupe de disponibilité AlwaysOn, un écouteur de groupe de disponibilité et une instance d'Azure Load Balancer. La configuration de la haute disponibilité à l'aide du portail Azure est actuellement disponible en préversion. 


![Configurer un cluster de basculement Windows Server et un groupe de disponibilité AlwaysOn sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


Pour plus d'informations, consultez [Configurer un groupe de disponibilité à partir du portail Azure](availability-group-azure-portal-configure.md).

## <a name="security-configuration"></a>Configuration de la sécurité 

Utilisez la page **Configuration de la sécurité** de la ressource Machines virtuelles SQL pour configurer les paramètres de sécurité SQL Server tels que le port à utiliser, l'activation de l'authentification SQL et l'activation de l'intégration d'Azure Key Vault. 

![Configurer la sécurité SQL Server sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

Pour plus d'informations, consultez [Meilleures pratiques en matière de sécurité](security-considerations-best-practices.md).


## <a name="security-center"></a>Security Center 

Utilisez la page **Security Center** de la ressource Machines virtuelles SQL pour afficher les recommandations de Security Center directement sur le panneau Machine virtuelle SQL. Activez [Azure Defender pour SQL](../../../security-center/defender-for-sql-usage.md) pour tirer parti de cette fonctionnalité. 

![Configurer les paramètres de SQL Server Security Center sur le portail Azure à l'aide de la ressource Machines virtuelles SQL](./media/manage-sql-vm-portal/sql-vm-security-center.png)

## <a name="sql-assessment-preview"></a>SQL Assessment (préversion)

Utilisez la page **SQL Assessment** de la ressource des machines virtuelles SQL pour évaluer l’intégrité de votre machine virtuelle SQL Server. Une fois la fonctionnalité activée, vos instances et bases de données SQL Server sont analysées, et des recommandations sont exposées pour améliorer les performances (index, statistiques, indicateurs de trace, etc.) et identifier des configurations manquantes correspondant aux meilleures pratiques. SQL Assessment est actuellement en préversion. 

:::image type="content" source="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png" alt-text="Regardez SQL Assessment dans le portail Azure sur la page des ressources de machines virtuelles SQL." lightbox="./media/manage-sql-vm-portal/sql-vm-assessment-workbook.png":::

Pour plus d’informations, consultez [SQL Assessment pour SQL Server sur machines virtuelles Azure](sql-assessment-for-sql-vm.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Questions fréquentes (FAQ) pour SQL Server sur une machine virtuelle Windows](frequently-asked-questions-faq.yml)
* [Guide des tarifs pour SQL Server sur une machine virtuelle Windows](pricing-guidance.md)
* [Nouveautés de SQL Server sur des machines virtuelles Azure](doc-changes-updates-release-notes-whats-new.md)
