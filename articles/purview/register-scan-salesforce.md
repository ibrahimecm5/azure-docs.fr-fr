---
title: Se connecter à Salesforce et le gérer
description: Ce guide décrit comment se connecter à Salesforce dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Salesforce.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 81f4775771c162dce061bc1ad8901bd9b9542d40
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554937"
---
# <a name="connect-to-and-manage-salesforce-in-azure-purview-preview"></a>Se connecter à Salesforce et le gérer dans Azure Purview (préversion)

Cet article explique comment inscrire Salesforce et comment s’authentifier et interagir avec Salesforce dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!IMPORTANT]
> Salesforce en tant que source est actuellement en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| Non|

Lors de l’analyse de Salesforce, Purview prend en charge l’extraction des métadonnées de Salesforce, dont les organisations, les objets, les champs, les clés étrangères et les contraintes uniques.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Vérifiez que l’adresse IP de la machine du runtime d’intégration auto-hébergé figure dans les [plages d’adresses IP de confiance pour votre organisation](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) définies sur Salesforce. Dans le cas contraire, vous devez également fournir le jeton de sécurité pour l’authentification auprès de Salesforce à partir d’un réseau sans relation de confiance. Pour en savoir plus, consultez la configuration des informations d’identification dans la section [Analyse](#scan).

* Si des utilisateurs doivent soumettre des documents Salesforce, certains paramètres de sécurité doivent être configurés pour autoriser cet accès sur Objets standard et les Objets personnalisés. Pour configurer des autorisations :
    - Dans Salesforce, cliquez sur Configurer, puis Gérer les utilisateurs.
    - Dans l’arborescence Gérer les utilisateurs, cliquez sur Profils.
    - Une fois les profils affichés à droite, sélectionnez le profil que vous souhaitez modifier, puis cliquez sur le lien Modifier en regard de celui-ci.
    
    Pour les Objets standard, assurez-vous que les autorisations de Lecture sont sélectionnées dans la section « Documents ». Pour les Objets personnalisés, assurez-vous que les autorisations de Lecture sont sélectionnées pour chaque objet personnalisé.

## <a name="register"></a>S’inscrire

Cette section explique comment inscrire Salesforce dans Azure Purview en utilisant [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire une nouvelle source Salesforce dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview Studio dans [Purview Studio](https://web.purview.azure.com/resource/).
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **Salesforce**. Sélectionnez **Continuer**.

Dans l’écran **Inscrire des sources (Salesforce)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez l’URL du point de terminaison de connexion à Salesforce en tant qu’**URL de domaine**. Par exemple : `https://login.salesforce.com`. Vous pouvez utiliser une URL d’instance de votre société (par exemple, `https://na30.salesforce.com`) ou l’URL de Mon domaine (par exemple, `https://myCompanyName.my.salesforce.com/`).

1. Sélectionnez une collection ou créez-en une (facultatif)

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-salesforce/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Salesforce afin d’identifier les ressources et de classifier vos données automatiquement. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

Azure Purview utilise l’API REST Salesforce version 41.0 pour extraire des métadonnées, dont des requêtes REST telles que ’Describe Global’ URI (/v41.0/sobjects/),’sObject Basic Information’ URI (/v41.0/sobjects/sObject/), and ’SOQL Query’ URI (/v41.0/query?).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour créer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**.

1. Sélectionnez la source Salesforce inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :
        * Sélectionnez **Clé de consommateur** lors de la création d’informations d’identification.
        * Indiquez le nom d’utilisateur de l’utilisateur que l’application connectée imite dans le champ Nom d’utilisateur.
        * Stockez le mot de passe de l’utilisateur que l’application connectée imite dans un secret Azure Key Vault. 
            * Si l’adresse IP de votre machine de runtime d’intégration auto-hébergé figure dans les [plages d’adresses IP de confiance pour votre organisation](https://help.salesforce.com/s/articleView?id=sf.security_networkaccess.htm&type=5) définies sur Salesforce, entrez simplement le mot de passe de l’utilisateur.
            * Sinon, concaténez le mot de passe et le jeton de sécurité en tant que valeur de secrète. Le jeton de sécurité est une clé générée automatiquement qui doit être ajoutée à la fin du mot de passe lors de la connexion à Salesforce à partir d’un réseau sans relation de confiance. Apprenez-en davantage sur la manière d’[obtenir ou réinitialiser un jeton de sécurité](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm).
        * Extrayez la clé du consommateur de la définition d’application connectée. Vous pouvez le trouver dans la page Gérer les applications connectées de l’application connectée ou dans la définition de l’application connectée.
        * Stockez le secret du consommateur extrait de la définition d’application connectée dans un secret Azure Key Vault. Vous pouvez le trouver avec la clé du consommateur.

    1. **Objets** : liste de noms d’objets pour définir l’étendue de votre analyse. Par exemple : `object1; object2`. Une liste vide implique la récupération de tous les objets disponibles. Vous pouvez spécifier des noms d’objets comme un modèle de caractère générique. Par exemple, `topic?`, `*topic*` ou `topic_?,*topic*`.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source Salesforce à analyser.

        > [!Note]
        > En règle générale, prévoyez 1 Go de mémoire pour 1 000 tables

        :::image type="content" source="media/register-scan-salesforce/scan.png" alt-text="Analyser Salesforce" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
