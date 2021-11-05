---
title: Se connecter aux serveurs erwin Mart et les gérer
description: Ce guide explique comment se connecter aux serveurs erwin Mart dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source de serveur erwin Mart.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0a6aef0cdbf55772ada02bef7090ccc3165b5658
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472444"
---
# <a name="connect-to-and-manage-erwin-mart-servers-in-azure-purview-preview"></a>Se connecter aux serveurs erwin Mart et les gérer dans Azure Purview (préversion)

Cet article explique comment inscrire des serveurs erwin Mart et comment s’authentifier et interagir avec ces derniers dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!IMPORTANT]
> Le serveur erwin Mart en tant que source est en préversion. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-erwin.md)|

La source erwin prend en charge l’analyse complète pour extraire les métadonnées d’un serveur erwin Mart. Les métadonnées incluent :

1. Les modèles logiques uniquement avec Entités, Attributs et Domaines OU
1. Les modèles physiques uniquement avec Tables, Colonnes, Types de données OU
1. Les modèles logiques ou physiques

> [!Important]
> Les versions d’erwin Mart prises en charge sont 9.x à 2021.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Veillez à exécuter le runtime d’intégration auto-hébergé sur la machine virtuelle sur laquelle s’exécute l’instance erwin Mart.

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire des serveurs erwin Mart dans Azure Purview en utilisant [Purview Studio](https://web.purview.azure.com/).

La seule authentification prise en charge pour une source erwin Mart est l’**authentification du serveur** sous la forme d’un nom d’utilisateur et d’un mot de passe.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Purview Studio dans [Purview Studio](https://web.purview.azure.com/).
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **erwin**. Sélectionnez **Continuer.**
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="register erwin source" border="true":::

Sur l’écran Inscrire des sources (erwin), procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
1. Entrez le **nom du serveur** erwin Mart. Il s’agit du nom d’hôte réseau utilisé pour se connecter au serveur erwin Mart. Par exemple, localhost.
1. Entrez le numéro de **port** utilisé lors de la connexion à erwin Mart. Par défaut, cette valeur est définie sur 18170.
1. Entrez le **nom de l’application**.

    >[!Note]
    > Vous pouvez trouver les détails ci-dessus en accédant jusqu’à votre modèle de données erwin. Sélectionnez Mart -\> Connexion pour afficher les détails relatifs au nom du serveur, au port et au nom de l’application.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Rechercher des détails erwin" border="true":::

1. Sélectionnez une collection ou créez-en une (facultatif).

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Inscrire la source" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser les serveurs erwin Mart afin d’identifier automatiquement les ressources et de classifier vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré sur la machine virtuelle sur laquelle s’exécute l’instance erwin Mart. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour configurer un runtime d’intégration auto-hébergé.
1. Accédez aux **Sources**.

1. Sélectionnez le serveur **erwin Mart** inscrit.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : Sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. **Nom du serveur, Port** et **Nom de l’application** sont renseignés automatiquement en fonction des valeurs entrées lors de l’inscription.

    1. **Informations d’identification :** sélectionnez les informations d’identification configurées pour vous connecter à votre serveur erwin Mart. Lors de la création d’informations d’identification, veillez à :
        * Sélectionner **Authentification de base** comme méthode d’authentification.
        * Indiquer le nom d’utilisateur du serveur erwin Mart dans le champ Nom d’utilisateur.
        * Enregistrer votre mot de passe utilisateur pour l’authentification du serveur dans le secret du coffre de clés.

        Pour plus d’informations sur les informations d’identification, reportez-vous à [ce lien](manage-credentials.md).

    1. **Utiliser Internet Information Services (IIS)**  : sélectionnez True ou False pour indiquer si Microsoft Internet Information Services (IIS) doit être utilisé lors de la connexion au serveur erwin Mart. La valeur par défaut est False.

    1. **Utiliser le protocole SSL** : Sélectionnez True ou False pour indiquer si le protocole SSL doit être utilisé lors de la connexion au serveur erwin Mart. La valeur par défaut est False.

        > [!Note]
        > Ce paramètre s’applique uniquement à erwin Mart version 9.1 ou ultérieure.

    1. **Mode de navigation** : sélectionnez le mode de navigation d’erwin Mart. Les options possibles sont « Bibliothèques et modèles » ou « Bibliothèques uniquement ».

    1. **Modèles** : étendez votre analyse en fournissant une liste de chaînes de localisateur de modèles erwin séparées par des points-virgules. Par exemple, mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical.

    1. **Mémoire maximale disponible** : mémoire maximale (en Go) disponible sur la machine virtuelle du client pouvant être utilisée par les processus d’analyse. Elle dépend de la taille du serveur erwin Mart à analyser.

    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Déclencher une analyse" border="true":::

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
