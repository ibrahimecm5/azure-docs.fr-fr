---
title: Se connecter au service Azure Data Explorer et le gérer
description: Ce guide explique comment se connecter à Azure Data Explorer dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Azure Data Explorer.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 954fbb8edef1e35993acb9ec7590018719c4627d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848075"
---
# <a name="connect-to-and-manage-azure-data-explorer-in-azure-purview"></a>Se connecter au service Azure Data Explorer et le gérer dans Azure Purview


Cet article explique comment inscrire Azure Data Explorer, et comment s’authentifier auprès d’une instance d’Azure Data Explorer et interagir avec elle dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan)| [Oui](#scan)| Non | Non** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire Azure Data Explorer dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Il n’existe qu’une seule façon de configurer l’authentification pour Azure Data Explorer :

- Principal de service

#### <a name="service-principal-to-register"></a>Principal de service à inscrire

Si vous souhaitez appliquer l’authentification par principal de service pour les analyses, vous pouvez utiliser un principal de service existant ou en créer un.

> [!Note]
> Si vous devez créer un principal de service, procédez comme suit :
> 1. Accédez au [portail Azure](https://portal.azure.com).
> 1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
> 1. Sélectionnez **Inscriptions d’applications**.
> 1. Sélectionnez **+ Nouvelle inscription d’application**.
> 1. Entrez un nom pour l’**application** (nom du principal de service).
> 1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
> 1. Pour l’URI de redirection, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ni qu’elle fonctionne.
> 1. Sélectionnez ensuite **Inscription**.

Il est nécessaire de récupérer l’ID d’application et le secret du principal de service :

1. Accédez à votre principal de service sur le [Portail Azure](https://portal.azure.com).
1. Copiez les valeurs **ID d’application (client)** dans **Vue d’ensemble** et **Secret client** dans **Certificats et secrets**.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** de votre choix et la **valeur** comme **secret client** de votre principal de service.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Octroi au principal du service de l’accès à l’instance Azure Data Explorer

1. Accédez au portail Azure. Ensuite, accédez à votre instance Azure Data Explorer.

1. Ajoutez le principal du service au rôle **AllDatabasesViewer** dans l’onglet **Autorisations**.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire un nouveau compte Azure Data Explorer (Kusto) dans votre catalogue de données, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Data Explorer**.
1. Sélectionnez **Continue** (Continuer)

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Sur l’écran **Inscrire des sources (Azure Data Explorer (Kusto))** , procédez comme suit :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer Azure Data Explorer.
3. Sélectionnez un cluster approprié.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Azure Data Explorer pour identifier automatiquement les ressources et classifier vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Azure Data Explorer que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données. 

   :::image type="content" source="media/register-scan-azure-data-explorer/set-up-scan-data-explorer.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des bases de données spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-data-explorer/scope-your-scan-data-explorer.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-data-explorer/scan-rule-set-data-explorer.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-data-explorer/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
