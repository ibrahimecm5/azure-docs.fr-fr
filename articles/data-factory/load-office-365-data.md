---
title: Charger des données d’Office 365 à l’aide d’Azure Data Factory
description: Utiliser Azure Data Factory pour copier des données à partir d’Office 365
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 07/05/2021
ms.author: jianleishen
ms.openlocfilehash: 6fa350757ff1d4595c0eaa9c3dffb1c7299fb116
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124835974"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Charger des données d’Office 365 à l’aide d’Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article vous montre comment _charger des données d’Office 365 dans le stockage d’objets blob Azure_ à l’aide de Data Factory. Vous pouvez suivre une procédure similaire pour copier des données vers Azure Data Lake Gen1 ou Gen2. Reportez-vous à l’[article sur le connecteur Office 365](connector-office-365.md) traitant de la copie des données à partir d’Office 365 en général.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Dans le menu de gauche, sélectionnez **Créer une ressource** > **Analytics** > **Data Factory** : 
   
   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="Sélection de Data Factory dans le volet &quot;Nouveau&quot; ":::

2. Sur la page **Nouvelle fabrique de données**, fournissez les valeurs des champs qui apparaissent dans l’image suivante :
      
   :::image type="content" source="./media/load-office-365-data/new-azure-data-factory.png" alt-text="Page Nouvelle fabrique de données":::
 
    * **Name** : Entrez un nom global unique pour votre fabrique de données Azure. Si l’erreur « Le nom de fabrique de données *LoadFromOffice365Demo* n’est pas disponible » apparaît, saisissez un autre nom pour la fabrique de données. Par exemple, utilisez le nom _**votrenom**_ **LoadFromOffice365Demo**. Essayez à nouveau de créer la fabrique de données. Pour savoir comment nommer les artefacts Data Factory, voir [Data Factory - Règles d’affectation des noms](naming-rules.md).
    * **Abonnement**: Sélectionnez l’abonnement Azure dans lequel créer la fabrique de données. 
    * **Groupe de ressources** : Sélectionnez un groupe de ressources existant dans la liste déroulante ou sélectionnez l’option **Créer** et entrez le nom d’un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/management/overview.md).  
    * **Version** : Sélectionnez **V2**.
    * **Emplacement** : Sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge sont affichés dans la liste déroulante. Les magasins de données utilisés par la fabrique de données peuvent se trouver dans d’autres emplacements et régions. Ces magasins de données incluent Azure Data Lake Store, Stockage Azure, Azure SQL Database, etc.

3. Sélectionnez **Create** (Créer).
4. Une fois la création terminée, accédez à votre fabrique de données. La page d’accueil **Data Factory** devrait s’afficher comme dans l’image suivante :
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Page d’accueil d’Azure Data Factory, avec la mosaïque Ouvrir Azure Data Factory Studio.":::

5. Sélectionnez **Ouvrir** dans la vignette **Ouvrir Azure Data Factory Studio** pour lancer l’application d’intégration de Data Factory dans un onglet distinct.

## <a name="create-a-pipeline"></a>Créer un pipeline

1. Dans la page d’accueil, sélectionnez **Orchestrer**.
 
    :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="Capture d’écran montrant la page d’accueil ADF.":::

2. Dans l’onglet **Général** du pipeline, entrez « CopyPipeline » pour le **nom** du pipeline.

3. Dans la boîte à outils Activités > catégorie Déplacer et transformer, faites glisser l’**activité Copier** de la boîte à outils vers la surface du concepteur de pipeline. Spécifiez « CopyFromOffice365ToBlob » comme nom d’activité.

### <a name="configure-source"></a>Configurer la source

1. Accédez au pipeline > **onglet Source**, puis cliquez sur **+ Nouveau** pour créer un jeu de données source. 

2. Dans la fenêtre Nouveau jeu de données, sélectionnez **Office 365**, puis sélectionnez **Continuer**.
 
3. Vous êtes maintenant dans l’onglet Configuration de l’activité de copie. Cliquez sur le bouton **Modifier** en regard du jeu de données Office 365 pour continuer la configuration des données.

    :::image type="content" source="./media/load-office-365-data/transition-to-edit-dataset.png" alt-text="Configuration générale du jeu de données Office 365":::
 
4. Vous voyez un nouvel onglet ouvert pour le jeu de données Office 365. Dans l’**onglet Général** en bas de la fenêtre Propriétés, entrez « SourceOffice365Dataset » pour le nom.
 
5. Accédez à l’onglet **Connexion** dans la fenêtre Propriétés. Cliquez sur **+ Nouveau** en regard de la zone de texte Service lié.

6. Dans la fenêtre Nouveau service lié, entrez « Office365LinkedService » comme nom, saisissez l’ID et la clé du principal de service et la clé du principal de service, puis testez la connexion et sélectionnez **Créer** pour déployer le service lié.

    :::image type="content" source="./media/load-office-365-data/new-office-365-linked-service.png" alt-text="Nouveau service lié Office 365":::
 
7. Une fois le service lié créé, vous revenez dans les paramètres du jeu de données. En regard de **Table**, cliquez sur la flèche vers le bas pour développer la liste des jeux de données Office 365 disponibles, puis choisissez « BasicDataSet_v0.Message_v0 » dans la liste déroulante :

    :::image type="content" source="./media/load-office-365-data/edit-dataset.png" alt-text="Configuration de la table du jeu de données Office 365":::

8. Revenez maintenant au **pipeline** > **onglet Source** pour continuer à configurer des propriétés supplémentaires pour l’extraction de données Office 365.  L’étendue de l’utilisateur et le filtre d’étendue utilisateur sont des prédicats facultatifs que vous pouvez définir pour limiter les données que vous souhaitez extraire d’Office 365. Pour plus d’informations sur la configuration de ces paramètres, consultez les [propriétés du jeu de données Office 365](./connector-office-365.md#dataset-properties).

9. Vous devez choisir l’un des filtres de date et fournir les valeurs heure de début et heure de fin.

10. Cliquez sur l’onglet **Importer le schéma** pour importer le schéma du jeu de données de messages.

    :::image type="content" source="./media/load-office-365-data/edit-source-properties.png" alt-text="Configuration du schéma du jeu de données Office 365":::

### <a name="configure-sink"></a>Configurer le récepteur

1. Accédez au pipeline > onglet **Récepteur**, puis sélectionnez **+ Nouveau** pour créer un jeu de données récepteur.
 
2. Dans la fenêtre Nouveau jeu de données, notez que seule les destinations prises en charge sont sélectionnées lors de la copie à partir d’Office 365. Sélectionnez **Stockage Blob Azure**, sélectionnez le format binaire, puis sélectionnez **Continuer**.  Dans ce didacticiel, vous copiez des données Office 365 dans un stockage Blob Azure.

3. Cliquez sur le bouton **Modifier** en regard du jeu de données Stockage Blob Azure pour continuer la configuration des données.

4. Dans l’onglet **Général** de la fenêtre Propriétés, sous Nom, entrez « OutputBlobDataset ».

5. Accédez à l’onglet **Connexion** dans la fenêtre Propriétés. En regard de la zone de texte Service lié, sélectionnez **+ Nouveau**.

6. Dans la fenêtre Nouveau service lié, entrez « AzureStorageLinkedService » comme nom, sélectionnez « Principal de service » dans la liste déroulante des méthodes d’authentification, renseignez le point de terminaison de service, l’abonné, l’ID du principal de service et la clé du principal de service, puis sélectionnez Enregistrer pour déployer le service lié.  Cliquez [ici](connector-azure-blob-storage.md#service-principal-authentication) pour savoir comment configurer l’authentification du principal de service pour le stockage Blob Azure.

    :::image type="content" source="./media/load-office-365-data/configure-blob-linked-service.png" alt-text="Nouveau service lié Blob":::


## <a name="validate-the-pipeline"></a>Valider le pipeline

Sélectionnez **Valider** dans la barre d’outils pour valider les paramètres du pipeline.

Vous pouvez également obtenir le code JSON associé au pipeline en cliquant sur Code dans le coin supérieur droit.

## <a name="publish-the-pipeline"></a>Publier le pipeline

Dans la barre d’outils supérieure, sélectionnez **Publier tout**. Cette action publie les entités (jeux de données et pipelines) que vous avez créées dans Data Factory.

:::image type="content" source="./media/load-office-365-data/publish-changes.png" alt-text="Publier les modifications"::: 

## <a name="trigger-the-pipeline-manually"></a>Déclencher le pipeline manuellement

Sélectionnez **Ajouter déclencheur** dans la barre d’outils, puis **Déclencher maintenant**. Sur la page Exécution du pipeline, sélectionnez **Terminer**. 

## <a name="monitor-the-pipeline"></a>Surveiller le pipeline

Accédez à l’onglet **Surveiller** sur la gauche. Vous voyez un pipeline qui est déclenché par un déclencheur manuel. Vous pouvez utiliser les liens dans la colonne **Actions** pour afficher les détails de l’activité et réexécuter le pipeline.

:::image type="content" source="./media/load-office-365-data/pipeline-status.png" alt-text="Surveillance d’un pipeline"::: 

Pour afficher les exécutions d’activités associées à l’exécution du pipeline, sélectionnez le lien **Afficher les exécutions d’activités** dans la colonne Actions. Dans cet exemple, il n’y a qu’une seule activité, vous ne voyez donc qu’une seule entrée dans la liste. Pour plus de détails sur l’opération de copie, sélectionnez le lien **Détails** (icône en forme de lunettes) dans la colonne Actions.

:::image type="content" source="./media/load-office-365-data/activity-status.png" alt-text="Analyser l’activité"::: 

Si c’est la première fois que vous demandez des données pour ce contexte (une combinaison de la table de données consultée, du compte de destination dans lequel les données sont chargées et de l’identité de l’utilisateur à l’origine de la demande d’accès aux données), l’état de l’activité de copie  sera **En cours d’exécution** et en cliquant uniquement sur le lien « Détails » sous Actions, l’état qui s’affiche est **RequesetingConsent**.  Un membre du groupe d’approbateurs pour l’accès aux données doit approuver la requête dans Privileged Access Management pour permettre l’extraction de données.

_État lors de la demande de consentement :_ 
:::image type="content" source="./media/load-office-365-data/activity-details-request-consent.png" alt-text="Détails de l’exécution de l’activité - Demande de consentement"::: 

_État lors de l’extraction des données :_

:::image type="content" source="./media/load-office-365-data/activity-details-extract-data.png" alt-text="Détails de l’exécution de l’activité - Extraire les données"::: 

Une fois le consentement fourni, l’extraction de données peut continuer et, après un certain temps, l’exécution du pipeline apparaît comme réussie.

:::image type="content" source="./media/load-office-365-data/pipeline-monitoring-succeeded.png" alt-text="Surveiller le pipeline - Succès"::: 

Maintenant, accédez au Stockage Blob Azure de destination et vérifiez que les données Office 365 ont été extraites au format binaire.

## <a name="next-steps"></a>Étapes suivantes

Lisez l’article suivant pour en savoir plus sur la prise en charge d’Azure Synapse Analytics : 

> [!div class="nextstepaction"]
>[Connecteur Office 365](connector-office-365.md)