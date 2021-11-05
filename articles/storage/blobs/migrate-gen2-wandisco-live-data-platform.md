---
title: Data Lake Storage et WANdisco LiveData Platform for Azure
description: Découvrez comment migrer des pétaoctets de données Hadoop locales vers des systèmes de fichiers Azure Data Lake Storage Gen2 sans interrompre les opérations de données ni nécessiter des temps d’arrêt.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 10/26/2021
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b0b78d1233d6e605691e06c69c9d2167c7ecb592
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019217"
---
# <a name="migrate-on-premises-hadoop-data-to-azure-data-lake-storage-gen2-with-wandisco-livedata-platform-for-azure"></a>Migrer les données Hadoop locales vers Azure Data Lake Storage Gen2 en utilisant WANdisco LiveData Platform for Azure

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) migre des pétaoctets de données Hadoop locales vers des systèmes de fichiers Azure Data Lake Storage Gen2 sans interrompre les opérations de données ni nécessiter des temps d’arrêt. Les vérifications continues de la plateforme empêchent la perte de données tout en conservant la cohérence aux deux extrémités de la transférence, même lorsqu’elle subit une modification.

La plateforme se compose de deux services. [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) migre des données faisant l’objet d’une utilisation active depuis des environnements locaux vers le stockage Azure, et [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) garantit que toutes les données modifiées ou ingérées sont répliquées de façon cohérente.

> [!div class="mx-imgBorder"]
> ![Illustration – Vue d’ensemble de Live Data Platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Gérez les deux services à l’aide du portail Azure et d’Azure CLI. Chaque service suit le même modèle de facturation de paiement à l’utilisation limité que tous les autres services Azure : la consommation des données dans LiveData Platform for Azure apparaîtra sur la facture Azure mensuelle, qui fournira les métriques d’utilisation.

Contrairement à une migration des données effectuée *hors connexion* en [copiant des informations statiques dans Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) ou en utilisant des outils Hadoop comme [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), vous pouvez conserver un fonctionnement entièrement opérationnel de vos systèmes métier pendant la *migration* en ligne avec WANdisco LiveData for Azure. Gardez vos environnements Big Data opérationnels même lors du déplacement de leurs données vers Azure.

## <a name="key-benefits-of-wandisco-livedata-platform-for-azure"></a>Avantages clés de WANdisco LiveData Platform for Azure

Le moteur de consensus à compatibilité réseau à grande échelle de [WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) permet la cohérence des données et assure la réplication des données en temps réel à grande échelle. Regardez la vidéo suivante pour plus d’informations :<br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

Les avantages clés de la plateforme sont les suivants :

- **Précision des données** : la validation de bout en bout des données empêche la perte de données et garantit que les données transférées sont adaptées à l’utilisation.

- **Cohérence des données** : conservez les volumes de données automatiquement cohérents entre les environnements même lorsqu’ils subissent une modification continue.

- **Efficacité des données** : transférez continuellement des volumes de données importants avec un contrôle total de la consommation de bande passante.

- **Élimination des temps d’arrêt** : créez, modifiez, lisez et supprimez librement des données avec d’autres applications pendant la migration, sans avoir à perturber les opérations de l’entreprise pendant la transférence des données vers Azure. Continuez à faire fonctionner les applications, l’infrastructure d’analytique, les travaux d’ingestion et d’autres traitements.

- **Utilisation simple** : utilisez l’intégration Azure de la plateforme pour créer, configurer, planifier et suivre la progression des migrations automatisées. En outre, configurez la réplication sélective des données, les métadonnées Hive, la sécurité des données et la confidentialité en fonction des besoins.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Fonctionnalités clés de WANdisco LiveData Platform for Azure

Les fonctionnalités clés de la plateforme sont les suivantes :

- **Migration des métadonnées** : en plus des données HDFS, migrez les métadonnées (à partir de Hive et d’autres stockages) avec LiveData Migrator for Azure.

- **Transfert planifié** : utilisez LiveData Migrator for Azure pour contrôler et automatiser le moment où le transfert de données démarre, ce qui évite d’avoir à migrer manuellement les modifications apportées aux données.

- **Kerberos** :LiveData Migrator for Azure prend en charge les clusters Kerberos.

- **Modèles d’exclusion** : créez des règles dans LiveData Migrator for Azure pour empêcher la migration de certaines tailles de fichiers ou de certains noms de fichiers (définis à l’aide de modèles glob) vers votre stockage cible. Créez des modèles d’exclusion dans le portail Azure ou avec l’interface CLI, et appliquez-les à un nombre quelconque de migrations.

- **Mappages de chemin d’accès** : définissez d’autres chemins d’accès cibles pour des systèmes de fichiers cibles spécifiques, qui déplacent automatiquement les données transférées vers les répertoires que vous spécifiez.

- **Gestion de la bande passante** : configurez la quantité maximale de bande passante réseau que LiveData Migrator for Azure peut utiliser pour empêcher la consommation de bande passante.

- **Exclusions** : définissez des requêtes de modèle qui empêchent la migration de fichiers et de répertoires qui répondent aux critères, ce qui vous permet de migrer des données de votre système source de manière sélective.

- **Métriques** : affichez des détails sur le transfert de données dans LiveData Migrator for Azure, telles que les fichiers transférés au fil du temps, les chemins d’accès exclus, les éléments dont le transfert a échoué, etc.


   > [!div class="mx-imgBorder"]
   > ![LiveData](./media/migrate-gen2-wandisco-live-data-platform/metrics-1.png)

## <a name="migrate-big-data-faster-without-risk"></a>Migrer le Big Data plus rapidement sans risque

Le premier service inclus dans WANdisco LiveData Platform for Azure est [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) qui migre des données depuis des environnements locaux vers Stockage Azure. Une fois que vous avez déployé LiveData Migrator sur votre cluster Hadoop local, il créera automatiquement la meilleure configuration pour votre système de fichiers. À partir de là, fournissez les détails Kerberos pour le système. LiveData Migrator for Azure est ensuite prêt à migrer les données vers Stockage Azure.

> [!div class="mx-imgBorder"]
> ![Architecture de LiveData Migrator for Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture-1.png)

Avant de commencer avec LiveData Migrator for Azure, passez en revue les [prérequis](https://docs.wandisco.com/live-data-platform/docs/prereq/).

Pour effectuer une migration :

1. Dans l’interface de ligne de commande Azure : 

   - Inscrivez-vous pour le fournisseur de ressources WANdisco dans l’Azure CLI en exécutant `az provider register --namespace Wandisco.Fusion --consent-to-permissions`.
   - Acceptez les conditions de facturation limitées de LiveData Platform en exécutant `az vm image terms accept --offer ldma --plan metered-v1 --publisher Wandisco --subscription <subscriptionID>`.

2. Déployez l’instance LiveData Migrator à partir du portail Azure sur votre cluster Hadoop local. (Vous n’avez pas besoin d’apporter des modifications au cluster ou de le redémarrer.)

   > [!div class="mx-imgBorder"]
   > ![Créer une instance LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

   > [!NOTE]
   > WANdisco LiveData Migrator for Azure offre la possibilité de créer un cluster de test Hadoop.

3. Configurez les détails Kerberos, le cas échéant.

4. Définissez le compte de stockage cible Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   > ![Créer une cible LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

5. Définissez l’emplacement des données que vous voulez migrer, par exemple `/user/hive/warehouse`.

   > [!div class="mx-imgBorder"]
   > ![Créer une migration LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

6. Démarrer la migration.

Surveillez la progression de la migration via les outils Azure standard, y compris l’Azure CLI et le portail Azure.

Pour obtenir des instructions plus détaillées, consultez la [série de vidéos LiveData Migrator for Azure How-To](https://fast.wistia.com/embed/channel/qg51p8erky).

## <a name="bidirectionally-replicate-data-under-active-change-with-livedata-plane-for-azure"></a>Répliquer les données de manière bidirectionnelle en cas de modification active avec LiveData Plane for Azure

Le deuxième service inclus dans LiveData Platform est [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure). LiveData Plane utilise le moteur de coordination de WANdisco pour assurer la cohérence des données entre plusieurs clusters Hadoop locaux et Stockage Azure en appliquant intelligemment des modifications aux données sur tous les systèmes, éliminant ainsi le risque de conflits de données à différents points d’utilisation.

> [!div class="mx-imgBorder"]
> ![Architecture de LiveData Plane for Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Après la migration initiale, assurez la cohérence de vos données avec LiveData Plane for Azure :

1. Déployez LiveData Plane for Azure localement et dans Azure, en commençant à partir du portail Azure. Aucune modification des applications n’est nécessaire.

2. Configurez des règles de réplication qui couvrent les emplacements de ces données que vous voulez conserver cohérentes, par exemple : `/user/contoso/sales/region/WA`.

3. Exécutez les applications qui accèdent aux données et les modifient dans les deux emplacements, selon vos besoins.

LiveData Plane for Azure réplique régulièrement les modifications de données dans tous les environnements, sans impact significatif sur les performances des opérations ou des applications du cluster.

## <a name="test-drive-or-trial"></a>Version d'évaluation ou essai

À partir de [LiveData Platform pour la page Marketplace d’Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview), vous disposez de deux options :

- Le bouton **Obtenir maintenant** lance le service dans votre abonnement. À partir de là, vous pouvez utiliser votre propre cluster Hadoop ou le cluster d’essai de WANdisco.

- Cliquez sur **Version d'évaluation** pour tester LiveData Migrator for Azure dans un environnement préconfiguré et hébergé pour vous. Cela vous permet d’essayer LiveData Migrator for Azure avant de l’ajouter à votre abonnement, sans coût ni risque pour vos données.

Regardez la [vidéo de démonstration Version d'évaluation](https://fast.wistia.net/embed/channel/qg51p8erky?wchannelid=qg51p8erky&wmediaid=ute6gsc60w) pour voir la version d'évaluation en action.

## <a name="next-steps"></a>Étapes suivantes

- [Planifiez et créez une migration dans LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma).

## <a name="see-also"></a>Voir aussi

- [LiveData Migrator for Azure sur la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)

- [Plans et tarification de LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=PlansAndPricee)

- [Questions fréquentes (FAQ) sur LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problèmes connus avec LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)
