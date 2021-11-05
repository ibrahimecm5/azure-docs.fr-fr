---
title: Superviser une activité de copie
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment surveiller l’activité de copie dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 14607231fd68cf78f33a21abdb26dd68e2ef6871
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131062185"
---
# <a name="monitor-copy-activity"></a>Superviser une activité de copie

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit comment surveiller l’activité de copie dans Azure Data Factory et les pipelines Synapse. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).  Vous pouvez également surveiller les activités de copie générées avec l’[outil Copier des données](copy-data-tool.md), ainsi que les [activités de suppression](delete-activity.md) à l’aide de la même approche.

## <a name="monitor-visually"></a>Surveiller visuellement

Une fois que vous avez créé et publié un pipeline, vous pouvez l’associer à un déclencheur ou lancer manuellement une exécution ad hoc. Vous pouvez superviser l’ensemble de vos exécutions de pipeline en mode natif dans l’expérience utilisateur. Pour plus d'informations sur la surveillance en général, consultez [Surveiller visuellement Azure Data Factory et les pipelines Synapse](monitor-visually.md).

Pour surveiller l’exécution de l’activité de copie, accédez à l’interface utilisateur **Data Factory Studio** ou **Azure Synapse Studio** pour votre instance de service. Dans la liste des exécutions de pipeline qui s'affiche sous l'onglet **Surveiller**, cliquez sur le lien correspondant au **nom du pipeline** pour accéder à la liste des exécutions d’activités dans l’exécution de pipeline.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run.png" alt-text="Surveillance de l’exécution du pipeline":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run-synapse.png" alt-text="Surveillance de l’exécution du pipeline":::

---

À ce niveau, vous pouvez voir des liens pour copier l’entrée, la sortie d'activité et des erreurs (en cas d’échec d’exécution de l’activité de copie), ainsi que des statistiques telles que durée/état. Cliquez sur le bouton **Détails** (lunettes) en regard du nom de l’activité de copie pour consulter de plus amples informations sur l'exécution de l'activité de copie. 

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run.png" alt-text="Surveiller une exécution d'activité de copie":::

Dans cette vue de surveillance graphique, le service vous présente les informations d’exécution de l’activité de copie, notamment le volume de données lues/écrites, le nombre de fichiers/lignes de données copiés de la source au récepteur, le débit, les configurations appliquées pour votre scénario de copie, les étapes de l’activité de copie avec les durées et les détails correspondants, et bien plus. Reportez-vous à [cette table](#monitor-programmatically) pour chaque mesure possible et sa description détaillée. 

Dans certains scénarios, lorsque vous exécutez une activité de copie, vous voyez des **« Conseils sur le réglage des performances »** en haut de l'affichage de surveillance de l’activité de copie, comme indiqué dans l’exemple suivant. Les conseils vous indiquent le goulot d’étranglement identifié par le service pour l’exécution de copie spécifique, de même que des suggestions sur ce qui doit être modifié afin d'améliorer le débit de copie. Apprenez-en davantage sur les [conseils de réglage automatique des performances](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

La partie inférieure **Détails et durées d’exécution** décrit les étapes clés de l’activité de copie, ce qui est particulièrement utile pour résoudre les problèmes de performances de copie. Le goulot d’étranglement de votre exécution de copie correspond à celui dont la durée est la plus longue. Reportez-vous à [Résoudre les problèmes de performances de l’activité de copie](copy-activity-performance-troubleshooting.md) pour connaître chaque étape et obtenir des instructions de dépannage détaillées.

**Exemple : Copier des données d’Amazon S3 vers Azure Data Lake Storage Gen2**

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run-details.png" alt-text="Détails de surveillance d'une exécution d'activité de copie":::

## <a name="monitor-programmatically"></a>Surveiller par programmation

Les détails de l’exécution de l’activité de copie et les caractéristiques de performances sont également retournés dans la section **Résultat d’exécution de l’activité de copie** > section **Sortie**, utilisée pour afficher la surveillance IU. Voici une liste complète des propriétés qui peuvent être retournées. Vous ne verrez que les propriétés qui s’appliquent à votre scénario de copie. Pour plus d’informations sur la surveillance des exécutions d’activités par programmation en général, consultez [Surveiller par programmation une fabrique de données Azure ou un pipeline Synapse](monitor-programmatically.md).

| Nom de la propriété  | Description | Unité en sortie |
|:--- |:--- |:--- |
| dataRead | Volume réel de données lues à partir de la source. | Valeur Int64, en octets |
| dataWritten | Volume réel des données écrites/validées sur le récepteur. La taille peut être différente de la taille `dataRead`, car elle indique comment chaque magasin de données stocke les données. | Valeur Int64, en octets |
| filesRead | Nombre de fichiers lus à partir de la source basée sur des fichiers. | Valeur Int64 (aucune unité) |
| filesWritten | Nombre de fichiers écrits/validés sur le récepteur basé sur des fichiers. | Valeur Int64 (aucune unité) |
| filesSkipped | Nombre de fichiers ignorés à partir de la source basée sur des fichiers. | Valeur Int64 (aucune unité) |
| dataConsistencyVerification | Détails de la vérification de la cohérence des données où vous pouvez voir si vos données copiées ont été vérifiées pour être cohérentes entre les magasins source et de destination. Apprenez-en plus dans [cet article](copy-activity-data-consistency.md#monitoring). | Array |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données source lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| sinkPeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données récepteur lors de l’exécution de l’activité de copie. | Valeur Int64 (aucune unité) |
| rowsRead | Nombre de lignes lues à partir de la source. Cette mesure ne s’applique pas lors de la copie de fichiers en l’absence d’analyse, par exemple, lorsque les jeux de données source et de récepteur sont de type binaire, ou d’un autre type de format avec des paramètres identiques. | Valeur Int64 (aucune unité) |
| rowsCopied | Nombre de lignes copiées dans le récepteur. Cette mesure ne s’applique pas lors de la copie de fichiers en l’absence d’analyse, par exemple, lorsque les jeux de données source et de récepteur sont de type binaire, ou d’un autre type de format avec des paramètres identiques.  | Valeur Int64 (aucune unité) |
| rowsSkipped | Nombre de lignes incompatibles qui ont été ignorées. Vous pouvez faire en sorte que les lignes incompatibles soient ignorées en définissant `enableSkipIncompatibleRow` sur true. | Valeur Int64 (aucune unité) |
| copyDuration | Durée de l’exécution de la copie. | Valeur Int32, en secondes |
| throughput | Taux de transfert de données, calculé par `dataRead` divisé par `copyDuration`. | Nombre à virgule flottante, en Kbits/s |
| sourcePeakConnections | Nombre maximal de connexions simultanées établies vers le magasin de données source lors de l’exécution de l’activité de copie. | Valeur Int32 (aucune unité) |
| sinkPeakConnections| Nombre maximal de connexions simultanées établies vers le magasin de données récepteur lors de l’exécution de l’activité de copie.| Valeur Int32 (aucune unité) |
| sqlDwPolyBase | Indique si PolyBase est utilisé lorsque les données sont copiées dans Azure Synapse Analytics. | Boolean |
| redshiftUnload | Indique si UNLOAD est utilisé lorsque les données sont copiées à partir de Redshift. | Boolean |
| hdfsDistcp | Indique si DistCp est utilisé lorsque les données sont copiées à partir de HDFS. | Boolean |
| effectiveIntegrationRuntime | Runtime d’intégration (IR) ou runtimes utilisés pour alimenter l’exécution de l’activité, au format `<IR name> (<region if it's Azure IR>)`. | Texte (chaîne) |
| usedDataIntegrationUnits | Unités d’intégration de données effectives pendant la copie. | Valeur Int32 |
| usedParallelCopies | Nombre effectif de parallelCopies pendant la copie. | Valeur Int32 |
| logPath | Chemin d'accès au journal de session des données ignorées dans le stockage d’objets blob. Consultez [Tolérance de panne](copy-activity-overview.md#fault-tolerance). | Texte (chaîne) |
| executionDetails | Détails supplémentaires sur les phases de l’activité de copie et les étapes, durées, configurations, etc. correspondantes. Nous vous déconseillons d’analyser cette section, car elle est susceptible de changer. Pour mieux comprendre combien il est important de bien appréhender et résoudre les problèmes de performances de copie, reportez-vous à la section [Surveiller visuellement](#monitor-visually). | Array |
| perfRecommendation | Copier les conseils sur le réglage des performances Pour plus d’informations, consultez [Conseils sur le réglage des performances](copy-activity-performance-troubleshooting.md#performance-tuning-tips). | Array |
| billingReference | Consommation de facturation pour l’exécution donnée. Pour plus d’informations, consultez [Superviser la consommation au niveau de l’exécution de l’activité](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Object |
| durationInQueue | Durée de mise en file d’attente en secondes avant le début de l’exécution de l’activité de copie. | Object |

**Exemple :**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Voir les autres articles relatifs à l’activité de copie :

\- [Vue d’ensemble de l’activité de copie](copy-activity-overview.md)

\- [Performances de l’activité de copie](copy-activity-performance.md)
