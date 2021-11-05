---
title: Se connecter à Looker et le gérer
description: Ce guide décrit comment se connecter à Looker dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Looker.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: a420923ed3d1ef57b8d0d3abdcb688e5412bafd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048156"
---
# <a name="connect-to-and-manage-looker-in-azure-purview"></a>Se connecter à Looker et le gérer dans Azure Purview

Cet article décrit comment inscrire Looker, et comment s’authentifier et interagir avec Looker dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!IMPORTANT]
> La source Looker est actuellement en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-looker.md)|

> [!Important]
> La version du serveur Looker pris en charge est 7.2

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Inscrire

Cette section décrit comment inscrire Looker dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Une clé API3 est requise pour se connecter au serveur Looker. La clé API3 se compose d’un client_id public et d’un client_secret privé et suit un modèle d’authentification OAuth2.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire un nouveau serveur Looker dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **Looker**. Sélectionnez **Continuer.**

:::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="Inscrire la source Looker" border="true":::

Sur l’écran Inscrire des sources (Looker), procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez l’URL de l’API Looker dans le champ **URL de l'API serveur**. Le port par défaut pour les requêtes d’API est le port 19999. En outre, tous les points de terminaison de l’API Looker requièrent une connexion HTTPS. Par exemple : « https://azurepurview.cloud.looker.com »

1. Sélectionnez une collection ou créez-en une (facultatif)

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="Analyser la source Looker" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Looker afin d’automatiquement identifier les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré sur la machine virtuelle sur laquelle s’exécute l’instance erwin Mart. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**.

1. Sélectionnez le serveur **Looker** inscrit.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. L’**URL de l'API serveur** est renseignée automatiquement en fonction de la valeur entrée lors de l’inscription.

    1. **Informations d’identification** : lors de la configuration des informations d’identification Looker, veillez à :

        * Sélectionner **Authentification de base** comme méthode d’authentification
        * Indiquez l’ID client de votre clé API3 dans le champ Nom d’utilisateur
        * Enregistrez la clé secrète client de votre clé API3 dans le coffre de clés.

        Pour accéder à l’ID client et au secret client, accédez à Looker -\>Admin -\> Utilisateurs -\> sélectionnez **Modifier** sur un utilisateur -\> sélectionnez **Modifier les clés** -\> utilisez l’ID client et le secret client, ou créez-en un.

        :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="obtenir des détails sur Looker" border="true":::

        Pour en savoir plus sur les informations d’identification, reportez-vous au lien [ici](manage-credentials.md)

    1. **Filtre de projet** : limitez votre analyse en fournissant une liste de projets Looker séparés par des points-virgules. Cette option permet de sélectionner des apparences et des tableaux de bord par leur projet parent.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille du serveur erwin Mart à analyser.

        :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Déclencher une analyse" border="true":::

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
