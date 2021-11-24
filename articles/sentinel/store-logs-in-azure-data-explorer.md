---
title: Intégrer Azure Data Explorer pour la conservation des journaux à long terme | Microsoft Docs
description: Envoyez les journaux Microsoft Sentinel à Azure Data Explorer pour une conservation à long terme afin de réduire les coûts de stockage des données.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fa0559d2ce0796cd8031b06b8ff4034d943675
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523901"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Intégrer Azure Data Explorer pour la conservation des journaux à long terme

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Par défaut, les journaux ingérés dans Microsoft Sentinel sont stockés dans Azure Monitor Log Analytics. Cet article explique comment réduire les coûts de conservation des données dans Microsoft Sentinel en les envoyant à Azure Data Explorer pour une conservation à long terme.

Le stockage des journaux dans Azure Data Explorer réduit les coûts tout en conservant la possibilité d’interroger vos données et s’avère particulièrement utile lorsque la quantité des données augmente. Par exemple, alors que les données de sécurité peuvent perdre de leur valeur au fil du temps, vous pouvez être tenu de conserver les journaux pour répondre à des exigences réglementaires ou pour mener des enquêtes périodiques sur des données plus anciennes.

## <a name="about-azure-data-explorer"></a>À propos d’Azure Data Explorer

Azure Data Explorer est une plateforme d’analyse du Big Data qui est hautement optimisée pour l’analyse des journaux et des données. Comme Azure Data Explorer utilise le langage de requête Kusto Query Language (KQL), il s’agit d’une bonne alternative au stockage de données Microsoft Sentinel. L’utilisation d’Azure Data Explorer pour votre stockage de données vous permet d’exécuter des requêtes multiplateformes et de visualiser des données à la fois sur Azure Data Explorer et sur Microsoft Sentinel.

Pour plus d’informations, consultez la [documentation](/azure/data-explorer/) et le [blog](https://azure.microsoft.com/blog/tag/azure-data-explorer/) d’Azure Data Explorer.

### <a name="when-to-integrate-with-azure-data-explorer"></a>Quand intégrer à Azure Data Explorer

Microsoft Sentinel offre des capacités SIEM et SOAR complètes, un déploiement et une configuration rapides, ainsi que des fonctionnalités de sécurité intégrées et avancées pour les équipes SOC. Toutefois, la valeur du stockage des données de sécurité dans Microsoft Sentinel peut diminuer après quelques mois, une fois que les utilisateurs SOC n’ont plus besoin d’y accéder aussi souvent qu’ils accèdent à des données plus récentes.

Si vous n’avez besoin d’accéder à des tables spécifiques qu’occasionnellement, par exemple pour des enquêtes ou des audits périodiques, vous pouvez considérer que la conservation de vos données dans Microsoft Sentinel n’est plus rentable. À ce stade, nous vous recommandons de stocker les données dans Azure Data Explorer, qui coûte moins cher, mais vous permet toujours d’explorer à l’aide des mêmes requêtes KQL que vous exécutez dans Microsoft Sentinel.

Vous pouvez accéder aux données dans Azure Data Explorer directement à partir de Microsoft Sentinel grâce à la [fonctionnalité de proxy Azure Data Explorer de Log Analytics](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md). Pour ce faire, utilisez des requêtes de cluster croisé dans votre recherche dans les journaux ou les classeurs.

> [!IMPORTANT]
> Les principales capacités SIEM, notamment les règles d’analytique, UEBA et le graphique d’investigation, ne prennent pas en charge les données stockées dans Azure Data Explorer.
>

> [!NOTE]
> L’intégration à Azure Data Explorer peut également vous permettre de disposer d’un contrôle et d’une granularité dans vos données. Pour plus d’informations, consultez [Considérations relatives à la conception](#design-considerations).
>
## <a name="send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel"></a>Envoyer des données directement à Microsoft Sentinel et Azure Data Explorer en parallèle

Il se peut que vous souhaitiez conserver toutes les données *ayant une valeur de sécurité* dans Microsoft Sentinel pour les utiliser dans les détections, les enquêtes sur les incidents, la chasse aux menaces, UEBA, etc. La conservation de ces données dans Microsoft Sentinel profite principalement aux utilisateurs du centre des opérations de sécurité (SOC), pour lesquels 3 à 12 mois de stockage suffisent généralement.

Vous pouvez également configurer toutes vos données, *quelle que soit leur valeur de sécurité*, pour qu’elles soient envoyées en même temps à Azure Data Explorer, où vous pouvez les stocker plus longtemps. Bien que l’envoi simultané des données à Microsoft Sentinel et à Azure Data Explorer entraîne une certaine duplication, les économies réalisées peuvent être importantes, car vous réduisez les coûts de rétention dans Microsoft Sentinel.

> [!TIP]
> Cette option vous permet également de mettre en corrélation des données réparties dans plusieurs magasins de données, par exemple pour enrichir les données de sécurité stockées dans Microsoft Sentinel avec des données opérationnelles ou à long terme stockées dans Azure Data Explorer. Pour plus d’informations, consultez [Requête inter-ressources Azure Data Explorer à l’aide d’Azure Monitor](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md).
>

L’illustration suivante montre comment vous pouvez conserver toutes vos données dans Azure Data Explorer, tout en envoyant uniquement vos données de sécurité à Microsoft Sentinel pour une utilisation quotidienne.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Stockez les données dans Azure Data Explorer et Microsoft Sentinel en parallèle.":::

Pour plus d’informations sur l’implémentation de cette option d’architecture, consultez [Surveillance d’Azure Data Explorer](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-azure-data-explorer"></a>Exporter des données de Log Analytics vers Azure Data Explorer

Au lieu d’envoyer vos données directement à Azure Data Explorer, vous pouvez choisir d’exporter vos données de Log Analytics vers Azure Data Explorer via Azure Event Hubs ou Azure Data Factory.

### <a name="data-export-architecture"></a>Architecture d’exportation de données

L’illustration suivante montre un exemple de flux de données exportées via le pipeline d’ingestion d’Azure Monitor. Vos données sont dirigées vers Log Analytics par défaut, mais vous pouvez également les configurer pour qu’elles soient exportées vers un compte de stockage Azure ou un Event Hub.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Exporter des données à partir d’Azure Monitor – architecture.":::

Lors de la configuration des règles d’exportation de données, sélectionnez les types de journaux que vous souhaitez exporter. Une fois ces règles configurées, les nouvelles données arrivant au point de terminaison d’ingestion de Log Analytics, et ciblées sur votre espace de travail pour les tables sélectionnées, sont exportées vers votre compte de stockage ou votre Event Hub.

Lorsque vous configurez des données pour l’exportation, tenez compte des points suivants :

|Considération  | Détails |
|---------|---------|
|**Étendue des données exportées**     |  Une fois l’exportation configurée pour une table spécifique, toutes les données envoyées à cette table sont exportées, sans exception. L’exportation d’un sous-ensemble filtré de vos données ou la limitation de l’exportation à des événements spécifiques n’est pas prise en charge.       |
|**Exigences relatives à l’emplacement**     |   L’espace de travail Azure Monitor/Microsoft Sentinel et l’emplacement de destination (un compte de stockage Azure ou un Event Hub) doivent se trouver dans la même région géographique.      |
|**Tables prises en charge**     | Toutes les tables ne sont pas prises en charge pour l’exportation, notamment les tables de journaux personnalisés, qui ne sont pas prises en charge. <br><br>Pour plus d’informations, consultez [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/logs/logs-data-export.md) et la [liste des tables prises en charge](../azure-monitor/logs/logs-data-export.md#supported-tables).         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Méthodes et procédures d’exportation de données

Utilisez l’une des procédures suivantes pour exporter des données de Microsoft Sentinel vers Azure Data Explorer :

- **Via un Event Hub Azure**. Exportez les données de Log Analytics vers un Event Hub, où vous pouvez les ingérer dans Azure Data Explorer. Cette méthode permet de stocker certaines données (les X premiers mois) à la fois dans Microsoft Sentinel et Azure Data Explorer.

- **Via Stockage Azure et Azure Data Factory**. Exportez vos données de Log Analytics vers Stockage Blob Azure, puis Azure Data Factory est utilisé pour exécuter une tâche de copie périodique afin d’exporter les données vers Azure Data Explorer. Cette méthode vous permet de copier les données d’Azure Data Factory uniquement lorsqu’elles approchent de leur limite de rétention dans Microsoft Sentinel/Log Analytics, évitant ainsi la duplication.

### <a name="azure-event-hub"></a>[Azure Event Hub](#tab/adx-event-hub)

Cette section décrit comment exporter les données Microsoft Sentinel de Log Analytics vers un Event Hub, où vous pouvez les ingérer dans Azure Data Explorer. Comme pour l’[envoi de données directement à Microsoft Sentinel et Azure Data Explorer en parallèle](#send-data-directly-to-microsoft-sentinel-and-azure-data-explorer-in-parallel), cette méthode comprend une certaine duplication des données, car les données sont transmises en continu dans Azure Data Explorer au fur et à mesure qu’elles arrivent dans Log Analytics.

L’illustration suivante montre un exemple de flux de données exportées vers un Event Hub, d’où elles sont ingérées dans Azure Data Explorer.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Exporter des données dans Azure Data Explorer via un Event Hub Azure.":::

L’architecture présentée dans l’image précédente offre l’expérience SIEM complète de Microsoft Sentinel, y compris la gestion des incidents, les investigations visuelles, la chasse aux menaces, les visualisations avancées, UEBA, et plus encore, pour les données qui doivent être consultées fréquemment, tous les *X* mois. Parallèlement, cette architecture vous permet également d’interroger les données à long terme en y accédant directement dans Azure Data Explorer, ou via Microsoft Sentinel grâce à la fonctionnalité de proxy d’Azure Data Explorer. Les requêtes sur le stockage de données à long terme dans Azure Data Explorer peuvent être portées sans aucune modification de Microsoft Sentinel à Azure Data Explorer.

> [!NOTE]
> Lorsque vous exportez plusieurs tables de données dans Azure Data Explorer via un Event Hub, gardez à l’esprit que l’exportation de données Log Analytics est limitée au nombre maximum d’Event Hubs par espace de noms. Pour plus d’informations sur l’exportation des données, consultez [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/logs/logs-data-export.md?tabs=portal).
>
> Pour la plupart des clients, nous recommandons d’utiliser le niveau standard d’Event Hubs. Selon la quantité de tables que vous devez exporter et le volume de trafic vers ces tables, vous pouvez avoir besoin d’utiliser le niveau Dedicated d’Event Hubs. Pour plus d’informations, consultez la [documentation concernant Event Hubs](../event-hubs/event-hubs-quotas.md).
>

> [!TIP]
> Pour plus d’informations sur cette procédure, consultez [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](/azure/data-explorer/ingest-data-no-code).
>

**Pour exporter des données dans Azure Data Explorer via un Event Hub** :

1. **Configurez l’exportation des données Log Analytics vers un Event Hub**. Pour plus d’informations, consultez [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/logs/logs-data-export.md).

1. **Créez un cluster et une base de données Azure Data Explorer**. Pour plus d'informations, consultez les pages suivantes :

    - [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    - [Sélectionner la bonne référence SKU de calcul pour votre cluster Azure Data Explorer](/azure/data-explorer/manage-cluster-choose-sku)

1. **Créez des tables cibles**. Les données brutes sont d’abord ingérées dans une table intermédiaire, où les données brutes sont stockées, manipulées et développées.

    Une stratégie de mise à jour, qui est similaire à une fonction appliquée à toutes les nouvelles données, est utilisée pour ingérer les données développées dans la table finale, qui a le même schéma que la table d’origine dans Microsoft Sentinel.

    Définissez la rétention de la table brute sur **0** jour. Les données sont stockées uniquement dans la table correctement formatée et supprimées dans la table brute dès qu’elles sont transformées.

    Pour plus d’informations, consultez [Ingérer et interroger des données de supervision dans Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Créez le mappage des tables**. Mappez les tables JSON pour définir la façon dont les enregistrements atterrissent dans la table d’événements bruts au fur et à mesure qu’ils arrivent d’un Event Hub. Pour plus d’informations, consultez [Créer la stratégie de mise à jour pour les données des métriques et de journaux](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Créez une stratégie de mise à jour et attachez-la à la table des enregistrements bruts**. Dans cette étape, créez une fonction, appelée stratégie de mise à jour, et attachez-la à la table de destination afin que les données soient transformées au moment de l’ingestion.

    > [!NOTE]
    > Cette étape est requise uniquement lorsque vous souhaitez que les tables de données dans Azure Data Explorer aient le même schéma et le même format que dans Microsoft Sentinel.
    >

    Pour plus d’informations, consultez [Connecter un Event Hub à Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Créez une connexion de données entre l’Event Hub et la table de données brutes dans Azure Data Explorer**. Configurez Azure Data Explorer avec des détails sur la façon d’exporter les données dans l’Event Hub.

    Utilisez les instructions de la [documentation d’Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) et spécifiez les informations suivantes :

    - **Cible**. Spécifiez la table spécifique avec les données brutes.
    - **Format**. Spécifiez `.json` comme format de la table.
    - **Mappage à appliquer**. Spécifiez la table de mappage créée à l’[étape 4](#mapping) ci-dessus.

1. **Modifiez la rétention de la table cible**. La [stratégie de rétention par défaut d’Azure Data Explorer](/azure/data-explorer/kusto/management/retentionpolicy) peut être beaucoup plus longue que ce dont vous avez besoin.

    Utilisez la commande suivante pour mettre à jour la stratégie de rétention à un an :

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```

### <a name="azure-storage--azure-data-factory"></a>[Stockage Azure / Azure Data Factory](#tab/azure-storage-azure-data-factory)

Cette section décrit comment exporter les données Microsoft Sentinel de Log Analytics vers Stockage Azure, où Azure Data Factory peut exécuter un travail normal pour exporter les données dans Azure Data Explorer.

L’utilisation de Stockage Azure et d’Azure Data Factory vous permet de copier les données à partir de Stockage Azure uniquement lorsqu’elles sont proches de la limite de rétention dans Microsoft Sentinel ou Log Analytics. Il n’existe aucune duplication de données, et Azure Data Explorer est utilisé *uniquement* pour accéder aux données plus anciennes que la limite de rétention dans Microsoft Sentinel.

> [!TIP]
> Bien que l’architecture permettant d’utiliser Stockage Azure et Azure Data Factory pour vos données héritées soit plus complexe, cette méthode permet de réaliser des économies plus importantes dans l’ensemble.
>
L’illustration suivante montre un exemple de flux de données exportées vers un stockage Azure, à partir duquel Azure Data Factory exécute un travail normal pour les exporter ensuite vers Azure Data Explorer.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Exporter des données dans Azure Data Explorer via Stockage Azure et Azure Data Factory.":::

**Pour exporter des données dans Azure Data Explorer via un stockage Azure et Azure Data Factory** :

1. **Configurez l’exportation des données Log Analytics vers un Event Hub**. Pour plus d’informations, consultez [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor](../azure-monitor/logs/logs-data-export.md?tabs=portal#enable-data-export).

1. **Créez un cluster et une base de données Azure Data Explorer**. Pour plus d'informations, consultez les pages suivantes :

    - [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    - [Sélectionner la bonne référence SKU de calcul pour votre cluster Azure Data Explorer](/azure/data-explorer/manage-cluster-choose-sku)

1. **Créez des tables cibles**. Les données brutes sont d’abord ingérées dans une table intermédiaire, où les données brutes sont stockées, manipulées et développées.

    Une stratégie de mise à jour, qui est similaire à une fonction appliquée à toutes les nouvelles données, est utilisée pour ingérer les données développées dans la table finale, qui a le même schéma que la table d’origine dans Microsoft Sentinel.

    Définissez la rétention de la table brute sur **0** jour. Les données sont stockées uniquement dans la table correctement formatée et supprimées dans la table brute dès qu’elles sont transformées.

    Pour plus d’informations, consultez [Ingérer et interroger des données de supervision dans Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Créez le mappage des tables**. Mappez les tables JSON pour définir la façon dont les enregistrements atterrissent dans la table d’événements bruts au fur et à mesure qu’ils arrivent d’un Event Hub. Pour plus d’informations, consultez [Créer la stratégie de mise à jour pour les données des métriques et de journaux](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Créez une stratégie de mise à jour et attachez-la à la table des enregistrements bruts**. Dans cette étape, créez une fonction, appelée stratégie de mise à jour, et attachez-la à la table de destination afin que les données soient transformées au moment de l’ingestion.

    > [!NOTE]
    > Cette étape est requise uniquement lorsque vous souhaitez que les tables de données dans Azure Data Explorer aient le même schéma et le même format que dans Microsoft Sentinel.
    >

    Pour plus d’informations, consultez [Connecter un Event Hub à Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **Créez une connexion de données entre l’Event Hub et la table de données brutes dans Azure Data Explorer**. Configurez Azure Data Explorer avec des détails sur la façon d’exporter les données dans l’Event Hub.

    Utilisez les instructions de la [documentation d’Azure Data Explorer](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) et spécifiez les informations suivantes :

    - **Cible**. Spécifiez la table spécifique avec les données brutes.
    - **Format**. Spécifiez `.json` comme format de la table.
    - **Mappage à appliquer**. Spécifiez la table de mappage créée à l’[étape 4](#mapping) ci-dessus.

1. **Configurez le pipeline Azure Data Factory** :

    - Créez des services liés pour Stockage Azure et Azure Data Explorer. Pour plus d'informations, consultez les pages suivantes :

        - [Copier et transformer des données dans un stockage Azure Blob à l’aide d’Azure Data Factory](../data-factory/connector-azure-blob-storage.md)
        - [Copier des données depuis/vers Azure Data Explorer à l’aide d’Azure Data Factory](../data-factory/connector-azure-data-explorer.md)

    - Créez un jeu de données à partir de Stockage Azure. Pour plus d’informations, consultez [Jeux de données dans Azure Data Factory](../data-factory/concepts-datasets-linked-services.md).

    - Créez un pipeline de données avec une opération de copie, basée sur les propriétés **LastModifiedDate**.

        Pour plus d’informations, consultez [Copier les fichiers nouveaux et modifiés par **LastModifiedDate** avec Azure Data Factory](../data-factory/solution-template-copy-new-files-lastmodifieddate.md).

---

## <a name="design-considerations"></a>Remarques relatives à la conception

Lorsque vous stockez vos données Microsoft Sentinel dans Azure Data Explorer, tenez compte des éléments suivants :

|Considération  |Description  |
|---------|---------|
|**Taille de cluster et référence SKU**     | Planifiez soigneusement le nombre de nœuds et la référence SKU de machine virtuelle dans votre cluster. Ces facteurs déterminent la puissance de traitement et la taille de votre cache à chaud (SSD et mémoire). Plus le cache est volumineux, plus vous pourrez interroger de données avec des performances élevées. <br><br>Nous vous encourageons à consulter le [calculateur de dimensionnement d’Azure Data Explorer](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html), où vous pouvez jouer avec différentes configurations et voir le coût qui en résulte. <br><br>Azure Data Explorer possède également une capacité de mise à l’échelle automatique qui prend des décisions intelligentes pour ajouter/supprimer des nœuds selon les besoins, en fonction de la charge du cluster. Pour plus d’informations, consultez [Gérer la mise à l’échelle horizontale d’un cluster (scale-out) dans Azure Data Explorer pour prendre en compte les fluctuations de la demande](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Cache chaud/froid**     | Azure Data Explorer permet de contrôler les tables de données qui se trouvent dans le cache à chaud et de renvoyer les résultats plus rapidement. Si votre cluster Azure Data Explorer contient de grandes quantités de données, vous pouvez décomposer les tables par mois, afin de bénéficier d’une plus grande granularité sur les données présentes dans votre cache chaud. <br><br>Pour plus d’informations, consultez [Stratégie de cache (cache chaud et froid)](/azure/data-explorer/kusto/management/cachepolicy).       |
|**Rétention**     |   Dans Azure Data Explorer, vous pouvez configurer le moment où les données sont supprimées d’une base de données ou d’une table individuelle, ce qui constitue également un élément important pour limiter les coûts de stockage. <br><br> Pour plus d’informations, consultez [Stratégie de conservation](/azure/data-explorer/kusto/management/retentionpolicy).       |
|**Sécurité**     |  Plusieurs paramètres d’Azure Data Explorer peuvent vous aider à protéger vos données, comme la gestion des identités, le chiffrement, etc. Spécifiquement pour le contrôle d’accès en fonction du rôle (RBAC), Azure Data Explorer peut être configuré pour restreindre l’accès aux bases de données, aux tables ou même aux lignes d’une table. Pour plus d’informations, consultez [Sécurité dans Azure Data Explorer](/azure/data-explorer/security) et [Sécurité au niveau des lignes](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy).|
|**Partage des données**     |   Azure Data Explorer vous permet de mettre des éléments de données à la disposition d’autres parties, comme des partenaires ou des fournisseurs, et même d’acheter des données à d’autres parties. Pour plus d’informations, consultez [Utiliser Azure Data Share pour partager des données avec Azure Data Explorer](/azure/data-explorer/data-share).      |
| **Autres composants de coût** | Prenez en compte les autres composants de coût pour les méthodes suivantes : <br><br>**Exportation de données via un Event Hub Azure** : <br>– Coûts d’exportation des données Log Analytics, facturés par Go exporté. <br>– Coûts de l’Event Hub, facturés par unité de débit.  <br><br>**Exportation de données via Stockage Azure et Azure Data Factory** : <br>– Exportation des données Log Analytics, facturée par Go exporté. <br>– Stockage Azure, facturé par Go stocké. <br>– Azure Data Factory, facturé par copie des activités exécutées.
|     |         |

## <a name="next-steps"></a>Étapes suivantes

Quel que soit l’emplacement de stockage de vos données, poursuivez la chasse et les investigations à l’aide de Microsoft Sentinel.

Pour plus d'informations, consultez les pages suivantes :

- [Didacticiel : Examiner les incidents avec Microsoft Sentinel](investigate-cases.md)
- [Recherche de menaces avec Microsoft Sentinel](hunting.md)
