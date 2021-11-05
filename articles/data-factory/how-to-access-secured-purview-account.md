---
title: Accéder à un compte Azure Purview sécurisé
description: Découvrez la procédure d’accès à un compte Azure Purview protégé par un pare-feu par le biais de points de terminaison privés à partir d’Azure Data Factory
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 09/02/2021
ms.openlocfilehash: 51da9cba732c5654709d0c1e84398035d4675cc8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005724"
---
# <a name="access-a-secured-azure-purview-account-from-azure-data-factory"></a>Accéder à un compte Azure Purview sécurisé à partir d'Azure Data Factory

Cet article explique la procédure d’accès à un compte Azure Purview sécurisé à partir d’Azure Data Factory pour différents scénarios d’intégration.

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Scénarios de déploiement d’un point de terminaison privé Azure Purview

Vous pouvez utiliser des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md) pour vos comptes Azure Purview afin d’autoriser un accès sécurisé au catalogue à partir d’un réseau virtuel (VNet) via une liaison privée. Purview fournit différents types de points privés pour différents besoins d’accès : le point de terminaison privé de *compte*, le point de terminaison privé du *portail* et les points de terminaison privés d’*ingestion*. Pour en savoir plus, consultez [Vue d’ensemble conceptuelle des points de terminaison privés de Purview](../purview/catalog-private-link.md#conceptual-overview). 

Si votre compte Purview est protégé par un pare-feu et refuse l’accès public, veillez à suivre la liste de vérification ci-dessous pour configurer les points de terminaison privés de sorte que Data Factory puisse se connecter à Purview. 

| Scénario                                                     | Points de terminaison privés Purview obligatoires                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Exécuter le pipeline et la traçabilité des rapports dans Purview](tutorial-push-lineage-to-purview.md) | Pour que le pipeline Data Factory envoie la traçabilité à Purview, des points de terminaison privés de **compte** et d’_*_ingestion_*_ Purview* sont obligatoires. <br>- Si vous utilisez _*Azure Integration Runtime**, suivez la procédure de la section [Points de terminaison privés managés pour Purview](#managed-private-endpoints-for-purview) pour créer des points de terminaison privés managés dans le réseau virtuel managé de Data Factory.<br>- Si vous utilisez un **Runtime d’intégration auto-hébergé**, suivez la procédure de [cette section](../purview/catalog-private-link-end-to-end.md#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts) pour créer les points de terminaison privés de *compte* et d’*ingestion* dans le réseau virtuel de votre runtime d’intégration. |
| [Découvrir et explorer des données avec Purview dans l’interface utilisateur ADF](how-to-discover-explore-purview-data.md) | Pour utiliser la barre de recherche située en haut au centre de l’interface utilisateur de création de Data Factory pour rechercher des données Purview et exécuter des actions, vous devez créer des points de terminaison privés de **compte** et de _*_portail_*_ dans le réseau virtuel dans lequel vous lancez Data Factory Studio. Suivez la procédure indiquée dans la section [Activer le point de terminaison privé de compte* et du *portail*](../purview/catalog-private-link-account-portal.md#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts). |

## <a name="managed-private-endpoints-for-purview"></a>Points de terminaison privés managés pour Purview

Les [points de terminaison privés managés](managed-virtual-network-private-endpoint.md#managed-private-endpoints) sont des points de terminaison privés créés sur le réseau virtuel managé Azure Data Factory qui établissent une liaison privée vers des ressources Azure. Lorsque vous exécutez le pipeline et la traçabilité des rapports dans un compte Azure Purview protégé par un pare-feu, créez un Azure Integration Runtime pour lequel l’option « Configuration de réseau virtuel » est activée, puis créez les points de terminaison privés managés Purview de **compte** et d’*_ingestion_** comme suit.

### <a name="create-managed-private-endpoints"></a>Créer des points de terminaison privés managés

Pour créer des points de terminaison privés managés pour Purview dans l’interface utilisateur de création Data Factory :

1. Accédez à **Gérer** -> **Azure Purview**, puis cliquez sur **Modifier** pour modifier votre compte Purview connecté existant ou sur **Se connecter à un compte Purview** pour vous connecter à un nouveau compte Purview.

2. Sélectionnez **Oui** pour **Créer des points de terminaison privés managés**. Vous devez au moins disposer d’un Azure Integration Runtime pour lequel l’option « Configuration du réseau virtuel » est activée dans la fabrique de données pour afficher cette option.

3. Cliquez sur le bouton **+ Créer tout** pour créer par lot les points de terminaison privés Purview nécessaires, notamment le point de terminaison privé de **_compte_ *et les points de terminaison privés d’* _ingestion_*pour les ressources managées Purview (stockage d’objets blob, stockage de file d’attente et espace de noms Event Hubs). Vous devez au moins disposer du rôle* Lecteur** sur votre compte Purview pour que Data Factory récupère les informations des ressources managées Purview.

   :::image type="content" source="./media/how-to-access-secured-purview-account/purview-create-all-managed-private-endpoints.png" alt-text="Créez un point de terminaison privé managé pour votre compte Purview connecté.":::

4. Sur la page suivante, spécifiez un nom pour le point de terminaison privé. Il sera utilisé pour générer des noms pour les points de terminaison privés d’ingestion, ainsi qu’avec le suffixe.

   :::image type="content" source="./media/how-to-access-secured-purview-account/name-purview-private-endpoints.png" alt-text="Nommez les points de terminaison privés managés pour votre compte Purview connecté.":::

5. Cliquez sur **Créer** pour créer les points de terminaison privés. Après la création, 4 requêtes de point de terminaison privé sont générées et doivent [être approuvées par un propriétaire de Purview](#approve-private-endpoint-connections).

La création de points de terminaison privés managés par lots est fournie uniquement dans l’IU de Purview. Si vous souhaitez créer des points de terminaison privés managés par programme, vous devez créer ces points de terminaison individuellement. Vous trouverez des informations sur les ressources managées Purview dans le portail Azure -> votre compte Purview -> Ressources managées.

### <a name="approve-private-endpoint-connections"></a>Approuver les connexions de point de terminaison privé

Une fois que vous avez créé les points de terminaison privés managés pour Purview, l’état « En attente » s’affiche en premier. Le propriétaire de Purview doit approuver les connexions de point de terminaison privé pour chaque ressource.

Si vous avez l’autorisation d’approuver la connexion du point de terminaison privé Purview, dans l’interface utilisateur de Data Factory : 

1. Accédez à **Gérer** -> **Azure Purview** -> **Modifier**
2. Dans la liste des points de terminaison privés, cliquez sur le bouton **Modifier** (crayon) en regard de chaque nom de point de terminaison privé
3. Cliquez sur **Gérer les approbations dans le portail Azure** pour afficher la ressource.
4. Sur la ressource donnée, accédez à **Mise en réseau** -> **Connexion du point de terminaison privé** pour l’approuver. Le point de terminaison privé est nommé `data_factory_name.your_defined_private_endpoint_name` avec la description « Demandé par data_factory_name ».
5. Répétez cette opération pour tous les points de terminaison privés.

Si vous n’êtes pas autorisé à approuver la connexion au point de terminaison privé Purview, demandez au propriétaire du compte Purview de procéder comme suit.

- Pour le point de terminaison privé du *compte* , accédez au portail Azure -> votre compte Purview -> Mise en réseau -> Connexion de point de terminaison privé pour approuver.
- Pour les points de terminaison privés d'*ingestion*, accédez au portail Azure -> votre compte Purview -> Ressources managées, cliquez respectivement dans le compte de stockage et l’espace de noms Event Hubs, puis approuvez la connexion de point de terminaison privé sur la page Mise en réseau -> Connexion du point de terminaison privé.

### <a name="monitor-managed-private-endpoints"></a>Surveiller des points de terminaison privés managés

Vous pouvez surveiller les points de terminaison privés managés créés pour Purview à deux endroits :

- Accédez à **Gérer** -> **Azure Purview** -> **Modifier** pour ouvrir votre compte Purview connecté existant. Pour afficher tous les points de terminaison privés pertinents, vous devez au moins disposer du rôle **Lecteur** sur votre compte Purview pour que Data Factory récupère les informations des ressources managées Purview. Dans le cas contraire, vous ne voyez que le point de terminaison privé du *compte* avec un avertissement.
- Accédez à **Gérer** -> **Points de terminaison managés privés**, où vous voyez tous les points de terminaison managés privés créés sous la fabrique de données. Si vous disposez au moins du rôle **Lecteur** sur votre compte Purview, vous voyez les points de terminaison privés Purview correspondants qui sont regroupés. Dans le cas contraire, ils apparaissent séparément dans la liste.

## <a name="nextsteps"></a>Étapes suivantes 

- [Connecter Data Factory à Azure Purview](connect-data-factory-to-azure-purview.md)
- [Tutoriel : Envoyer les données de traçabilité Data Factory à Azure Purview](tutorial-push-lineage-to-purview.md)
- [Découvrir et explorer des données dans ADF avec Purview](how-to-discover-explore-purview-data.md)
