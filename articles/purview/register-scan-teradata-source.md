---
title: Se connecter Teradata et le gérer
description: Ce guide explique comment se connecter à Teradata dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Teradata.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ebaad16ff413b33f175815a1ddadb2fa1d5b63ae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841920"
---
# <a name="connect-to-and-manage-teradata-in-azure-purview"></a>Connecter à Teradata et le gérer dans Azure Purview

Cet article explique comment inscrire Teradata, ainsi que comment s’authentifier et interagir avec Teradata dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui**](how-to-lineage-teradata.md)|

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

Les versions de base de données Teradata prises en charge vont de 12.x à 16.x.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Vous devez télécharger manuellement le pilote JDBC de Teradata sur la machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Le fichier JAR exécutable peut être téléchargé à partir du [site web de Teradata](https://downloads.teradata.com/).

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne procédez pas à l’installation dans un compte d’utilisateur.

## <a name="register"></a>S’inscrire

Cette section explique comment inscrire Teradata dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

La seule authentification prise en charge pour une source Teradata est l’**Authentification de base**. Assurez-vous d’avoir un accès en lecture à la source Teradata qui est analysée.

### <a name="steps-to-register"></a>Procédure d’inscription

1.  Accédez à votre compte Purview.
1.  Sélectionnez **Data Map** dans le volet de navigation de gauche.
1.  Sélectionnez **Inscrire**.
1.  Dans Inscrire des sources, sélectionnez **Teradata**. Sélectionnez **Continue** (Continuer)

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Options d’inscription de Teradata" border="true":::

Dans l’écran **Inscrire des sources (Teradata)** , effectuez les actions suivantes :

1.  Entrez le **Nom** avec lequel la source de données sera listée dans le catalogue.

1.  Entrez le nom d’**hôte** pour la connexion à une source Teradata. Il peut également s’agir d’une adresse IP ou d’une chaîne de connexion complète au serveur.

1.  Sélectionnez une collection ou créez-en une (facultatif)

1.  Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Inscrire Teradata" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Teradata afin d’identifier les ressources et de classer vos données automatiquement. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

1. Dans le centre d’administration, sélectionnez **Runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé.

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Teradata inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :
        * Sélectionnez Authentification de base quand vous créez des informations d’identification.
        * Indiquez un nom d’utilisateur pour la connexion au serveur de base de données dans le champ d’entrée Nom d’utilisateur
        * Stockez le mot de passe du serveur de base de données dans la clé secrète.

        Pour en savoir plus sur les informations d’identification, reportez-vous au lien [ici](./manage-credentials.md)

    1. **Schéma** : répertorie le sous-ensemble de schémas à importer, exprimé sous la forme d’une liste séparée par des points-virgules. Par exemple : `schema1; schema2`. Tous les schémas utilisateur sont importés si cette liste est vide. Tous les schémas système (par exemple, SysAdmin) et les objets sont ignorés par défaut. Si la liste est vide, tous les schémas disponibles sont importés.

        Les modèles de nom de schéma acceptables utilisant la syntaxe d’expressions de type SQL LIKE incluent l’utilisation de %. Par exemple : `A%; %B; %C%; D`
        * commençant par A ou
        * se terminant par B ou
        * contenant C ou
        * égalant D

        L’utilisation de NOT et des caractères spéciaux n’est pas autorisée.

    1. **Emplacement du pilote** : spécifiez le chemin d’accès à l’emplacement du pilote JDBC dans votre machine virtuelle où s’exécute le runtime d’intégration auto-hébergé. Il doit s’agir du chemin vers l’emplacement du dossier JAR valide.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source Teradata à analyser.

        > [!Note]
        > En règle générale, prévoyez 2 Go de mémoire pour 1 000 tables.

        :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configuration de l’analyse" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
