---
title: Connexion à Google BigQuery et gestion de projets
description: Ce guide explique comment se connecter aux projets Google BigQuery dans Azure Purview et comment utiliser les fonctionnalités de Purview pour analyser et gérer votre source Google BigQuery.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: c300e315571c90fd2aa1892336b6ad40d5593ed9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433438"
---
# <a name="connect-to-and-manage-google-bigquery-projects-in-azure-purview"></a>Connexion à Google BigQuery et gestion de projets dans Azure Purview

Cet article décrit comment inscrire des projets Google BigQuery et comment s’authentifier et interagir avec Google BigQuery dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!IMPORTANT]
> Google BigQuery en tant que source est actuellement en préversion. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-google-bigquery.md)|

> [!Important]
> La version de Google BigQuery prise en charge est 11.0.0.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Téléchargez et installez le pilote JDBC de BigQuery sur la machine sur laquelle le runtime d’intégration auto-hébergé est en cours d’exécution. Le pilote est disponible [ici](https://cloud.google.com/bigquery/providers/simba-drivers).

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

## <a name="register"></a>S’inscrire

Cette section explique comment inscrire un projet Google BigQuery dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **Google BigQuery**. Sélectionnez **Continuer.**

    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="inscrire la source BigQuery" border="true":::

Sur l’écran Inscrire des sources (Google BigQuery), procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez le **ProjectID.** Il doit s’agir d’un ID de projet complet. Par exemple mydomain.com:myProject

1. Sélectionnez une collection ou créez-en une (facultatif).

1. Sélectionnez **Inscription**.

    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Configurer la source BigQuery" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser un projet Google BigQuery pour identifier automatiquement les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. S’il n’est pas configuré, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md).

1. Accédez aux **Sources**.

1. Sélectionnez le projet **BigQuery** inscrit.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    1. **Informations d’identification** : lors de la configuration des informations d’identification BigQuery, veillez à :

        * Sélectionner **Authentification de base** comme méthode d’authentification
        * Indiquer l’ID de messagerie du compte de service dans le champ nom d’utilisateur. Par exemple, xyz\@developer.gserviceaccount.com
        * Enregistrer un fichier de clé privée du compte de service au format JSON dans le coffre de clés

        Pour créer une nouvelle clé privée à partir de la plateforme cloud de Google :
        1. Dans le menu de navigation, sous les options IAM & admin, \>Comptes de service\>, sélectionnez un projet - \> 
        1. Sélectionnez l’adresse e-mail du compte de service pour lequel vous souhaitez créer une clé.
        1. Sélectionnez l’onglet **Clés**.
        1. Sélectionnez le menu déroulant **Ajouter une clé**, puis sélectionnez Créer une nouvelle clé. 
        1. Choisissez le format JSON.

          > [!Note]
          > Le contenu de la clé privée est enregistré dans un fichier temporaire sur la machine virtuelle lorsque les processus d’analyse sont en cours d’exécution. Ce fichier temporaire est supprimé une fois que les analyses sont terminées avec succès. En cas d’échec de l’analyse, le système continue à réessayer jusqu’à ce qu’il réussisse. Assurez-vous que l’accès est correctement limité sur la machine virtuelle où SHIR est en cours d’exécution.

        Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

    1. **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide. 

        > [!Note]
        > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

    1. **Jeu de données** : spécifiez une liste de jeux de données BigQuery à importer.
        Par exemple, dataset1 ; dataset2. Si la liste est vide, tous les jeux de données disponibles sont importés.
        Les modèles de nom de jeu de données acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %.

        Par exemple : A%; %B; %C%; D
        * commençant par A ou
        * se terminant par B ou
        * contenant C ou
        * égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur votre machine virtuelle pouvant être utilisée par les processus d’analyse. Cela dépend de la taille du serveur Google BigQuery à analyser.

        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Analyser la source BigQuery" border="true":::

1. Sélectionnez **Test Connection** (Tester la connexion).

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
