---
title: Effectuer la migration d’un cluster vers une version plus récente
titleSuffix: Azure HDInsight
description: Découvrez comment effectuer la migration d’un cluster Azure HDInsight vers une version plus récente.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 6fa655092de1c3e103381ccd58bf840b70a3809e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249758"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Effectuer la migration d’un cluster HDInsight vers une version plus récente

Pour tirer parti des dernières fonctionnalités proposées par HDInsight, nous vous recommandons d’effectuer régulièrement la migration des clusters HDInsight vers la version la plus récente. HDInsight ne prend pas en charge les mises à niveau sur place lorsqu’un cluster existant est mis à niveau vers une nouvelle version de composant. Vous devez créer un nouveau cluster avec la version de composant et la version de plateforme souhaitées, puis effectuer la migration de vos applications pour qu’elles utilisent le nouveau cluster. Suivez les instructions ci-dessous pour effectuer la migration de vos clusters HDInsight.

> [!NOTE]  
> Pour obtenir des informations sur les versions HDInsight prises en charge, consultez [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Tâches de migration

Le workflow pour mettre à niveau un cluster HDInsight est le suivant :
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="Schéma du workflow de mise à niveau HDInsight" border="false":::

1. Lisez chaque section de ce document pour comprendre les modifications qui peuvent être nécessaires lors de la mise à jour de votre cluster HDInsight.
2. Créez un cluster comme environnement de test ou d’assurance qualité. Pour plus d’informations sur la création d’un cluster, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Copiez les travaux, sources de données et récepteurs existants dans le nouvel environnement.
4. Effectuez des tests de validation pour vérifier que vos tâches fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifiez un temps d’arrêt pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes :

1. Sauvegardez toutes les données temporaires stockées localement sur les nœuds du cluster, par exemple si vous avez des données stockées directement sur un nœud principal.
1. [Supprimez le cluster existant](./hdinsight-delete-cluster.md).
1. Créez un cluster dans le même sous-réseau de réseau virtuel avec la version HDI la plus récente (ou prise en charge) à l’aide du même magasin de données par défaut que le cluster précédent a utilisé. Cela permet au nouveau cluster de continuer à travailler sur vos données de production existantes.
1. Importez toutes les données temporaires que vous avez sauvegardées.
1. Démarrez des tâches ou poursuivez le traitement avec le nouveau cluster.

## <a name="workload-specific-guidance"></a>Recommandations relatives aux charges de travail

Les documents suivants fournissent des conseils sur la migration de certaines charges de travail :

* [Migration HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migration Kafka](./kafka/migrate-versions.md)
* [Migration Hive/Interactive Query](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Sauvegarde et restauration

Pour plus d’informations sur la sauvegarde et la restauration d’une base de données, consultez [Récupérer une base de données dans Azure SQL Database à l’aide des sauvegardes de base de données automatisées](../azure-sql/database/recovery-using-backups.md).

## <a name="upgrade-scenarios"></a>Scénarios de mise à niveau

Comme mentionné plus haut, Microsoft recommande de migrer régulièrement les clusters HDInsight vers la dernière version afin de bénéficier des nouvelles fonctionnalités et des derniers correctifs. Consultez les différentes raisons listées ci-dessous pour lesquelles nous pourrions vous demander de supprimer et redéployer un cluster :

* La version du cluster est [mise hors service](hdinsight-retired-versions.md) ou est passée en [support De base](hdinsight-36-component-versioning.md) et vous rencontrez un problème de cluster qui serait résolu avec une version plus récente.
* La cause racine d’un problème de cluster a été identifiée comme étant en lien avec une machine virtuelle sous-dimensionnée. [Consultez la configuration de nœuds recommandée par Microsoft](hdinsight-supported-node-configuration.md#all-supported-regions-except-brazil-south-and-japan-west).
* Un client ouvre un cas de support et l’équipe technique de Microsoft détermine que le problème a déjà été résolu dans une version du cluster plus récente.
* Une base de données du metastore par défaut (Ambari, Hive, Oozie, Ranger) a atteint sa limite d’utilisation. Dans ce cas, Microsoft vous demande de recréer le cluster avec une base de données d’un [ metastore personnalisé](hdinsight-use-external-metadata-stores.md#custom-metastore).
* La cause racine d’un problème de cluster est liée à une **opération non prise en charge**. Voici quelques opérations courantes non prises en charge :
     * **Déplacement ou ajout d’un service dans Ambari**. Quand vous affichez des informations sur les services de cluster dans Ambari, l’une des actions disponibles dans le menu Service Actions (Actions de service) est **Move [Service Name]** (Déplacer [Nom du service]). Une autre action est **Add [Service Name]** (Ajouter [Nom du service]). Ces deux options ne sont pas prises en charge.
     * **Altération du package Python**. Les clusters HDInsight sont basés sur les environnements Python intégrés, Python 2.7 et Python 3.5. L’installation directe de packages personnalisés dans ces environnements intégrés par défaut peut entraîner des modifications inattendues de la version de la bibliothèque et arrêter le cluster. Découvrez comment [effectuer une installation sécurisée des packages Python externes personnalisés](./spark/apache-spark-python-package-installation.md#safely-install-external-python-packages) pour vos applications Spark.
     * **Logiciels tiers**. Les clients ont la possibilité d’installer des logiciels tiers sur leurs clusters HDInsight. Toutefois, nous recommandons de recréer le cluster en cas d’arrêt des fonctionnalités existantes.
     * **Plusieurs charges de travail sur le même cluster**. Dans HDInsight 4.0, Hive Warehouse Connector nécessite des clusters distincts pour les charges de travail Spark et Interactive Query. [Suivez les étapes ci-dessous pour configurer les deux clusters dans Azure HDInsight](interactive-query/apache-hive-warehouse-connector.md). De même, l’intégration de [Spark avec HBASE](hdinsight-using-spark-query-hbase.md) nécessite deux clusters différents.
     * **Changement du mot de passe de la base de données Ambari personnalisée**. Le mot de passe de la base de données Ambari est défini au moment de la création du cluster et il n’existe pas de mécanisme pour le changer. Quand un client déploie le cluster avec une [base de données Ambari personnalisée](hdinsight-custom-ambari-db.md), il peut changer le mot de passe de cette base de données sur SQL DB ; en revanche, il n’a aucune possibilité de changer ce mot de passe pour un cluster HDInsight déjà en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Se connecter à HDInsight à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gérer un cluster Linux avec Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Notes de publication HDInsight](./hdinsight-version-release.md)
