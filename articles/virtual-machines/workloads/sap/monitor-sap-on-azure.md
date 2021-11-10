---
title: Surveiller SAP sur Azure (préversion) | Microsoft Docs
description: Commencez ici pour découvrir comment surveiller SAP sur Azure.
author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.custom: subject-monitoring
ms.date: 10/13/2021
ms.author: mamccrea
ms.openlocfilehash: cf68247c9d259aff6ffe6d2e7cd9be76eea6f237
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063267"
---
# <a name="monitor-sap-on-azure-preview"></a>Surveiller SAP sur Azure (préversion)

Quand vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous souhaitez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article explique comment surveiller l’exécution de SAP sur Azure à l’aide d’Azure Monitor pour SAP Solutions. Azure Monitor pour SAP Solutions utilise des parties spécifiques de l’infrastructure d’[Azure Monitor](../../../azure-monitor/overview.md).

## <a name="overview"></a>Vue d’ensemble

Azure Monitor pour SAP Solutions est un produit de surveillance Azure natif pour toute personne exécutant ses paysages SAP sur Azure. Il fonctionne à la fois avec [SAP sur Azure Machines Virtuelles](./hana-get-started.md) et [SAP sur Azure Grandes instances](./hana-overview-architecture.md).

Azure Monitor pour SAP Solutions vous permet de collecter des données de télémétrie à partir de l’infrastructure Azure et des bases de données dans un emplacement central et d’établir une corrélation visuelle entre les données afin d’accélérer la résolution des problèmes.

Vous pouvez surveiller différents composants d’un paysage SAP, tels que les machines virtuelles Azure, le cluster à haute disponibilité, la base de données SAP HANA, SAP NetWeaver et ainsi de suite, en ajoutant le **fournisseur** de ce composant. Pour plus d’informations, consultez [Déploiement d’Azure Monitor pour SAP Solutions à l’aide du portail Azure](azure-monitor-sap-quickstart.md).

Infrastructure prise en charge :

- Machine virtuelle Azure
- Instance volumineuse Azure

Bases de données prises en charge :
- Base de données SAP HANA
- Microsoft SQL Server

Azure Monitor pour SAP Solutions utilise les fonctionnalités [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) et [Classeurs](../../../azure-monitor/visualize/workbooks-overview.md) d’[Azure Monitor](../../../azure-monitor/overview.md). Cette application vous permet d’effectuer les opérations suivantes :

- Créez des [visualisations personnalisées](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) en modifiant les classeurs par défaut fournis par Azure Monitor pour SAP Solutions. 
- Écrivez des [requêtes personnalisées](../../../azure-monitor/logs/log-analytics-tutorial.md).
- Créez des [alertes personnalisées](../../../azure-monitor/alerts/alerts-log.md) à l’aide de l’espace de travail Azure Log Analytics. 
- Tirez parti de la [période de rétention flexible](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) dans Journaux Azure Monitor/Log Analytics. 
- Connectez les données de surveillance à votre système de tickets.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Quelles sont les données collectées par Azure Monitor pour SAP Solutions ?

Azure Monitor pour SAP Solutions ne collecte pas de métriques ou de données de journal des ressources Azure Monitor comme de nombreuses autres ressources Azure. Au lieu de cela, il envoie directement des journaux personnalisés au système de journaux Azure Monitor, où vous pouvez ensuite utiliser les fonctionnalités intégrées de Log Analytics.

La collecte de données dans Azure Monitor pour SAP Solutions varie en fonction des fournisseurs que vous configurez. Les données suivantes sont collectées au cours de la préversion publique.

Télémétrie de cluster Pacemaker haute disponibilité :
- État du nœud, de la ressource et de l’appareil de traitement par blocs STONITH
- Contraintes d’emplacement de Pacemaker
- État des voix et des anneaux du quorum
- [Autres](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Télémétrie SAP HANA :
- Utilisation du processeur, de la mémoire, du disque et du réseau
- Réplication du système HANA (HSR)
- Sauvegarde HANA
- État de l’hôte HANA
- Rôles de serveur d’index et de serveur de noms
- Croissance de base de données
- Tables principales
- Utilisation du système de fichiers

Télémétrie du serveur Microsoft SQL :
- Processeur, mémoire, utilisation du disque
- Nom d’hôte, nom de l’instance SQL, ID du système SAP
- Requêtes de lots, compilations et espérance de vie d’une page dans le temps
- Les 10 instructions SQL les plus onéreuses dans le temps
- Les 12 plus grandes tables dans le système SAP
- Problèmes enregistrés dans le journal des erreurs SQL Server
- Processus de blocage et statistiques d’attente SQL dans le temps

Télémétrie du système d’exploitation (Linux) : 
- Utilisation du processeur, nombre de fourches, processus en cours d’exécution et bloqués 
- Utilisation de la mémoire et distribution entre utilisée, mise en cache et mise en mémoire tampon 
- Permuter l’utilisation, la pagination et le taux d’échange 
- Utilisation des systèmes de fichiers, nombre d’octets lus et écrits par périphérique de traitement par blocs 
- Latence de lecture/écriture par périphérique de traitement par blocs 
- Nombre d’E/S en cours, octets de lecture/écriture PMEM 
- Paquets réseau en entrée/sortie, octets réseau en entrée/sortie 

Télémétrie SAP NetWeaver :

- Disponibilité du serveur d’applications et du système SAP, notamment la disponibilité du processus d’instance pour Dispatcher, ICM, Gateway, Message Server, Enqueue Server, IGS Watchdog
- Tendances et statistiques d’utilisation des processus de travail
- Tendances et statistiques des verrous de mise en file d’attente
- Tendances et statistiques d’utilisation des files d’attente

## <a name="data-sharing-with-microsoft"></a>Partage de données avec Microsoft

Azure Monitor pour solutions SAP collecte les métadonnées système pour offrir une meilleure prise en charge de SAP. Aucune information d’identification personnelle (PII)/information d’identification de l’utilisateur final (EUII) n’est collectée.

Vous pouvez activer le partage de données avec Microsoft lorsque vous créez la ressource Azure Monitor pour solutions SAP en choisissant *Partager* dans la liste déroulante. Nous vous recommandons d’activer le partage de données. Le partage de données offre aux équipes de support et d’ingénierie de Microsoft des informations sur votre environnement, ce qui nous permet d’offrir une meilleure prise en charge pour votre solution SAP sur Azure stratégique.

## <a name="architecture-overview"></a>Vue d’ensemble de l’architecture

Le diagramme suivant montre, à un niveau élevé, comment Azure Monitor pour SAP Solutions collecte les données de télémétrie à partir de la base de données SAP HANA. L’architecture est identique, que SAP HANA soit déployée sur des machines virtuelles Azure ou sur des instances volumineuses Azure.

![Architecture d’Azure Monitor pour solutions SAP](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Les composants clés de l’architecture sont les suivants :
- **Portail Azure** : votre point de départ. Vous pouvez accéder à la place de marché au sein du portail Azure et découvrir Azure Monitor pour SAP Solutions.
- **Ressource Azure Monitor pour SAP Solutions** : un emplacement d’arrivée vous permettant d’examiner la télémétrie de surveillance.
- **Groupe de ressources managées** : déployé automatiquement dans le cadre du déploiement de ressource Azure Monitor pour SAP Solutions. Les ressources déployées dans le groupe de ressources managées aident à collecter des données de télémétrie. Les ressources clés déployées et leurs objectifs sont les suivantes :
   - **Machine virtuelle Azure** : également appelée *machine virtuelle de collecteur*, il s’agit d’une machine virtuelle Standard_B2ms. L’objectif principal de cette machine virtuelle est d’héberger la *charge utile de surveillance*. La charge utile de surveillance fait référence à la logique de la télémétrie de collecte des données à partir des systèmes sources et du transfert des données vers l’infrastructure de surveillance. Dans le diagramme précédent, la charge utile de supervision contient la logique permettant la connexion à la base de données SAP HANA via le port SQL.
   - **[Azure Key Vault](../../../key-vault/general/basic-concepts.md)** : cette ressource est déployée pour conserver en toute sécurité les informations d’identification de la base de données SAP HANA et pour stocker des informations sur les [fournisseurs](./azure-monitor-providers.md).
   - **Espace de travail Log Analytics** : destination où sont stockées les données de télémétrie.
      - La visualisation est basée sur la télémétrie dans Log Analytics à l’aide de [workbooks Azure](../../../azure-monitor/visualize/workbooks-overview.md). Vous pouvez personnaliser la visualisation. Vous pouvez également épingler vos classeurs ou une visualisation spécifique au sein des classeurs au tableau de bord Azure afin de permettre une actualisation automatique. La fréquence maximale d’actualisation est de 30 minutes.
      - Vous pouvez utiliser votre espace de travail existant dans le même abonnement que la ressource de surveillance SAP en choisissant cette option au moment du déploiement.
      - Vous pouvez utiliser le langage de requête Kusto (KQL) pour exécuter des [requêtes](../../../azure-monitor/logs/log-query-overview.md) par rapport aux tables brutes dans l’espace de travail Log Analytics. Examinons les *journaux d’activité personnalisés*.

> [!Note]
> Vous êtes responsable de la mise à jour corrective et de la maintenance de la machine virtuelle déployée dans le groupe de ressources managées.

> [!Tip]
> Vous pouvez utiliser un espace de travail Log Analytics existant pour la collecte de données de télémétrie s’il est déployé dans le même abonnement Azure que la ressource pour Azure Monitor pour SAP Solutions.

### <a name="architecture-highlights"></a>Points forts de l’architecture

Voici les principales caractéristiques de l’architecture :
 - **Multi-instance** : vous pouvez surveiller plusieurs instances d’un type de composant donné (par exemple, base de données HANA, cluster à haute disponibilité (HA), Microsoft SQL Server, SAP NetWeaver) sur plusieurs SID SAP au sein d’un réseau virtuel avec une seule ressource d’Azure Monitor pour SAP Solutions.
 - **Multi-fournisseur** : le diagramme d’architecture précédent montre le fournisseur SAP HANA à titre d’exemple. De la même manière, vous pouvez configurer plusieurs fournisseurs pour des composants correspondants afin de collecter des données de ces composants. Par exemple, base de données HANA, cluster HA, Microsoft SQL Server et SAP NetWeaver. 
 - **Open source** : le code source d’Azure Monitor pour solutions SAP est disponible dans [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Vous pouvez vous référer au code du fournisseur et en apprendre plus sur le produit, contribuer ou partager des commentaires.
 - **Requêtes de framework extensibles** : les requêtes SQL pour la collecte des données de télémétrie sont écrites en [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Des requêtes SQL supplémentaires pour collecter plus de données de télémétrie peuvent facilement être ajoutées. Vous pouvez demander des données de télémétrie spécifiques à ajouter à Azure Monitor pour SAP Solutions. Pour ce faire, laissez un commentaire à l’aide du lien à la fin de cet article. Vous pouvez également laisser un commentaire en contactant votre équipe de compte.

## <a name="analyzing-metrics"></a>Analyse des métriques

Azure Monitor pour SAP Solutions ne prend pas en charge les métriques. 

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Azure Monitor pour SAP Solutions ne prend pas en charge les journaux de ressources ni les journaux d’activité. Pour obtenir la liste des tables utilisées par les Journaux Azure Monitor pouvant être interrogés par Log Analytics, consultez les [informations de référence sur les données Surveiller SAP sur Azure](monitor-sap-on-azure-reference.md#azure-monitor-logs-tables). 

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Lorsque vous sélectionnez **Journaux** dans le menu Azure Monitor pour SAP Solutions, Log Analytics s’ouvre avec l’étendue de requête définie sur Azure Monitor pour SAP Solutions actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres comptes ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](../../../azure-monitor/logs/scope.md).

Vous pouvez utiliser des requêtes Kusto pour vous aider à surveiller vos ressources Surveiller SAP sur Azure. Voici un exemple de requête qui vous fournit des données à partir d’un journal personnalisé pour un intervalle de temps spécifié. Vous spécifiez l’intervalle de temps et le nombre de lignes. Dans cet exemple, vous obtenez cinq lignes de données pour l’intervalle de temps sélectionné. 

```Kusto
custom log name 
| take 5

```

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent.

Vous pouvez configurer des alertes dans Azure Monitor pour SAP Solutions à partir du portail Azure. Pour plus d’informations, consultez [Configurer des alertes dans Azure Monitor pour SAP Solutions à l’aide du portail Azure](azure-monitor-alerts-portal.md).

## <a name="create-azure-monitor-for-sap-solutions-resources"></a>Créer des ressources Azure Monitor pour SAP Solutions

Vous disposez de plusieurs options pour déployer Azure Monitor pour SAP Solutions et configurer des fournisseurs : 
- Vous pouvez déployer Azure Monitor pour SAP Solutions directement à partir du portail Azure. Pour plus d’informations, consultez [Déploiement d’Azure Monitor pour SAP Solutions à l’aide du portail Azure](azure-monitor-sap-quickstart.md).
- Utilisation d’Azure PowerShell Pour plus d’informations, consultez [Déployer Azure Monitor pour SAP Solutions avec Azure PowerShell](azure-monitor-sap-quickstart-powershell.md).
- Utilisez l’extension CLI. Pour plus d’informations, consultez [Module de commande SAP HANA](https://github.com/Azure/azure-hanaonazure-cli-extension#sapmonitor).

## <a name="pricing"></a>Tarifs
Azure Monitor pour solutions SAP est un produit gratuit (pas de frais de licence). Le paiement des composants sous-jacents dans le groupe de ressources managées vous incombe. Les coûts de consommation associés à l’utilisation et à la rétention des données vous incombent également. Pour plus d’informations, consultez les documents de tarification Azure standard :
- [Tarification des machines virtuelles Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)
- [Tarification des comptes de stockage Azure](https://azure.microsoft.com/pricing/details/storage/queues/)
- [Tarification d’Azure Log Analytics et des alertes](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir la liste des journaux personnalisés relatifs à Azure Monitor pour SAP Solutions et des informations sur les types de données associés, consultez les [informations de référence sur les données Surveiller SAP sur Azure](monitor-sap-on-azure-reference.md).
- Pour plus d’informations sur les fournisseurs disponibles pour Azure Monitor pour SAP Solutions, consultez [Fournisseurs Azure Monitor pour SAP Solutions](azure-monitor-providers.md).
