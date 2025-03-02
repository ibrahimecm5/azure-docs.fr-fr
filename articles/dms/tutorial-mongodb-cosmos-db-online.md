---
title: 'Tutoriel : Migrer MongoDB en ligne vers l’API Azure Cosmos DB pour MongoDB'
titleSuffix: Azure Database Migration Service
description: Découvrez comment effectuer une migration en ligne d’une instance locale de MongoDB vers l’API Azure Cosmos DB pour MongoDB à l’aide d’Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 09/21/2021
ms.openlocfilehash: 1d5198365465e07e393aef1c155416a444071385
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578694"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Tutoriel : Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB en ligne à l’aide de DMS
[!INCLUDE[appliesto-mongodb-api](../cosmos-db/includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Veuillez lire ce guide dans son intégralité avant d’effectuer les étapes de migration.
>

Ce guide de migration de MongoDB fait partie d’une série sur la migration de MongoDB. Les étapes critiques de la migration de MongoDB sont la [pré-migration](../cosmos-db/mongodb-pre-migration.md), la migration proprement dite et la [post-migration](../cosmos-db/mongodb-post-migration.md), comme indiqué ci-dessous.

![Diagramme des étapes de migration.](../cosmos-db/mongodb/media/pre-migration-steps/overall-migration-steps.png)

## <a name="overview-of-online-data-migration-from-mongodb-to-azure-cosmos-db-using-dms"></a>Vue d’ensemble de la migration des données en ligne de MongoDB vers Azure Cosmos DB avec DMS

Vous pouvez utiliser Azure Database Migration Service pour effectuer la migration en ligne (temps d’arrêt minimal) de bases de données à partir d’une instance locale ou cloud de MongoDB vers l’API MongoDB Azure Cosmos DB.

Ce tutoriel explique les étapes associées à l’utilisation d’Azure Database Migration Service pour migrer des données MongoDB vers Azure Cosmos DB :
> [!div class="checklist"]
> 
> * Créer une instance Azure Database Migration Service. 
> * Créez un projet de migration. 
> * Spécifiez la source. 
> * Spécifiez la cible. 
> * Effectuez un mappage aux bases de données cibles. 
> * Exécuter la migration. 
> * Surveiller la migration. 
> * Vérifiez les données dans Azure Cosmos DB. 
> * Effectuer la migration quand vous êtes prêt 

Dans ce tutoriel, vous allez effectuer la migration d’un jeu de données MongoDB entre une machine virtuelle Azure et l’API MongoDB Azure Cosmos DB à l’aide d’Azure Database Migration Service, pour un temps d’arrêt minimal. Si vous n'avez pas encore configuré d'instance source de MongoDB, consultez l'article [Installer et configurer MongoDB sur une machine virtuelle Windows dans Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> L’utilisation d’Azure Database Migration Service pour effectuer une migration en ligne nécessite la création d’une instance basée sur le niveau tarifaire Premium.

> [!IMPORTANT]
> Pour une expérience de migration optimale, Microsoft recommande de créer une instance Azure Database Migration Service dans la même région Azure que la base de données cible. Le déplacement des données entre les régions ou les zones géographiques peut ralentir le processus de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration en ligne de MongoDB vers l’API Azure Cosmos DB pour MongoDB. Pour une migration hors connexion, consultez [Migrer MongoDB vers l’API Azure Cosmos DB pour MongoDB hors connexion à l’aide de DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

* Les étapes[Exécuter la prémigration](../cosmos-db/mongodb-pre-migration.md), comme l’estimation du débit, le choix d’une clé de partition et la stratégie d’indexation.
* [Créez un compte API d’Azure Cosmos DB pour MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) et vérifiez que [SSR (nouvelle tentative côté serveur)](../cosmos-db/mongodb/prevent-rate-limiting-errors.md) est activé.

  > [!NOTE]
  > DMS n’est pas pris en charge si vous effectuez une migration vers un compte d’API pour MongoDB qui est provisionné en mode serverless.

* Créez un réseau virtuel Microsoft Azure pour Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité site à site à vos serveurs sources locaux via [ExpressRoute](../expressroute/expressroute-introduction.md) ou un [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](../virtual-network/virtual-network-service-endpoints-overview.md) au sous-réseau où doit être provisionné le service :
    >
    > * Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > * Point de terminaison de stockage
    > * Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

* Vérifiez que les règles de groupe de sécurité de votre réseau virtuel ne bloquent pas les ports de communication suivants : 53, 443, 445, 9354 et 10000-20000. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel, consultez l’article [Filtrer le trafic avec les groupes de sécurité réseau](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Ouvrez votre pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur MongoDB source via le port TCP 27017 (par défaut).
* Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Configurer les nouvelles tentatives Azure Cosmos DB côté serveur pour une migration efficace

Les clients qui effectuent une migration à partir de MongoDB vers Azure Cosmos DB bénéficient des fonctionnalités de gouvernance des ressources, qui vous permettent d’utiliser pleinement vos RU/s de débit provisionnés. Azure Cosmos DB peut limiter une demande de service de migration de données au cours de la migration si cette demande dépasse les RU/s provisionnés du conteneur. Vous devrez alors retenter cette demande. Le service de migration des données est capable d’effectuer de nouvelles tentatives. Cependant, le temps d’aller-retour nécessaire au tronçon réseau entre le service de migration des données et Azure Cosmos DB impacte le temps de réponse global de cette requête. L’amélioration du temps de réponse aux demandes limitées peut raccourcir le temps total nécessaire à la migration. La fonctionnalité de *nouvelle tentative côté serveur* d’Azure Cosmos DB permet au service d’intercepter les codes d’erreur de limitation et d’effectuer une nouvelle tentative avec un temps d’aller-retour beaucoup plus court, améliorant ainsi considérablement les temps de réponse aux requêtes.

La fonctionnalité de nouvelle tentative côté serveur est accessible via le panneau *Fonctionnalités* du portail Azure Cosmos DB.

![Capture d’écran de la fonctionnalité de nouvelle tentative côté serveur MongoDB.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

Si cette fonctionnalité est *désactivée*, nous vous recommandons de l’activer comme indiqué ci-dessous.

![Capture d’écran de l’activation de la fonctionnalité de nouvelle tentative côté serveur MongoDB.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)] 

## <a name="create-an-instance"></a>Créer une instance

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service.

5. Sélectionnez un réseau virtuel existant ou créez-en un.

   Le réseau virtuel fournit à Azure Database Migration Service un accès à l’instance MongoDB source et au compte Azure Cosmos DB cible.

   Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](../virtual-network/quick-create-portal.md).

6. Sélectionnez une référence (SKU) dans le niveau tarifaire Premium.

    > [!NOTE]
    > Les migrations en ligne sont uniquement prises en charge lorsque vous utilisez le niveau Premium. Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

    ![Localiser toutes les instances Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.

    Vous pouvez également découvrir l’instance Azure Database Migration Service à partir du volet de recherche du portail Azure.

    ![Utiliser le volet de recherche dans le Portail Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Sélectionnez **+ Nouveau projet de migration**.

4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **MongoDB**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Cosmos DB (API MongoDB)** , puis dans **Choisir un type d’activité**, sélectionnez **Migration de données en ligne [préversion]** .

    ![Créer un projet Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Sélectionnez **Enregistrer**, puis **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Sur l'écran **Détails de la source**, spécifiez les détails de connexion du serveur MongoDB source.

   > [!IMPORTANT]
   > Azure Database Migration Service ne prend pas en charge Azure Cosmos DB comme source.

    Il existe trois modes de connexion à une source :
   * **Mode standard**, qui accepte un nom de domaine complet ou une adresse IP, un numéro de port et des informations d’identification de connexion.
   * **Mode de chaîne de connexion**, qui accepte une chaîne de connexion MongoDB comme décrit dans l’article sur le [format d’URI de la chaîne de connexion](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Mode de données du stockage Azure**, qui accepte une URL SAP du conteneur d’objets blob. Sélectionnez **L’objet blob contient des images mémoire BSON** si le conteneur d’objets blob contient des images mémoire BSON produites par l’[outil bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) MongoDB, et désélectionnez-le si le conteneur contient des fichiers JSON.

     Si vous sélectionnez cette option, vérifiez que la chaîne de connexion de compte de stockage s’affiche au format suivant :

     ```
     https://blobnameurl/container?SASKEY
     ```

     De plus, en fonction des informations d’images mémoire de type qui se trouvent dans le stockage Azure, gardez à l’esprit les informations suivantes.

     * Pour les images mémoire BSON, les données dans le conteneur d’objets blob doivent être au format bsondump, de sorte que les fichiers de données sont placés dans des dossiers nommés d’après les bases de données conteneur au format collection.bson. Les fichiers de métadonnées (le cas échéant) doivent être nommés en utilisant le format *collection*.metadata.json.

     * Pour les images mémoire JSON, les fichiers présents dans le conteneur d’objets blob doivent être placés dans des dossiers nommés d’après les bases de données conteneur. Dans chaque dossier de base de données, les fichiers de données doivent être placés dans un sous-dossier appelé « data » et nommé en utilisant le format *collection*.json. Les fichiers de métadonnées (le cas échéant) doivent être placés dans un sous-dossier appelé « metadata » et nommé en utilisant le même format, *collection*.json. Les fichiers de métadonnées doivent être au même format que celui produit par l’outil de bsondump MongoDB.

    > [!IMPORTANT]
    > Il est déconseillé d’utiliser un certificat autosigné sur le serveur mongo. Toutefois, si un tel certificat est utilisé, connectez-vous au serveur en utilisant le **mode chaîne de connexion** et vérifiez que votre chaîne de connexion a “”
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Vous pouvez utiliser l’adresse IP quand la résolution de noms DNS est impossible.

   ![Spécifier les détails de la source](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > L’adresse du serveur source doit être l’adresse du serveur principal si la source est un jeu de réplicas, et le routeur si la source est un cluster MongoDB shardé. Pour un cluster MongoDB shardé, Azure Database Migration Service doit pouvoir se connecter aux shards individuels du cluster, ce qui peut nécessiter l’ouverture du pare-feu sur des machines supplémentaires.

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Sur l’écran **Détails de la cible de migration**, spécifiez les détails de connexion du compte Azure Cosmos DB cible, à savoir le compte d’API Azure Cosmos DB pour MongoDB préprovisionné vers lequel vous migrez vos données MongoDB.

    ![Spécifier les détails de la cible](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Sélectionnez **Enregistrer**.

## <a name="map-to-target-databases"></a>Mapper aux bases de données cibles

1. Dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

   Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

   Si la chaîne **Create** s’affiche en regard du nom de la base de données, cela indique qu’Azure Database Migration Service n’a pas trouvé la base de données cible et qu’il va créer la base de données pour vous.

   À ce stade de la migration, si vous souhaitez un débit de partage pour la base de données, spécifiez un RU de débit. Dans Cosmos DB, vous pouvez configurer le débit au niveau de la base de données ou individuellement pour chaque collection. Le débit est mesuré en [unités de requête](../cosmos-db/request-units.md) (RU). Apprenez-en davantage sur les [tarifs d'Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapper aux bases de données cibles](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Sélectionnez **Enregistrer**.

3. Sur l'écran **Paramètre de collection**, développez la liste des collections, puis consultez la liste des collections à migrer.

   Azure Database Migration Service sélectionne automatiquement toutes les collections qui se trouvent sur l’instance source de MongoDB et qui ne se trouvent pas dans le compte Azure Cosmos DB cible. Si vous souhaitez migrer à nouveau des collections contenant déjà des données, vous devez les sélectionner explicitement dans cet écran.

   Vous pouvez spécifier le nombre de RU à utiliser par les collections. Dans la plupart des cas, une valeur comprise entre 500 (1 000 au minimum pour les collections shardées) et 4 000 doit suffire. Azure Database Migration Service suggère des valeurs par défaut intelligentes en fonction de la taille de la collection.

    > [!NOTE]
    > Effectuez la migration et la collection de la de base de données en parallèle, en utilisant si nécessaire plusieurs instances d’Azure Database Migration Service, afin d’accélérer l’exécution.

   Vous pouvez également spécifier une clé de partition afin de tirer parti du [partitionnement dans Azure Cosmos DB](../cosmos-db/partitioning-overview.md) pour une extensibilité optimale. Veillez à consulter les [meilleures pratiques de sélection d'une clé de partition](../cosmos-db/partitioning-overview.md#choose-partitionkey). Si vous n’avez pas de clé de partition, vous pouvez toujours utiliser **_id** comme clé de sharding pour un meilleur débit.

   ![Sélectionner des tables de collections](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Sélectionnez **Enregistrer**.

5. Dans l’écran **Récapitulatif de la migration**, spécifiez un nom pour l’activité de migration dans la zone de texte **Nom de l’activité**.

    ![Résumé de la migration](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Exécuter la migration

* Sélectionnez **Exécuter la migration**.

   La fenêtre d’activité de migration s’affiche, et l’**état** de l’activité est indiqué.

   ![État de l'activité](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Surveiller la migration

* Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à ce que l’**état** de la migration indique **Relecture**.

   > [!NOTE]
   > Vous pouvez sélectionner l'activité pour obtenir des détails sur les métriques de migration au niveau des bases de données et des collections.

   ![État d’activité Relecture](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Vérifier les données dans Cosmos DB

1. Apportez des changements à votre base de données MongoDB source.
2. Connectez-vous à Cosmos DB pour vérifier si les données sont répliquées à partir du serveur MongoDB source.

    ![Capture d’écran montrant où vous pouvez vérifier que les données ont été répliquées](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Effectuer la migration

* Une fois que tous les documents de la source sont disponibles sur la cible Cosmos DB, sélectionnez **Terminer** dans le menu contextuel de l’activité de migration pour effectuer la migration.

    Cette action va permettre d’achever la relecture de toutes les modifications en attente et entraîner l’exécution de la migration.

    ![Capture d’écran montrant l’option de menu Terminer](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimisation de la post-migration

Après avoir migré les données stockées dans la base de données MongoDB vers l’API Azure Cosmos DB pour MongoDB, vous pouvez vous connecter à Azure Cosmos DB et gérer les données. Vous pouvez également effectuer d’autres étapes d’optimisation de la post-migration, comme l’optimisation de la stratégie d’indexation, la mise à jour du niveau de cohérence par défaut ou la configuration de la distribution mondiale pour votre compte Azure Cosmos DB. Pour plus d’informations, consultez l’article [Optimisation de la post-migration](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Informations sur le service Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ?
    * Si vous ne connaissez que le nombre de vCores et de serveurs présents dans votre cluster de bases de données existant, lisez [Estimation des unités de requête à l’aide de vCores ou de processeurs virtuels](../cosmos-db/convert-vcore-to-request-unit.md) 
    * Si vous connaissez les taux de requêtes typiques de la charge de travail actuelle des bases de données, consultez [Estimation des unités de requête avec Azure Cosmos DB Capacity Planner](../cosmos-db/mongodb/estimate-ru-capacity-planner.md)

## <a name="next-steps"></a>Étapes suivantes

* Pour accéder à d'autres scénarios, lisez les instructions de migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.