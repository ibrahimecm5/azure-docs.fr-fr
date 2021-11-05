---
title: Configurer des stratégies de point de terminaison de service
description: Configurez des stratégies de point de terminaison de service de stockage Azure pour protéger Azure SQL Managed Instance contre l’exfiltration vers des comptes de stockage Azure non autorisés.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 9760285da827e884c5dfa49ca1da58b3e76292de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096662"
---
# <a name="configure-service-endpoint-policies-preview-for-azure-sql-managed-instance"></a>Configurer des stratégies de point de terminaison de service (préversion) pour Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Les [stratégies de point de terminaison de service](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) de stockage Azure de réseau virtuel vous permettent de filtrer le trafic de réseau virtuel de sortie vers le stockage Azure, ce qui limite les transferts de données vers des comptes de stockage spécifiques.

La possibilité de configurer vos stratégies de point de terminaison et de les associer à votre instance SQL Managed Instance est actuellement en préversion. 

## <a name="key-benefits"></a>Principaux avantages

La configuration des stratégies de point de terminaison de service de stockage Azure de réseau virtuel pour votre instance Azure SQL Managed Instance offre les avantages suivants :

- __Amélioration de la sécurité de votre trafic Azure SQL Managed Instance vers le stockage Azure__ : les stratégies de point de terminaison établissent un contrôle de sécurité qui empêche l’exfiltration erronée ou malveillante des données critiques de l’entreprise. Le trafic peut être limité uniquement aux comptes de stockage conformes à vos exigences de gouvernance des données.

- __Contrôle précis des comptes de stockage accessibles__ : les stratégies de point de terminaison de service peuvent autoriser le trafic vers les comptes de stockage au niveau d’un abonnement, d’un groupe de ressources et d’un compte de stockage individuel. Les administrateurs peuvent utiliser des stratégies de point de terminaison de service pour appliquer l’adhésion à l’architecture de sécurité des données de l’organisation dans Azure.

- __Le trafic système reste non affecté__ : les stratégies de point de terminaison de service ne bloquent jamais l’accès au stockage requis pour le fonctionnement d’Azure SQL Managed Instance. Cela comprend le stockage des sauvegardes, des fichiers de données, des fichiers journaux de transactions et d’autres ressources.

> [!IMPORTANT]
> Les stratégies de point de terminaison de service contrôlent uniquement le trafic qui provient du sous-réseau SQL Managed Instance et se termine dans le stockage Azure. Les stratégies n’affectent pas, par exemple, l’exportation de la base de données vers un fichier BACPAC local, l’intégration Azure Data Factory, la collecte d’informations de diagnostic via les paramètres de diagnostic Azure ni d’autres mécanismes d’extraction de données qui ne ciblent pas directement le stockage Azure.

## <a name="limitations"></a>Limitations

L’activation des stratégies de point de terminaison de service pour votre instance Azure SQL Managed Instance présente les limitations suivantes :

- En préversion, cette fonctionnalité est disponible dans toutes les régions Azure où SQL Managed Instance est pris en charge sauf **Chine Est 2**, **Chine Nord 2**, **USA Centre EUAP**, **USA Est 2 EUAP**, **US Gov Arizona**, **US Gov Texas**, **US Gov Virginie** et **USA Centre-Ouest**.
- La fonctionnalité est disponible uniquement pour les réseaux virtuels déployés à l’aide du modèle de déploiement Azure Resource Manager.
- La fonctionnalité est disponible uniquement dans les sous-réseaux qui ont des [points de terminaison de service](../../virtual-network/virtual-network-service-endpoints-overview.md) pour le stockage Azure activés.
- L’activation des points de terminaison de service pour le stockage Azure s’étend également de façon à inclure des régions jumelées dans lesquelles vous déployez le réseau virtuel pour prendre en charge le trafic du stockage géo-redondant avec accès en lecture (RA-GRS) et du stockage géo-redondant (GRS).
- L’attribution d’une stratégie de point de terminaison de service à un point de terminaison de service met à niveau le point de terminaison d’une étendue régionale à une étendue globale. En d’autres termes, tout le trafic vers le stockage Azure passe par le point de terminaison de service, quelle que soit la région dans laquelle le compte de stockage réside.

## <a name="prepare-storage-inventory"></a>Préparer l’inventaire de stockage

Avant de commencer la configuration des stratégies de point de terminaison de service sur un sous-réseau, composez la liste des comptes de stockage auxquels l’instance managée doit avoir accès dans ce sous-réseau.

Voici une liste de workflows qui peuvent contacter le stockage Azure :

- [Audit](auditing-configure.md) dans le stockage Azure.
- Exécution d’une [sauvegarde de copie uniquement](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) vers le stockage Azure.
- [Restauration](restore-sample-database-quickstart.md) d’une base de données à partir du stockage Azure.
- Importation de données avec [BULK INSERT ou OPENROWSET(BULK ...)](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server).
- Enregistrement des [événements étendus](../database/xevent-db-diff-from-svr.md) dans une cible de fichier d’événements sur le stockage Azure.
- [Migration hors connexion d’Azure DMS](../../dms/tutorial-sql-server-to-managed-instance.md) vers Azure SQL Managed Instance.
- [Migration de Log Replay Service](log-replay-service-migrate.md) vers Azure SQL Managed Instance.
- Synchronisation de tables en utilisant la [réplication transactionnelle](replication-transactional-overview.md).

Notez le nom du compte, le groupe de ressources et l’abonnement pour tout compte de stockage qui participe à ces workflows, ou à d’autres, qui accèdent au stockage. 


## <a name="configure-policies"></a>Configurer des stratégies

Vous devez d’abord créer votre stratégie de point de terminaison de service, puis l’associer au sous-réseau SQL Managed Instance. Modifiez le workflow de cette section pour l’adapter aux besoins de votre entreprise.


> [!NOTE]
> - Les sous-réseaux SQL Managed Instance nécessitent que les stratégies contiennent l’alias de service /Services/Azure/ManagedInstance (voir l’étape 5). 
> - Les instances managées déployées sur un sous-réseau qui contient déjà des stratégies de point de terminaison de service seront automatiquement mises à niveau avec l’alias de service /Services/Azure/ManagedInstance.

### <a name="create-a-service-endpoint-policy"></a>Créer une stratégie de point de terminaison de service

Pour créer une stratégie de point de terminaison de service, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Sélectionnez **+ Créer une ressource**. 
1. Dans le volet de recherche, entrez _stratégie de point de terminaison de service_, sélectionnez **Stratégie de point de terminaison de service**, puis **Créer**.

   ![Créer une stratégie de point de terminaison de service](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

1. Renseignez les valeurs suivantes dans la page **Informations de base** :

   - Abonnement : sélectionnez l’abonnement pour votre stratégie dans la liste déroulante.
   - Groupe de ressources : sélectionnez le groupe de ressources dans lequel se trouve votre instance managée, ou sélectionnez **Créer nouveau** et indiquez le nom d’un nouveau groupe de ressources.
   - Nom : indiquez un nom pour votre stratégie, par exemple **mySEP**.
   - Emplacement : sélectionnez la région du réseau virtuel qui héberge l’instance managée. 

   ![Informations de base sur la création d’une stratégie de point de terminaison de service](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

1. Dans **Définitions de stratégies**, sélectionnez **Ajouter un alias** et entrez les informations suivantes dans le volet **Ajouter un alias** :
   - Alias de service : sélectionnez /Services/Azure/ManagedInstance.
   - Sélectionnez **Ajouter** pour terminer l’ajout de l’alias de service.

   ![Ajouter un alias à une stratégie de point de terminaison de service](./media/service-endpoint-policies-configure/add-an-alias.png)

1. Dans Définitions de stratégies, sélectionnez **+ Ajouter** sous **Ressources**, puis entrez ou sélectionnez les informations suivantes dans le volet **Ajouter une ressource** :
   - Service : sélectionnez **Microsoft.Storage**.
   - Étendue : sélectionnez **Tous les comptes de l’abonnement**.
   - Abonnement : sélectionnez un abonnement contenant le ou les comptes de stockage à autoriser. Reportez-vous à votre [inventaire des comptes de stockage Azure](#prepare-storage-inventory) créé précédemment.
   - Sélectionnez **Ajouter** pour terminer l’ajout de la ressource.
   - Répétez cette étape pour ajouter d’autres abonnements. 

   ![Ajouter une ressource à une stratégie de point de terminaison de service](./media/service-endpoint-policies-configure/add-a-resource.png)

1. Facultatif : vous pouvez configurer des étiquettes sur la stratégie de point de terminaison de service sous **Étiquettes**.
1.  Sélectionnez **Vérifier + créer**. Validez les informations et sélectionnez **Créer**. Pour apporter d’autres modifications, sélectionnez **Précédent**.

   > [!TIP]
   > Tout d’abord, configurez des stratégies pour autoriser l’accès à des abonnements entiers. Validez la configuration en veillant à ce que tous les workflows fonctionnent normalement. Ensuite, si vous le souhaitez, reconfigurez les stratégies pour autoriser des comptes de stockage individuels ou des comptes dans un groupe de ressources. Pour ce faire, sélectionnez **Un seul compte** ou **Tous les comptes du groupe de ressources** dans le champ _Étendue :_ à la place et renseignez les autres champs en conséquence.

### <a name="associate-policy-with-subnet"></a>Associer une stratégie au sous-réseau

Une fois votre stratégie de point de terminaison de service créée, associez-la à votre sous-réseau SQL Managed Instance.

Pour associer votre stratégie, procédez comme suit :

1. Dans la zone _Tous les services_ du portail Azure, recherchez _réseaux virtuels_. Sélectionner **Réseaux virtuels**.
1. Recherchez et sélectionnez le réseau virtuel qui héberge votre instance managée.
1. Sélectionnez **Sous-réseaux** et choisissez le sous-réseau dédié à votre instance managée. Entrez les informations suivantes dans le volet du sous-réseau :
    - Services : sélectionnez **Microsoft.Storage**. Si ce champ est vide, vous devez configurer le point de terminaison de service pour le stockage Azure sur ce sous-réseau.
    - Stratégies de point de terminaison de service : sélectionnez les stratégies de point de terminaison de service que vous souhaitez appliquer au sous-réseau SQL Managed Instance.

   ![Associer une stratégie de point de terminaison de service à un sous-réseau](./media/service-endpoint-policies-configure/associate-service-endpoint-policy.png)

1. Sélectionnez **Enregistrer** pour terminer la configuration du réseau virtuel.

> [!WARNING]
> Si les stratégies sur ce sous-réseau n’ont pas l’alias `/Services/Azure/ManagedInstance`, vous pouvez voir l’erreur suivante : ` Failed to save subnet 'subnet'. Error: 'Found conflicts with NetworkIntentPolicy.`
> `Details: Service endpoint policies on subnet are missing definitions`
> Pour résoudre ce problème, mettez à jour toutes les stratégies sur le sous-réseau pour inclure l’alias `/Services/Azure/ManagedInstance`.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur la [sécurisation de vos comptes de stockage Azure](../../storage/common/storage-network-security.md).
- Découvrez les [fonctionnalités de sécurité de SQL Managed instance](../database/security-overview.md).
- Explorez l’[architecture de connectivité](connectivity-architecture-overview.md) de SQL Managed Instance.
