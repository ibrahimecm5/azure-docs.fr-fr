---
title: Se connecter à plusieurs sources Azure et les gérer
description: Ce guide explique comment se connecter simultanément à plusieurs sources Azure dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer vos sources.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: aefb039e17ee75b92829feb9e2f0b06fb5bef99a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076203"
---
# <a name="connect-to-and-manage-multiple-azure-sources-in-azure-purview"></a>Se connecter à plusieurs sources Azure et les gérer dans Azure Purview

Cet article explique comment inscrire plusieurs sources Azure, s’authentifier auprès d’elles et interagir avec elles dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan)| [Oui](#scan)| Non| [Dépendant de la source](catalog-lineage-user-guide.md)|

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire plusieurs sources Azure dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="prerequisites-for-registration"></a>Prérequis pour l’inscription

Vous devez configurer une authentification pour pouvoir énumérer les ressources associées à un abonnement ou à un groupe de ressources.

1. Accédez à l’abonnement ou au groupe de ressources dans le portail Azure.  
1. Sélectionnez  **Contrôle d’accès (IAM)**   dans le menu de gauche.
1. Sélectionnez **+Ajouter**.
1. Dans la zone **Sélectionner l’entrée**, sélectionnez le rôle **Lecteur** et entrez le nom de votre compte Azure Purview (il s’agit de son fichier MSI). 
1. Sélectionnez **Enregistrer** pour terminer l’attribution de rôle.

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Il existe deux façons de configurer l’authentification pour plusieurs sources dans Azure :

* Identité managée
* Principal du service

Vous devez configurer l’authentification sur chaque ressource dans votre abonnement ou votre groupe de ressources que vous souhaitez inscrire et analyser. Les types de ressources Stockage Azure (Stockage Blob Azure et Azure Data Lake Storage Gen2) vous facilitent la tâche en vous permettant d’ajouter le fichier MSI ou le principal de service au niveau de l’abonnement ou du groupe de ressources en tant que lecteur des données blob du stockage. Les autorisations s’étendent ensuite à chaque compte de stockage de cet abonnement ou groupe de ressources. Pour tous les autres types de ressources, vous devez appliquer le fichier MSI ou le principal du service sur chaque ressource ou créer un script pour ce faire.

Pour savoir comment ajouter des autorisations sur chaque type de ressource au sein d’un abonnement ou groupe de ressources, consultez les ressources suivantes :
    
- [Stockage Blob Azure](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md#authentication-for-a-scan)
- [Azure SQL Managed Instance](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Azure Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Enregistrer**.
1. Sous **Inscrire des sources**, sélectionnez **Azure (Multiple)** .

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Capture d’écran montrant la vignette pour Azure Multiple à l’écran pour l’inscription de plusieurs sources.":::

1. Sélectionnez **Continuer**.
1. Sur l’écran **Inscrire des sources (Azure)** , procédez comme suit :

   1. Dans la zone **Nom**, entrez le nom sous lequel la source de données apparaîtra dans le catalogue. 
   1. Dans la zone **Groupe d’administration**, vous pouvez choisir groupe d’administration sur lequel effectuer le filtrage.
   1. Dans les zones de liste déroulante **Abonnement** et **Groupe de ressources**, sélectionnez un abonnement ou un groupe de ressources spécifique. L’étendue d’inscription sera définie sur l’abonnement ou le groupe de ressources sélectionné.  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Capture d’écran montrant les zones de sélection d’un abonnement et d’un groupe de ressources.":::

   1. Dans la zone **Sélectionner une collection**, sélectionnez une collection ou créez-en une (facultatif).
   1. Sélectionnez **Inscrire** pour inscrire les sources de données.

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser plusieurs sources Azure afin d’identifier automatiquement des ressources et classifier vos données. Pour plus d’informations sur l’analyse en général, consultez notre [présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.
1. Sélectionnez la source de données que vous avez inscrite.
1. Cliquez sur **Afficher les détails** >  **+ Nouvelle analyse** ou utilisez l’icône d’action rapide d’**analyse** sur la vignette source.
1. Pour **Nom**, entrez le nom.
1. Pour **Type**, sélectionnez les types de ressources que vous souhaitez analyser au sein de cette source. Choisissez une de ces options :

    - Conserver **Tout**. Cette sélection inclut les futurs types de ressources qui n’existent pas encore dans cet abonnement ou ce groupe de ressources.
    - Utilisez les zones pour sélectionner spécifiquement les types de ressources que vous souhaitez analyser. Si vous choisissez cette option, les types de ressources qui pourront être créés à l’avenir dans cet abonnement ou ce groupe de ressources ne seront pas inclus pour les analyses, sauf si l’analyse est explicitement modifiée par la suite.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Capture d’écran montrant les options d’analyse de plusieurs sources.":::

1. Sélectionnez les informations d’identification pour vous connecter aux ressources dans votre source de données.
    - Vous pouvez sélectionner des informations d’identification au niveau parent comme un fichier MSI ou sélectionner des informations d’identification pour un type de principal de service spécifique. Vous pouvez ensuite utiliser ces informations d’identification pour tous les types de ressources de l’abonnement ou du groupe de ressources.
    - Vous pouvez sélectionner spécifiquement le type de ressource et appliquer d’autres informations d’identification pour ce type de ressource.

    Ces informations d’identification seront considérées comme la méthode d’authentification pour toutes les ressources d’un type spécifique. Vous devez définir les informations d’identification choisies sur les ressources afin de les analyser correctement comme décrit [précédemment dans cet article](#authentication-for-registration).
1. Dans chaque type, vous pouvez choisir d’analyser toutes les ressources ou un sous-ensemble par nom :
    - Si vous conservez la valeur **Tout** pour l’option, les ressources futures de ce type seront également analysées lors des exécutions d’analyse ultérieures.
    - Si vous sélectionnez des comptes de stockage ou des bases de données SQL spécifiques, les ressources futures de ce type créées dans cet abonnement ou ce groupe de ressources ne seront pas incluses dans les analyses, sauf si l’analyse est explicitement modifiée par la suite.

1. Sélectionnez **Tester la connexion**. Cette opération teste l’accès pour vérifier si vous avez appliqué le fichier MSI Azure Purview en tant que lecteur sur l’abonnement ou le groupe de ressources. Si vous recevez un message d’erreur, suivez [ces instructions](#prerequisites-for-registration) pour y remédier. Elle teste ensuite votre authentification et votre connexion à chacune de vos sources sélectionnées, puis génère un rapport. Le nombre de sources sélectionnées a un impact sur le temps nécessaire à la génération de ce rapport. L’option Tester la connexion teste d’abord la connectivité et l’accès au niveau de l’abonnement/du groupe de ressources/de l’espace de travail Synapse. Elle continue ensuite en testant l’accès et la connectivité à chaque ressource individuelle, puis affiche le résultat dans le rapport. En cas d’échec sur certaines ressources, si vous pointez sur l’icône **X**, le message d’erreur détaillé s’affiche.

    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection.png" alt-text="Capture d’écran montrant le curseur de configuration de l’analyse, avec le bouton Tester la connexion mis en évidence.":::
    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection-report.png" alt-text="Capture d’écran montrant un exemple de rapport de test de connexion, avec des connexions qui réussissent et d’autres qui échouent. Si vous pointez sur l’une des connexions ayant échoué, un rapport détaillé de l’erreur s’affiche.":::

1. Une fois que le test de la connexion a réussi, sélectionnez **Continuer** pour continuer.

1. Sélectionnez les ensembles de règles d’analyse pour chaque type de ressource choisi à l’étape précédente. Vous pouvez également créer des ensembles de règles d’analyse inline.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Capture d’écran montrant des règles d’analyse pour chaque type de ressource.":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez planifier une exécution hebdomadaire, mensuelle ou unique.

1. Vérifiez votre analyse et sélectionnez **Enregistrer** pour terminer la configuration.

## <a name="view-your-scans-and-scan-runs"></a>Afficher vos analyses et exécutions d’analyse

1. Pour afficher les détails de la source, sélectionnez **Afficher les détails** dans la vignette située sous la section **Data Map**.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Capture d’écran montrant les détails de la source."::: 

1. Pour afficher les détails de l’exécution de l’analyse, accédez à la page **Détails de l’analyse**.
   
    La *barre d’état* présente un bref résumé de l’état d’exécution des ressources enfants. Elle s’affiche au niveau de l’abonnement ou du groupe de ressources. Voici la signification des différentes couleurs :
    
    - Vert : l’analyse a réussi.
    - Rouge : l’analyse a échoué. 
    - Gris : l’analyse est toujours en cours.
   
    Vous pouvez sélectionner chaque analyse pour afficher des détails plus précis.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Capture d’écran montrant les détails de l’analyse.":::

1. Affichez un résumé des exécutions d’analyse récentes qui ont échoué en bas des détails de la source. Vous pouvez également afficher des détails plus précis sur ces exécutions.

## <a name="manage-your-scans-edit-delete-or-cancel"></a>Gérer vos analyses : modifier, supprimer ou annuler

Pour gérer une analyse, procédez comme suit :

1. Accédez au centre de gestion.
1. Sélectionnez **Sources de données** sous la section **Sources et analyse**, puis sélectionnez la source de données souhaitée
1. Sélectionnez l’analyse que vous souhaitez gérer. Ensuite : 

   - Vous pouvez modifier l’analyse en sélectionnant **Modifier**.
   - Vous pouvez supprimer l’analyse en sélectionnant **Supprimer**.
   - Si l’analyse est en cours d’exécution, vous pouvez l’annuler en sélectionnant **Annuler**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
