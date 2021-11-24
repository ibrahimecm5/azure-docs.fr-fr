---
title: Comment appliquer automatiquement des étiquettes de confidentialité à vos données dans Azure Purview
description: Découvrez comment créer des étiquettes de confidentialité et les appliquer automatiquement à vos données lors d’une analyse.
author: ajaykar
ms.author: ajaykar
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 6bcb751c58b4ba14e84f529ec268beacc93fdcf4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339558"
---
# <a name="how-to-automatically-apply-sensitivity-labels-to-your-data-in-azure-purview"></a>Comment appliquer automatiquement des étiquettes de confidentialité à vos données dans Azure Purview

## <a name="create-or-extend-existing-sensitivity-labels-to-azure-purview"></a>Créer des étiquettes de confidentialité ou étendre des étiquettes existantes à Azure Purview

> [!IMPORTANT]
> Les étiquettes de confidentialité Azure Purview sont actuellement disponibles en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.
>

Si vous ne disposez pas encore d’étiquettes de confidentialité, vous devez les créer et les rendre accessibles à Azure Purview. Il est également possible de modifier les étiquettes de confidentialité existantes pour les rendre accessibles à Azure Purview.

### <a name="step-1-licensing-requirements"></a>Étape 1 : exigences en termes de licence

Les étiquettes de sensibilité sont créées et gérées dans le Centre de conformité Microsoft 365. Pour créer des étiquettes de confidentialité à utiliser dans Azure Purview, vous devez disposer d’une licence Microsoft 365 active qui offre l’avantage d’appliquer automatiquement des étiquettes de confidentialité.

Pour obtenir la liste complète des licences, consultez le [Forum aux questions sur les étiquettes de confidentialité dans Azure Purview](sensitivity-labels-frequently-asked-questions.yml). Si vous ne disposez pas encore de la licence requise, vous pouvez vous inscrire pour obtenir une version d’évaluation de [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="step-2-consent-to-use-sensitivity-labels-in-azure-purview"></a>Étape 2 : autoriser l’utilisation des étiquettes de confidentialité dans Azure Purview

Les étapes suivantes étendent vos étiquettes de confidentialité et leur permettent d’être disponibles pour une utilisation dans Azure Purview, où vous pouvez appliquer des étiquettes de confidentialité à des fichiers et colonnes de base de données.

1. Dans Microsoft 365, accédez à la page **Information Protection**.</br>
   Si vous avez récemment configuré votre abonnement pour Information Protection, l’affichage de la page **Information Protection** peut prendre plusieurs heures.
1. Dans la zone **Étendre l’étiquetage aux ressources dans Azure Purview**, sélectionnez le bouton **Activer**, puis sélectionnez **Oui** dans la boîte de dialogue de confirmation qui s’affiche.

Par exemple :

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-small.png" alt-text="Sélection du bouton « Activer » pour étendre les étiquettes de confidentialité à Purview" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview.png":::

:::image type="content" source="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation-small.png" alt-text="Confirmer le choix d’étendre les étiquettes de confidentialité à Purview" lightbox="media/how-to-automatically-label-your-content/extend-sensitivity-labels-to-purview-confirmation.png":::

> [!TIP]
>Si vous ne voyez pas le bouton et que vous ne savez pas si le consentement a été accordé pour étendre l’étiquetage aux ressources dans Purview, consultez [cet élément de la FAQ](sensitivity-labels-frequently-asked-questions.yml#how-can-i-determine-if-consent-has-been-granted-to-extend-labeling-to-purview) sur la manière de déterminer l’état.
>

Une fois que vous avez étendu l’étiquetage aux ressources dans Azure Purview, toutes les étiquettes de confidentialité publiées peuvent être utilisées dans Purview.

### <a name="step-3-create-or-modify-existing-label-to-automatically-label-content"></a>Étape 3 : créer ou modifier une étiquette existante pour étiqueter automatiquement le contenu

**Pour créer des étiquettes de confidentialité ou modifier des étiquettes existantes** :

1. Ouvrez le [Centre de conformité Microsoft 365](https://compliance.microsoft.com/).

1. Sous **Solutions**, sélectionnez **Information Protection**, puis **Créer une étiquette**.

    :::image type="content" source="media/how-to-automatically-label-your-content/create-sensitivity-label-full-small.png" alt-text="Création d’étiquettes de confidentialité dans le Centre de conformité Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-sensitivity-label-full.png":::

1. Donnez un nom à l’étiquette. Ensuite, sous **Définir l’étendue de cette étiquette** :

    - Dans tous les cas, sélectionnez **Ressources Azure Purview**.
    - Pour étiqueter des fichiers, sélectionnez également **Fichiers et e-mails**. Cette option n’est pas nécessaire pour étiqueter uniquement des ressources de base de données

    :::image type="content" source="media/how-to-automatically-label-your-content/create-label-scope-small.png" alt-text="Étiqueter automatiquement dans le Centre de conformité Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-label-scope.png":::

1. Suivez les autres invites pour configurer les paramètres d’étiquette.

    Plus précisément, définissez des règles d’étiquetage automatique pour les fichiers et les colonnes de base de données :

    - [Définition de règles d’étiquetage automatique pour les fichiers](#autolabeling-for-files)
    - [Définition de règles d’étiquetage automatique pour les colonnes de base de données](#autolabeling-for-database-columns)

    Pour plus d’informations sur les options de configuration, consultez [Fonctions des étiquettes de confidentialité](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) dans la documentation de Microsoft 365.

1. Répétez les étapes indiquées ci-dessus pour créer des étiquettes supplémentaires.

    Pour créer une sous-étiquette, sélectionnez l’étiquette parent > **…**  > **Autres actions** > **Ajouter une sous-étiquette**.

1. Pour modifier des étiquettes existantes, accédez à **Information Protection** > **Étiquettes**, puis sélectionnez votre étiquette.

    Sélectionnez ensuite **Modifier l’étiquette** pour rouvrir la configuration **Modifier l’étiquette de confidentialité**, avec tous les paramètres que vous avez définis lors de la création de l’étiquette.

    :::image type="content" source="media/how-to-automatically-label-your-content/edit-sensitivity-label-full-small.png" alt-text="Modification d’une étiquette de confidentialité existante" lightbox="media/how-to-automatically-label-your-content/edit-sensitivity-label-full.png":::

1. Lorsque vous avez terminé de créer toutes vos étiquettes, veillez à vérifier leur ordre et à les réorganiser en fonction des besoins.

    Pour modifier l’ordre d’une étiquette, sélectionnez **…** **> Autres actions** > **Déplacer vers le haut** ou **Déplacer vers le bas**.

    Pour plus d’informations, consultez [Priorité des étiquettes (ordre significatif)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) dans la documentation de Microsoft 365.

#### <a name="autolabeling-for-files"></a>Étiquetage automatique pour les fichiers

Définissez des règles d’étiquetage automatique pour les fichiers lorsque vous créez ou modifiez votre étiquette.

Sur la page **Étiquetage automatique pour les applications Office**, activez **Étiquetage automatique pour les applications Office**, puis définissez les conditions dans lesquelles vous souhaitez que votre étiquette soit appliquée automatiquement à vos données.

Par exemple :

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files-small.png" alt-text="Définir des règles d’étiquetage automatique pour les fichiers dans le Centre de conformité Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-files.png":::

Pour plus d’informations, consultez [Application automatique d’une étiquette de confidentialité aux données](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) dans la documentation de Microsoft 365.

#### <a name="autolabeling-for-database-columns"></a>Étiquetage automatique pour les colonnes de base de données

Définissez des règles d’étiquetage automatique pour les colonnes de base de données lorsque vous créez ou modifiez votre étiquette.

Sous l’option **Colonnes de base de données** :

1. Sélectionnez le curseur **Étiquetage automatique pour les colonnes de base de données**.

1. Sélectionnez **Vérifier les types d’informations sensibles** pour choisir les types d’informations sensibles à appliquer à votre étiquette.

Par exemple :

:::image type="content" source="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns-small.png" alt-text="Définir des règles d’étiquetage automatique pour les colonnes SQL dans le Centre de conformité Microsoft 365" lightbox="media/how-to-automatically-label-your-content/create-auto-labeling-rules-db-columns.png":::

### <a name="step-4-publish-labels"></a>Étape 4 : publier des étiquettes

Après avoir créé une étiquette, vous devez analysez vos données dans Azure Purview pour appliquer automatiquement les étiquettes créées, en fonction des règles d’étiquetage automatique définies.

## <a name="scan-your-data-to-apply-sensitivity-labels-automatically"></a>Analyser vos données pour appliquer automatiquement des étiquettes de confidentialité

Analysez vos données dans Azure Purview pour appliquer automatiquement les étiquettes créées, en fonction des règles d’étiquetage automatique définies. Patientez jusqu’à 24 heures pour que les étiquettes de confidentialité se reflètent dans Purview.

Pour savoir comment configurer des analyses sur différentes ressources dans Azure Purview, consultez :

|Source  |Référence  |
|---------|---------|
|**Fichiers dans Stockage** | [Inscription et analyse du Stockage Blob Azure](register-scan-azure-blob-storage-source.md) </br> [Inscription et analyse de fichiers Azure Files](register-scan-azure-files-storage-source.md)[Inscription et analyse d’Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Inscription et analyse d’Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)</br>[Inscription et analyse d’Amazon S3](register-scan-amazon-s3.md) |
|**colonnes de la base de données** | [Inscrire et analyser une base de données Azure SQL](register-scan-azure-sql-database.md) </br>[Inscrire et analyser une instance Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md) </br> [Inscrire et analyser des pools SQL dédiés](register-scan-azure-synapse-analytics.md)</br> [Inscrire et analyser des espaces de travail Azure Synapse Analytics](register-scan-azure-synapse-analytics.md) </br> [Inscription et analyse d’Azure Cosmos DB (API SQL)](register-scan-azure-cosmos-database.md) </br> [Inscrire et analyser une base de données Azure MySQL](register-scan-azure-mysql-database.md) </br> [Inscrire et analyser une base de données Azure pour PostgreSQL](register-scan-azure-postgresql.md) |
| | |

## <a name="view-labels-on-assets-in-the-catalog"></a>Afficher des étiquettes sur les ressources dans le catalogue

Une fois que vous avez défini les règles d’étiquetage automatique de vos étiquettes dans Microsoft 365 et analysé vos données dans Azure Purview, les étiquettes sont automatiquement appliquées à vos ressources.

**Pour afficher les étiquettes appliquées à vos ressources dans le catalogue Azure Purview :**

Dans le catalogue Azure Purview, utilisez les options de filtrage **Étiquette** pour afficher uniquement les ressources comportant des étiquettes spécifiques. Par exemple :

:::image type="content" source="media/how-to-automatically-label-your-content/filter-search-results-small.png" alt-text="Recherche de ressources par étiquette" lightbox="media/how-to-automatically-label-your-content/filter-search-results.png":::

Pour afficher les détails d’une ressource, y compris les classifications trouvées et l’étiquette appliquée, cliquez dessus dans les résultats.

Par exemple :

:::image type="content" source="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage-small.png" alt-text="Affichage d’une étiquette de confidentialité sur un fichier dans le Stockage Blob Azure" lightbox="media/how-to-automatically-label-your-content/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Affichage des rapports d’analyse pour les classifications et les étiquettes de confidentialité

Retrouvez des insights sur vos données classées et étiquetées dans Azure Purview à l’aide des rapports **Classification** et **Étiquetage de confidentialité**.

> [!div class="nextstepaction"]
> [Insights relatifs aux classifications](./classification-insights.md)

> [!div class="nextstepaction"]
> [Insights relatifs aux étiquettes de confidentialité](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Vue d’ensemble de l’étiquetage dans Azure Purview](create-sensitivity-label.md)

> [!div class="nextstepaction"]
> [Forum aux questions sur l’étiquetage](sensitivity-labels-frequently-asked-questions.yml)