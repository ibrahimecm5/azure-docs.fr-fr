---
title: Comprendre les tarifs Azure Data Factory via des exemples
description: Cet article explique et décrit le modèle tarifaire Azure Data Factory avec des exemples détaillés.
author: shirleywangmsft
ms.author: shwang
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: pricing
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 944a49b0c58d1c6e197d385fd6bd029aa29631c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251943"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Comprendre les tarifs Data Factory à travers des exemples

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article explique et décrit le modèle tarifaire Azure Data Factory avec des exemples détaillés.

> [!NOTE]
> Les prix mentionnés dans les exemples ci-dessous sont hypothétiques et ne sont pas destinés à indiquer la tarification réelle.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copier des données d’AWS S3 vers le Stockage Blob Azure toutes les heures

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3.

2. Un jeu de données de sortie pour les données sur le Stockage Azure.

3. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

   :::image type="content" source="media/pricing-concepts/scenario1.png" alt-text="Ce diagramme illustre un pipeline avec un déclencheur de planification. Dans le pipeline, l’activité de copie est acheminée vers un jeu de données d’entrée, qui est acheminé vers un service lié A W S S3. Elle est également acheminée vers un jeu de données de sortie, qui acheminé vers un service lié de Stockage Azure.":::

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 2 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 2 exécutions d’activité (1 pour l’exécution du déclencheur, 1 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 2 enregistrements d’exécution de monitoring récupérés (1 pour l’exécution du pipeline, 1 pour l’exécution d’activité) |

**Prix total du scénario : 0,16811 $**

- Opérations Data Factory = **0,0001 $**
  - Lecture/écriture = 10\*0,00001 = 0,0001 $ [1 lecture/écriture = 0,50/50000 $ = 0,00001]
  - Monitoring  = 2\*0,000005 = 0,00001 $ [1 monitoring = 0,25/50000 $ = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,168 $**
  - Exécutions d’activité = 0,001\*2 = 0,002 $ [1 exécution = 1/1000 $ = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copier des données et les transformer toutes les heures avec Azure Databricks

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure et les transformer avec Azure Databricks selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3 et un jeu de données de sortie pour les données sur le Stockage Azure.
2. Une activité Azure Databricks pour la transformation de données.
3. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

:::image type="content" source="media/pricing-concepts/scenario2.png" alt-text="Ce diagramme illustre un pipeline avec un déclencheur de planification. Dans le pipeline, l’activité de copie est acheminée vers un jeu de données d’entrée, un jeu de données de sortie et une activité DataBricks, qui s’exécute sur Azure Databricks. Le jeu de données d’entrée est acheminé vers un service lié A W S S3. Le jeu de données de sortie est acheminé vers un service lié Stockage Azure.":::

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 3 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 3 exécutions d’activité (1 pour l’exécution du déclencheur, 2 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 3 enregistrements d’exécution de monitoring récupérés (1 pour l’exécution du pipeline, 2 pour l’exécution d’activité) |
| Exécution de l’activité Databricks – hypothèse : temps d’exécution = 10 minutes | 10 minutes d’exécution de l’activité de pipeline externe |

**Prix total du scénario : 0,16916 $**

- Opérations Data Factory = **0,00012 $**
  - Lecture/écriture = 11\*0,00001 = 0,00011 $ [1 lecture/écriture = 0,50/50000 $ = 0,00001]
  - Monitoring = 3\*0,000005 = 0,00001 $ [1 monitoring = 0,25/50 000 $ = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,16904 $**
  - Exécutions d’activité = 0,001\*3 = 0,003 $ [1 exécution = 1/1000 $ = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)
  - Activité du pipeline externe = 0,000041 $ (au prorata de 10 minutes de durée d’exécution, à 0,00025 $/h par Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copier des données et les transformer toutes les heures avec des paramètres dynamiques

Dans ce scénario, vous souhaitez copier des données d’AWS S3 vers le Stockage Blob Azure et les transformer avec Azure Databricks (avec des paramètres dynamiques dans le script) selon une planification horaire.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de copie avec un jeu de données d’entrée pour les données à copier à partir d’AWS S3 et un jeu de données de sortie pour les données sur le Stockage Azure.
2. Une activité de recherche pour transmettre dynamiquement des paramètres au script de transformation.
3. Une activité Azure Databricks pour la transformation de données.
4. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

:::image type="content" source="media/pricing-concepts/scenario3.png" alt-text="Ce diagramme illustre un pipeline avec un déclencheur de planification. Dans le pipeline, l’activité de copie est acheminée vers un jeu de données d’entrée, un jeu de données de sortie et une activité de recherche qui achemine les données vers une activité DataBricks, qui s’exécute sur Azure Databricks. Le jeu de données d’entrée est acheminé vers un service lié A W S S3. Le jeu de données de sortie est acheminé vers un service lié Stockage Azure.":::

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 3 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 4 exécutions d’activité (1 pour l’exécution du déclencheur, 3 pour les exécutions d’activité) |
| Copie de données – hypothèse : temps d’exécution = 10 minutes | 10 \* 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 4 enregistrements d’exécution de monitoring récupérés (1 pour l’exécution du pipeline, 3 pour l’exécution d’activité) |
| Exécution de l’activité de recherche – hypothèse : temps d’exécution = 1 minute | 1 minute d’exécution de l’activité de pipeline |
| Exécution de l’activité Databricks – hypothèse : temps d’exécution = 10 minutes | 10 minutes d’exécution de l’activité de pipeline externe |

**Prix total du scénario : 0,17020 $**

- Opérations Data Factory = **0,00013 $**
  - Lecture/écriture = 11\*0,00001 = 0,00011 $ [1 lecture/écriture = 0,50/50000 $ = 0,00001]
  - Monitoring  = 4\*0,000005 = 0,00002 $ [1 monitoring = 0,25/50000 $ = 0,000005]
- Exécution &amp; orchestration de pipeline = **0,17007 $**
  - Exécutions d’activité = 0,001\*4 = 0,004 $ [1 exécution = 1/1000 $ = 0,001]
  - Activités de déplacement des données = 0,166 $ (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)
  - Activité du pipeline = 0,00003 (au prorata d’1 minute de durée d’exécution, à 0,002 $/h par Azure Integration Runtime)
  - Activité du pipeline externe = 0,000041 $ (au prorata de 10 minutes de durée d’exécution, à 0,00025 $/h par Azure Integration Runtime)

## <a name="run-ssis-packages-on-azure-ssis-integration-runtime"></a>Exécuter des packages SSIS sur le runtime d’intégration Azure-SSIS

Le runtime d’intégration Azure-SSIS (IR) est un cluster spécialisé de machines virtuelles Azure pour les exécutions de packages SSIS dans Azure Data Factory (ADF). Lorsque vous l’approvisionnez, il vous sera dédié. Il sera donc facturé comme n’importe quelle autre machine virtuelle Azure dédiée, à condition que vous l’exécutiez, que vous l’utilisiez ou non pour exécuter des packages SSIS. En ce qui concerne son coût de fonctionnement, l'estimation horaire est affichée sur son volet de configuration dans le portail ADF. Par exemple :  

:::image type="content" source="media/pricing-concepts/ssis-pricing-example.png" alt-text="Exemple de tarif SSIS":::

Dans l’exemple ci-dessus, si vous conservez votre Azure-SSIS IR en cours d’exécution pendant 2 heures, vous êtes facturé : **2 (heures) x US$1.158/heure = US$2.316**.

Pour faire évoluer le coût d’exécution de votre Azure-SSIS IR, vous pouvez réduire la taille de votre machine virtuelle, effectuer un scale-in de la taille de votre cluster, utiliser votre propre licence SQL Server via l’option Azure Hybrid Benefit (AHB) (cela peut vous permettre de faire des économies substantielles, comme l’indique la [page de tarification Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/)) et/ou démarrer et arrêter votre Azure-SSIS IR lorsque vous souhaitez traiter vos charges de travail SSIS, selon un modèle juste-à-temps. Pour en savoir plus, consultez la section [Pour reconfigurer un runtime d’intégration Azure-SSIS](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir) et la page [Guide pratique pour démarrer et arrêter Azure-SSIS Integration Runtime selon une planification](how-to-schedule-azure-ssis-integration-runtime.md).

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Utilisation du débogage des flux de données de mappage pour une journée de travail normale

En tant qu’ingénieur Données, Sam est en charge au quotidien de la conception, de la création et du test des flux de données de mappage. Le matin, Sam se connecte à l’IU ADF et active le mode débogage pour les flux de données. La durée TTL par défaut pour les sessions de débogage est de 60 minutes. Sam travaillant 8 heures par jour, la session de débogage n’expire jamais. Par conséquent, les frais quotidiens de Sam sont les suivants :

**8 (heures) x 8 (cœurs optimisés pour le calcul) x 0,193 $ = 12,35 $**

En même temps, Chris, un autre ingénieur Données, se connecte également à l’interface utilisateur du navigateur ADF pour le profilage des données et le travail de conception des tâches d’extraction, de transformation et de chargement (ETL). Chris n’opère pas toute la journée dans ADF comme Sam. Chris a uniquement besoin d’utiliser le débogueur de flux de données pendant 1 heure durant la même période et le même jour que Sam ci-dessus. Les frais que Chris expose pour le débogage sont les suivants :

**1 (heure) x 8 (cœurs à usage général) x 0,274 USD = 2,19 USD**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformer des données dans le magasin d’objets blob à l’aide des flux de données de mappage

Dans ce scénario, vous souhaitez transformer visuellement toutes les heures des données dans un magasin d’objets blob à partir du mappage des flux de données ADF.

Pour réaliser ce scénario, créez un pipeline avec les éléments suivants :

1. Une activité de flux de données avec la logique de transformation.

2. Un jeu de données d’entrée pour les données sur le Stockage Azure.

3. Un jeu de données de sortie pour les données sur le Stockage Azure.

4. Un déclencheur de planification pour exécuter le pipeline toutes les heures.

| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 2 entités en lecture/écriture  |
| Création de jeux de données | 4 entités en lecture/écriture (2 pour la création du jeu de données, 2 pour les références de service lié) |
| Création d’un pipeline | 3 entités de lecture/écriture (1 pour la création du pipeline, 2 pour les références de jeu de données) |
| Récupération d’un pipeline | 1 entité en lecture/écriture |
| Exécution d’un pipeline | 2 exécutions d’activité (1 pour l’exécution du déclencheur, 1 pour les exécutions d’activité) |
| Hypothèses concernant le flux de données : durée d’exécution = 10 min + durée de vie de 10 min | 10 \* 16 cœurs de calcul général avec une durée de vie de 10 |
| Monitoring du pipeline – hypothèse : 1 seule exécution s’est produite | 2 enregistrements d’exécution de monitoring récupérés (1 pour l’exécution du pipeline, 1 pour l’exécution d’activité) |

**Prix total du scénario : 1,4631 $**

- Opérations Data Factory = **0,0001 $**
  - Lecture/écriture = 10\*0,00001 = 0,0001 $ [1 lecture/écriture = 0,50/50000 $ = 0,00001]
  - Monitoring  = 2\*0,000005 = 0,00001 $ [1 monitoring = 0,25/50000 $ = 0,000005]
- Exécution et orchestration du pipeline = **1,463 $**
  - Exécutions d’activité = 0,001\*2 = 0,002 $ [1 exécution = 1/1000 $ = 0,001]
  - Activités de flux de données = 1,461 $ au prorata d’une durée de 20 minutes (durée d’exécution de 10 minutes + durée de vie de 10 minutes). 0,274 $/h sur Azure Integration Runtime avec 16 cœurs de calcul général

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Intégration de données dans un VNET managé Azure Data Factory
Dans ce scénario, vous souhaitez supprimer des fichiers d’origine d’un Stockage Blob Azure et copier des données d’Azure SQL Database vers le Stockage Blob Azure. Vous effectuerez cette opération à deux reprises sur des pipelines différents. Le temps d’exécution de ces deux pipelines se chevauche.
:::image type="content" source="media/pricing-concepts/scenario-4.png" alt-text="Scénario 4":::
Pour réaliser ce scénario, vous devez créer deux pipelines avec les éléments suivants :
  - Activité de pipeline – Supprimer l’activité.
  - Activité de copie avec un jeu de données d’entrée pour les données à copier à partir du Stockage Blob Azure.
  - Un jeu de données de sortie pour les données sur Azure SQL Database.
  - Un déclencheur de planification pour exécuter le pipeline.


| **Opérations** | **Types et unités** |
| --- | --- |
| Création d’un service lié | 4 entités en lecture/écriture |
| Création de jeux de données | 8 entités en lecture/écriture (4 pour la création du jeu de données, 4 pour les références de service lié) |
| Création d’un pipeline | 6 entités de lecture/écriture (2 pour la création du pipeline, 4 pour les références de jeu de données) |
| Récupération d’un pipeline | 2 entités en lecture/écriture |
| Exécution d’un pipeline | 6 exécutions d’activité (2 pour l’exécution du déclencheur, 4 pour les exécutions d’activité) |
| Exécuter une activité de suppression : chaque durée d’exécution = 5 min. L’exécution de l’activité de suppression dans le premier pipeline a lieu de 10:00 UTC à 10:05 UTC. L’exécution de l’activité de suppression dans le deuxième pipeline a lieu de 10:02 UTC à 10:07 UTC.|Exécution d’activité de pipeline totale de 7 minutes dans le VNET managé. L’activité de pipeline prend en charge jusqu’à 50 accès simultanés dans le VNET managé. L’activité de pipeline a une durée de vie (TTL) de 60 minutes.|
| Hypothèse de copie de données : chaque durée d’exécution = 10 min. L’exécution de la copie dans le premier pipeline a lieu de 10:06 UTC et 10:15 UTC. L’exécution de l’activité de copie dans le deuxième pipeline a lieu de 10:08 UTC à 10:17 UTC. | 10 * 4 Azure Integration Runtime (paramètre DIU par défaut = 4) – pour plus d’informations sur les unités d’intégration de données et l’optimisation des performances de copie, voir [cet article](copy-activity-performance.md) |
| Monitoring du pipeline – hypothèse : Seules 2 exécutions ont eu lieu | 6 enregistrements d’exécution de monitoring récupérés (2 pour l’exécution du pipeline, 4 pour l’exécution d’activité) |


**Prix total du scénario : 1,45523 USD**

- Opérations Data Factory = 0,00023 USD
  - Lecture/écriture = 20*0,00001 = 0,0002 $ [1 lecture/écriture = 0,50/50000 $ = 0,00001]
  - Monitoring = 6*0,000005 = 0,00003 $ [1 monitoring = 0,25/50000 $ = 0,000005]
- Exécution et orchestration du pipeline = 1,455 USD
  - Exécutions d’activité = 0,001*6 = 0,006 $ [1 exécution = 1/1000 $ = 0,001]
  - Activités de déplacement des données = 0,333 USD (au prorata de 10 minutes de durée d’exécution, à 0,25 $/h par Azure Integration Runtime)
  - Activité du pipeline = 1,116 USD (au prorata de 7 minutes de durée d’exécution plus TTL de 60 minutes. à 1 USD/h par Azure Integration Runtime)

> [!NOTE] 
> Ces prix sont fournis uniquement à titre d’exemple.

**FORUM AUX QUESTIONS**

Q : Si je souhaite exécuter plus de 50 activités de pipeline, est-il possible d’exécuter ces activités simultanément ?

R : Au maximum 50 activités de pipeline simultanées sont autorisées.  La 51° activité de pipeline sera mise en file d’attente jusqu’à l’ouverture d’un « emplacement libre ». Identique pour une activité externe. Au maximum 800 activités externes simultanées sont autorisées.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous comprenez les tarifs associés à Azure Data Factory, vous pouvez vous lancer !

- [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Présentation du service Azure Data Factory](introduction.md)

- [Création visuelle dans Azure Data Factory](author-visually.md)
