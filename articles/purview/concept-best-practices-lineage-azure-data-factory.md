---
title: Meilleures pratiques en matière de traçabilité des données Azure Purview
description: Cet article présente les meilleures pratiques pour la traçabilité des données de diverses sources de données dans Azure Purview.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 0036004e8222ce79fc43e5d6603abecc2e375b1a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479022"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Meilleures pratiques en matière de traçabilité des données Azure Purview

La traçabilité des données est comprise au sens large comme le cycle de vie qui couvre l’origine des données et où elle évolue au fil du temps dans le patrimoine des données. Purview peut capturer la traçabilité des données dans différentes parties du patrimoine de données de votre organisation et à différents niveaux de préparation, y compris : 
* Données brutes entièrement mises en lots à partir de différentes plateformes 
* Données transformées et préparées 
* Données utilisées par les plateformes de visualisation

 
## <a name="intended-audience"></a>Public concerné

* Ingénieur Data 
* Data Scientist 
* Propriétaire des données 

## <a name="why-do-you-need-adopt-lineage"></a>Pourquoi avez-vous besoin d’adopter une traçabilité ?  

La traçabilité des données est le processus qui consiste à décrire les données qui existent, où elles sont stockées et comment elles transitent entre les systèmes. Il existe de nombreuses raisons pour lesquelles la traçabilité des données est importante, mais à un niveau élevé, elles peuvent toutes se résumer à trois catégories principales que nous explorerons ici : 
* Suivre les données dans les rapports 
* Analyse d’impact 
* Capturer les modifications et l’emplacement où les données se trouvent durant le cycle de vie des données 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Meilleures pratiques et considérations en matière de traçabilité Azure Data Factory 

### <a name="azure-data-factory-instance"></a>Instance Azure Data Factory 

* La traçabilité des données n’est pas signalée automatiquement au catalogue jusqu’à ce que l’état de la connexion Data Factory passe à Connected. Les états Disconnected et CannotAccess ne peuvent pas capturer la traçabilité. 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="Capture d’écran montrant une liste de connexions Data Factory." lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* Chaque instance Data Factory peut se connecter à un seul compte Purview. Vous pouvez établir une nouvelle connexion dans un autre compte Purview, mais cette opération passe la connexion existante à Disconnected.  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Capture d’écran montrant un avertissement pour déconnecter Azure Data Factory.":::

* L’identité managée de Data Factory est utilisée pour authentifier la traçabilité dans le compte Purview, le rôle Conservateur de données de l’identité managée de Data Factory sur la collection racine Purview est requis. 
* Ne prend pas en charge plus de 10 fabriques de données à la fois. Si vous souhaitez ajouter plus de 10 fabriques de données à la fois, veuillez créer un ticket de support. 

### <a name="azure-data-factory-activities"></a>Activités Azure Data Factory  

* Azure Purview capture la traçabilité du runtime à partir des activités Azure Data Factory suivantes : 
    * [Activité Copy](../data-factory/copy-activity-overview.md)
    * [Activité Data Flow](../data-factory/concepts-data-flow-overview.md)
    * [Activité Exécuter le package SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* Azure Purview supprime la traçabilité si la source ou la destination utilise un système de stockage de données non pris en charge.  
    * Les sources de données prises en charge dans l’activité Copy sont répertoriées dans **Prise en charge des activités Copy** de [Se connecter à Azure Data Factory](how-to-link-azure-data-factory.md)
    * Les sources de données prises en charge dans l’activité de flux de données sont répertoriées dans **Prise en charge des flux de données** de [Se connecter à Azure Data Factory](how-to-link-azure-data-factory.md)
    * Les sources de données prises en charge dans SSIS sont répertoriées dans **Prise en charge de l’activité de package d’exécution SSIS** de [Traçabilité de SQL Server Integration Services](how-to-lineage-sql-server-integration-services.md)

* Purview ne peut pas capturer la traçabilité si l’activité Copy Azure Data Factory utilise les fonctionnalités de l’activité Copy énumérées dans **Limitations sur la traçabilité** de [Se connecter à Azure Data Factory](how-to-link-azure-data-factory.md)  

* En ce qui concerne la traçabilité de l’activité Dataflow, Purview ne prend en charge que la source et le récepteur. La traçabilité de la transformation Dataflow n’est pas encore prise en charge. 

* La traçabilité du flux de données ne s’intègre pas à l’ensemble de ressources Purview. 

    **Exemple 1 d’ensemble de ressources**    

    Nom qualifié : https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    Nom d’affichage : « données » 

* En ce qui concerne la traçabilité de l’activité Exécuter le package SSIS, nous ne prenons en charge que la source et la destination. La traçabilité de la transformation n’est pas encore prise en charge. 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Capture d’écran de la traçabilité de l’activité Execute SSIS dans Purview." lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* Reportez-vous au guide pas à pas suivant pour la [traçabilité Azure Data Factory dans Purview](../data-factory/tutorial-push-lineage-to-purview.md).  

## <a name="next-steps"></a>Étapes suivantes
-  [Gérer les sources de données](./manage-data-sources.md)
