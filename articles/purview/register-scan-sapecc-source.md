---
title: Se connecter à une source SAP ECC et la gérer
description: Ce guide décrit comment se connecter à SAP ECC dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source SAP ECC.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ce62eebd42812c4706e5fb7848a0a7bd4f6988ed
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472425"
---
# <a name="connect-to-and-manage-sap-ecc-in-azure-purview"></a>Se connecter à SAP ECC et le gérer dans Azure Purview

Cet article explique comment inscrire SAP ECC et comment s’authentifier et interagir avec SAP ECC dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register)| [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-sapecc.md)|

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

* Le connecteur lit les métadonnées à partir de SAP à l’aide de l’API [SAP Java Connector (JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0. Assurez-vous que Java Connector est disponible sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé. Assurez-vous que vous utilisez la distribution JCo correcte pour votre environnement. Par exemple : sur une machine Microsoft Windows, assurez-vous que les fichiers sapjco3.jar et sapjco3.dll sont disponibles.

    > [!Note]
    > Le pilote doit être accessible à tous les comptes de la machine virtuelle. Ne l’installez pas dans un compte d’utilisateur.

* Déployez le module de fonction ABAP d’extraction des métadonnées sur le serveur SAP en suivant les étapes mentionnées dans le [Guide de déploiement des fonctions ABAP](abap-functions-deployment-guide.md). Vous aurez besoin d’un compte de développeur ABAP pour créer le module de fonction RFC sur le serveur SAP. Le compte d’utilisateur requiert des autorisations suffisantes pour se connecter au serveur SAP et exécuter les modules de fonction RFC suivants :
  * STFC_CONNECTION (vérifier la connectivité)
  * RFC_SYSTEM_INFO (vérifier les informations système)

## <a name="register"></a>S’inscrire

Cette section explique comment inscrire SAP ECC dans Azure Purview en utilisant [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

La seule authentification prise en charge pour une source SAP ECC est l’**Authentification de base**.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans Inscrire des sources, sélectionnez **SAP ECC**. Sélectionnez **Continuer.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="Options d’inscription de SAP ECC" border="true":::

Dans l’écran **Inscrire des sources (SAP ECC)** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.

1. Entrez le nom du **Serveur d’applications** pour vous connecter à la source SAP ECC. Il peut également s’agir d’une adresse IP de l’hôte du serveur d’applications SAP.

1. Entrez le **Numéro du système** SAP. Il s’agit d’un entier à deux chiffres compris entre 00 et 99.

1. Sélectionnez une collection ou créez-en une (facultatif)

1. Terminez pour inscrire la source de données.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="Inscrire SAP ECC" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser SAP ECC afin d’identifier automatiquement les ressources et de classifier vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

1. Dans le centre d’administration, sélectionnez Runtimes d’intégration. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](./manage-integration-runtimes.md) pour créer un runtime d’intégration auto-hébergé.

1. Accédez aux **Sources**

1. Sélectionnez la source SAP ECC inscrite.

1. Sélectionnez **+ Nouvelle analyse**.

1. Fournissez les renseignements ci-dessous :

    1. **Nom** : nom de l’analyse

    1. **Se connecter via le runtime d’intégration** : sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. **Informations d’identification** : sélectionnez les informations d’identification pour vous connecter à votre source de données. Veillez à respecter les points suivants :

        * Sélectionnez Authentification de base quand vous créez des informations d’identification.
        * Indiquez un identifiant d’utilisateur pour la connexion au serveur SAP dans le champ d’entrée Nom d’utilisateur.
        * Stockez le mot de passe utilisateur utilisé pour se connecter au serveur SAP dans la clé secrète.

    1. **ID client** : entrez l’identifiant client SAP. Il s’agit d’un nombre à trois chiffres compris entre 000 et 999.

    1. **Chemin de la bibliothèque JCo** : chemin vers le répertoire contenant les bibliothèques JCo.

    1. **Mémoire maximale disponible** : Mémoire maximale (en Go) disponible sur la machine du runtime d’intégration auto-hébergé pouvant être utilisée par les processus d’analyse. Elle dépend de la taille de la source SAP ECC à analyser. Il est recommandé de fournir une grande quantité de mémoire disponible, par exemple, 100.

        :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="Analyser SAP ECC" border="true":::

1. Sélectionnez **Continuer**.

1. Choisissez votre **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
