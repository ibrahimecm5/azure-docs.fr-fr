---
title: Rapports Étiquettes de confidentialité sur vos données dans Azure Purview à l’aide de Purview Insights
description: Ce guide pratique explique comment afficher et utiliser les rapports Purview « Étiquettes de confidentialité » qui concernent vos données.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2c030d419349275204e662d78396543f5517ceca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847220"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Insights relatifs aux étiquettes de confidentialité de vos données dans Azure Purview

Ce guide pratique explique comment accéder aux insights relatifs à la sécurité fournis par les étiquettes de confidentialité appliquées à vos données, ainsi que la façon de les consulter et de les filtrer.

> [!IMPORTANT]
> Les aperçus d'étiquettes de confidentialité Azure Purview sont actuellement disponibles en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Les sources de données prises en charge sont les suivantes : Stockage Blob Azure, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance, compartiments Amazon S3, bases de données Amazon RDS (préversion publique), Power BI

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Lancer votre compte Purview à partir d’Azure.
> - Afficher les insights relatifs à l’étiquetage de confidentialité de vos données.
> - Descendre dans la hiérarchie pour obtenir plus de détails sur l’étiquetage de confidentialité de vos données.

 
## <a name="prerequisites"></a>Prérequis

Avant de commencer à utiliser les insights Purview, assurez-vous d’avoir effectué les étapes suivantes :

- Configurez vos ressources Azure et alimentez les comptes appropriés avec des données de test

- [Étendez les étiquettes de confidentialité de Microsoft 365 aux ressources dans Azure Purview](create-sensitivity-label.md), et créez ou sélectionnez les étiquettes que vous souhaitez appliquer à vos données.

- Configurez et terminez une analyse sur les données de test dans chaque source de données. Pour plus d'informations, consultez [Gérer des sources de données dans Azure Purview](manage-data-sources.md) et [Créer un ensemble de règles d'analyse](create-a-scan-rule-set.md).

- Connecté à Purview avec un compte avec le [rôle de lecteur de données ou conservateur de données](catalog-permissions.md#roles).

Pour plus d'informations, consultez [Gérer des sources de données dans Azure Purview](manage-data-sources.md) et [Étiqueter automatiquement vos données dans Azure Purview](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Utiliser les insights Purview relatifs à l’étiquetage de confidentialité

Dans Purview, les classifications sont similaires aux étiquettes d’objet et permettent de marquer et d’identifier les données d’un type spécifique qui se trouvent dans votre patrimoine de données pendant l’analyse.

Les étiquettes de confidentialité vous permettent de préciser le niveau de confidentialité de certaines données de votre organisation. Par exemple, un nom de projet spécifique peut être hautement confidentiel au sein de votre organisation, alors que ce même terme n’est pas confidentiel pour d’autres organisations. 

Les classifications sont mises en correspondance directement, par exemple un numéro de sécurité sociale, qui a la classification **Numéro de sécurité sociale**. 

En revanche, les étiquettes de confidentialité sont appliquées quand une ou plusieurs classifications et conditions sont regroupées. Dans ce contexte, « [conditions](/microsoft-365/compliance/apply-sensitivity-label-automatically) » faire référence à tous les paramètres que vous pouvez définir pour des données non structurées, par exemple, **Proximité d’une autre classification** et **Pourcentage de confiance**. 

Purview utilise les mêmes classifications, également appelées [types d’informations sensibles](/microsoft-365/compliance/sensitive-information-type-entity-definitions), que Microsoft 365. Cela vous permet d’étendre vos étiquettes de confidentialité existantes dans vos ressources Azure Purview.

> [!NOTE]
> Une fois que vous avez analysé vos types de sources, donnez quelques heures aux insights **Étiquetage de confidentialité** pour qu’ils tiennent compte des nouvelles ressources.

**Pour afficher les insights relatifs à l’étiquetage de confidentialité :**

1. Accédez à la page d’accueil d’**Azure Purview**.

1. Sur la page **Vue d’ensemble**, dans la section **Démarrer**, sélectionnez la vignette **Lancer le compte Purview**.

1. Dans Purview, sélectionnez l’élément de menu **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: à gauche pour accéder à la zone **Insights**.

1. Dans la zone **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false":::, sélectionnez **Étiquettes de confidentialité** pour afficher le rapport Purview **Insights relatifs à l’étiquetage de confidentialité**.

    > [!NOTE]
    > Si ce rapport est vide, vous n’avez peut-être pas étendu vos étiquettes de confidentialité à Azure Purview. Pour plus d’informations, consultez [Étiqueter automatiquement vos données dans Azure Purview](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Insights relatifs à l’étiquetage de confidentialité":::

   La page principale d’**Insights relatifs à l’étiquetage de confidentialité** affiche les zones suivantes :

   |Domaine  |Description  |
   |---------|---------|
   |**Vue d’ensemble des sources portant des étiquettes de confidentialité**     |Affiche les vignettes qui indiquent : <br>– le nombre d’abonnements trouvés dans vos données ; <br>– le nombre d’étiquettes de confidentialité uniques appliquées à vos données ; <br>– le nombre de sources sur lesquelles des étiquettes de confidentialité sont appliquées ; <br>– le nombre de fichiers et de tables trouvés sur lesquels des étiquettes de confidentialité sont appliquées.|
   |**Principales sources avec données étiquetées (30 derniers jours)**     | Affiche la tendance, au cours des 30 derniers jours, du nombre de sources sur lesquelles des étiquettes de confidentialité sont appliquées.       |
   |**Principales étiquettes appliquées aux sources**     |Affiche les principales étiquettes appliquées parmi toutes vos ressources de données Purview. |
   |**Principales étiquettes appliquées aux fichiers**     |Affiche les principales étiquettes de confidentialité appliquées aux fichiers dans vos données.          |
   |**Principales étiquettes appliquées aux tables**     | Affiche les principales étiquettes de confidentialité appliquées aux tables de base de données dans vos données. |   
   |  **Activité Étiquetage**  |  Affiche des graphiques distincts pour les fichiers et les tables, chacun indiquant le nombre de fichiers ou de tables étiquetés au cours de la période sélectionnée. <br>**Par défaut** : 30 jours<br>Sélectionnez le filtre **Période** au-dessus des graphiques pour sélectionner un autre intervalle de temps à afficher.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Exploration hiérarchique des insights relatifs à l’étiquetage de confidentialité

Dans l’un des graphiques **Insights relatifs à l’étiquetage de confidentialité** suivants, sélectionnez le lien **Afficher plus** pour l’explorer au niveau du détail :

- **Principales étiquettes appliquées aux sources**
- **Principales étiquettes appliquées aux fichiers**
- **Principales étiquettes appliquées aux tables**
- **Activité Étiquetage > Données étiquetées**

Par exemple :

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Exploration hiérarchique des étiquettes de confidentialité":::

Pour en savoir plus, effectuez l’une des actions suivantes :

|Option  |Description  |
|---------|---------|
|**Filtrer vos données**     |  Utilisez les filtres situés au-dessus de la grille pour filtrer les données affichées, notamment le nom de l’étiquette, le nom de l’abonnement ou le type de source. <br><br>Si vous n’êtes pas sûr du nom exact de l’étiquette, vous pouvez entrer tout ou partie du nom dans la zone **Filtrer par mot clé**.       |
|**Trier la grille** |Pour trier la grille, sélectionner l’en-tête de la colonne qui vous intéresse. | 
|**Modifier les colonnes**     |  Pour afficher plus ou moins de colonnes dans votre grille, sélectionnez **Modifier les colonnes** :::image type="icon" source="media/insights/ico-columns.png" border="false":::, puis sélectionnez les colonnes que vous souhaitez afficher ou dont vous voulez modifier l’ordre.    <br><br>Pour trier la grille, sélectionner l’en-tête de la colonne qui vous intéresse.   |
|**Explorer plus en détail**     | Pour explorer une étiquette spécifique au niveau du détail, sélectionnez un nom dans la colonne **Étiquette de confidentialité** pour afficher le rapport **Étiquette par source**. <br><br>Ce rapport affiche les données de l’étiquette sélectionnée, notamment le nom de la source, le type de source, l’ID d’abonnement et le nombre de fichiers et de tables étiquetés.      |
|**Parcourir les ressources**     |  Pour parcourir les ressources trouvées avec une étiquette ou une source spécifique, sélectionnez une ou plusieurs étiquettes ou sources (selon le rapport que vous consultez), puis sélectionnez **Parcourir les ressources** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: au-dessus des filtres. <br><br>Les résultats de la recherche affichent toutes les ressources étiquetées trouvées pour le filtre sélectionné.  Pour plus d’informations, consultez [Rechercher dans le catalogue de données Azure Purview](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Intégration des étiquettes de confidentialité avec conformité Microsoft 365

L’intégration étroite à [Microsoft Information Protection](/microsoft-365/compliance/information-protection) proposée dans Microsoft 365 signifie que Purview permet d’étendre la visibilité de votre patrimoine de données et de classifier et d’étiqueter vos données de façon directe.

Pour que vos étiquettes de confidentialité Microsoft 365 soient étendues à vos ressources dans Azure Purview, vous devez activer Information Protection pour Azure Purview dans le centre de conformité Microsoft 365.

Pour plus d’informations, consultez [Étiqueter automatiquement vos données dans Azure Purview](create-sensitivity-label.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur ces rapports d’insights Azure Purview :

- [Insights relatifs aux glossaires](glossary-insights.md)
- [Insights relatifs aux analyses](scan-insights.md)
- [Insights relatifs aux classifications](./classification-insights.md)
