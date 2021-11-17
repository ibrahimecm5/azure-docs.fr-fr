---
title: Comment ajouter un principal du service Cosmos DB pour Azure Managed Instance pour Apache Cassandra
description: Découvrez comment ajouter un principal de service Cosmos DB à un réseau virtuel existant pour Azure Managed Instance pour Apache Cassandra
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fd7f23db4b4df9b1153ac8b00afb35c265d0be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858644"
---
# <a name="use-azure-portal-to-add-cosmos-db-service-principal"></a>Utiliser le portail Azure pour ajouter un principal du service Cosmos DB

Pour un déploiement réussi dans un réseau virtuel existant, Azure Managed Instance pour Apache Cassandra nécessite le principal de service Azure Cosmos DB avec un rôle (tel que Contributeur de réseau) autorisant l’action `Microsoft.Network/virtualNetworks/subnets/join/action`. Dans certains cas, il peut être nécessaire d’ajouter ces autorisations manuellement. Cet article explique comment effectuer cette opération à l’aide du portail Azure. 

## <a name="add-cosmos-db-service-principal"></a>Ajouter un principal de service Cosmos DB

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez au réseau virtuel cible dans votre abonnement, sélectionnez l’onglet Contrôle d’accès, puis cliquez sur `add role assignment` :

   :::image type="content" source="./media/add-service-principal/service-principal-1.png" alt-text="Ajouter une attribution de rôle" lightbox="./media/add-service-principal/service-principal-1.png" border="true"::: 

1. Recherchez le rôle `Network Contributor`, mettez-le en surbrillance, puis sélectionnez l’onglet `members` :

   :::image type="content" source="./media/add-service-principal/service-principal-2.png" alt-text="Ajouter un contributeur de réseau" lightbox="./media/add-service-principal/service-principal-2.png" border="true"::: 

   > [!NOTE]
   > Vous n’avez pas besoin de disposer d’un rôle avec des autorisations aussi étendues que celles d’un Contributeur de réseau, car il est utilisé en guise d’exemple par souci de simplicité. Vous pouvez également créer un rôle de client avec des autorisations plus restreinte, à condition qu’il autorise l’action `Microsoft.Network/virtualNetworks/subnets/join/action`.

1. Vérifiez que `User, group, or service principal` est sélectionné pour `Assign access to`, puis cliquez sur `Select members` pour rechercher le principal de service `Azure Cosmos DB`. Sélectionnez-le dans la fenêtre de droite :

   :::image type="content" source="./media/add-service-principal/service-principal-3.png" alt-text="Sélectionner un principal de service Cosmos DB" lightbox="./media/add-service-principal/service-principal-3.png" border="true"::: 

1. Cliquez sur l’onglet `Review + assign` en haut, puis sur le bouton `Review + assign` en bas. Le principal de service Cosmos DB doit maintenant être assigné. 

   :::image type="content" source="./media/add-service-principal/service-principal-4.png" alt-text="Examiner et affecter" lightbox="./media/add-service-principal/service-principal-4.png" border="true"::: 

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment affecter le principal du service Azure Cosmos DB avec un rôle approprié à un réseau virtuel afin d’autoriser les déploiements managés de Cassandra. Explorez plus en détail Azure Managed Instance pour Apache Cassandra avec les articles suivants :

* [Vue d’ensemble d’Azure Managed Instance pour Apache Cassandra](introduction.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)
