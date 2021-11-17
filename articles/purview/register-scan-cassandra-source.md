---
title: Se connecter et gérer Cassandra
description: Ce guide explique comment se connecter à Cassandra dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Cassandra.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 80f396e9b0bf53e25b188c42da6be6b8ad7f6061
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851892"
---
# <a name="connect-to-and-manage-cassandra-in-azure-purview-preview"></a>Se connecter à Cassandra et le gérer dans Azure Purview (préversion)

Cet article explique comment inscrire Cassandra et comment s’authentifier et interagir avec Cassandra dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| Non | Non | Non | Non| [Oui](how-to-lineage-cassandra.md)|

> [!Important]
> Les versions de serveur Cassandra prises en charge sont 3 *x* ou 4.*x*.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717).
  Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

* Vérifiez que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) est installé sur la machine virtuelle où est installé le runtime d’intégration auto-hébergé.

* Vérifiez que le package Redistributable Visual C++ pour Visual Studio 2012 Update 4 est installé sur la machine dotée du runtime d’intégration auto-hébergé. Si cette mise à jour n’est pas installée, [vous pouvez la télécharger ici](https://www.microsoft.com/download/details.aspx?id=30679).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire Cassandra dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire un nouveau serveur Cassandra dans votre catalogue de données :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet gauche.
1. Sélectionnez **Inscription**.
1. Dans l’écran **Inscrire des sources**, sélectionnez **Cassandra**, puis **Continuer** :

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Capture d’écran montrant l’écran Inscrire des sources." border="true":::

1. Dans l’écran **Inscrire des sources (Cassandra)**  :

   1. Saisissez un **Nom**. La source de données va utiliser ce nom dans le catalogue.
   1. Dans la zone **Hôte**, entrez l’adresse du serveur sur lequel le serveur Cassandra est en cours d’exécution. Par exemple : 20.190.193.10.
   1. Dans la zone **Port**, entrez le port utilisé par le serveur Cassandra.
   1. Sélectionnez une collection ou créez-en une (facultatif).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Capture d’écran montrant l’écran Inscrire des sources (Cassandra)." border="true":::
   1. Sélectionnez **Inscription**.

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Cassandra pour identifier automatiquement les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer et exécuter une analyse :

1. Dans le centre d’administration, sélectionnez **Runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, effectuez [ces étapes pour configurer un runtime d’intégration auto-hébergé](./manage-integration-runtimes.md).

1. Accédez à **Sources**.

1. Sélectionnez le serveur Cassandra inscrit.

1. Sélectionnez **Nouvelle analyse**.

1. Fournissez les détails ci-après.

    1. **Nom** : Spécifiez un nom pour l’analyse.

    1. **Se connecter via le runtime d’intégration** : Sélectionnez le runtime d’intégration auto-hébergé configuré.

    1. **Informations d’identification** : Quand vous configurez les informations d’identification Cassandra, veillez à respecter ces consignes :

        * Sélectionnez **Authentification de base** comme méthode d’authentification.
        * Dans la zone **Nom d’utilisateur**, indiquez le nom de l’utilisateur pour lequel vous établissez la connexion. 
        * Dans le secret du coffre de clés, enregistrez le mot de passe de l’utilisateur Cassandra pour lequel vous établissez la connexion.

        Pour plus d’informations, consultez [Informations d’identification pour l’authentification des sources dans Purview](manage-credentials.md).

    1. **Espaces de clés** : Spécifiez la liste des espaces de clés Cassandra à importer. Les espaces de clés doivent être séparés par des points-virgules. Par exemple, espace de clés1 ; espace de clés2. Si la liste est vide, tous les espaces de clés disponibles sont importés.

        Vous pouvez utiliser des modèles de nom d’espace de clés qui utilisent une syntaxe d’expression de type SQL, contenant %.

        Par exemple : A%; %B; %C%; D

        Cette expression a la signification suivante :
        * Commence par A ou
        * Se termine par B ou
        * Contient C ou
        * Est égal à D

        Vous ne pouvez pas utiliser NOT ni des caractères spéciaux.

    1. **Utiliser Secure Sockets Layer (SSL)**  : Sélectionnez **True** ou **False** pour indiquer si le protocole SSL doit être utilisé lors de la connexion au serveur Cassandra. Par défaut, cette option a la valeur **False**.

    1. **Mémoire maximale disponible** : Spécifiez la mémoire maximale (en Go) disponible sur la machine virtuelle à utiliser à des fins d’analyse. Cette valeur dépend de la taille du serveur Cassandra à analyser.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="analyser la source cassandra" border="true":::

1. Sélectionnez **Test Connection** (Tester la connexion).

1. Sélectionnez **Continuer**.

1. Sélectionnez un **déclencheur d’analyse**. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

1. Passez en revue votre analyse, puis sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
