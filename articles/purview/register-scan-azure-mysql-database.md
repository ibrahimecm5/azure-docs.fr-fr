---
title: Se connecter à une base de données Azure pour MySQL et la gérer
description: Ce guide décrit comment se connecter à Azure Database pour MySQL dans Azure Purview et comment utiliser les fonctionnalités de Purview pour analyser et gérer votre source Azure Database pour MySQL.
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: bcde9aee9719f8dbb127b908c312cc734c559f02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841885"
---
# <a name="connect-to-and-manage-azure-databases-for-mysql-in-azure-purview"></a>Se connecter à des bases de données Azure pour MySQL et les gérer dans Azure Purview

Cet article décrit comment inscrire une base de données Azure pour MySQL, et comment s’authentifier et interagir avec des bases de données Azure pour MySQL dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| [Oui*](#scan) | [Oui](#scan) | [Oui](#scan) | Non | Non** |

\*Purview s’appuie sur les métadonnées UPDATE_TIME de Azure Database pour MySQL pour les analyses incrémentielles. Dans certains cas, ce champ peut ne pas persister dans la base de données et une analyse complète est effectuée. Pour plus d’informations, consultez [Le tableau INFORMATION_SCHEMA TABLES](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html) pour MySQL.

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section décrit comment inscrire une base de données Azure pour MySQL dans Azure Purview en utilisant [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

Suivez les instructions de [CREATE DATABASES AND USERS](../mysql/howto-create-users.md) afin de créer une connexion pour votre base de données Azure pour MySQL.

1. Accédez à votre coffre de clés dans le portail Azure.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** et la **valeur** comme *mot de passe* de votre base de données Azure SQL Database.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) de type authentification SQL en utilisant le **nom d’utilisateur** et le **mot de passe** pour configurer votre analyse.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire une nouvelle base de données Azure pour MySQL dans votre catalogue de données, effectuez les actions suivantes :

1. Accédez à votre compte Purview.

1. Sélectionnez **Data Map** dans le volet de navigation de gauche.

1. Sélectionnez **Inscription**.

1. Sous **Inscrire des sources**, sélectionnez **Azure Database pour MySQL**. Sélectionnez **Continuer**.

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Dans l’écran **Inscrire des sources (Azure Database pour MySQL)** , effectuez les actions suivantes :

1. Entrez un **nom** pour votre source de données. Il s’agit du nom d’affichage de cette source de données dans votre catalogue.
1. Sélectionnez **À partir de l’abonnement Azure**, puis sélectionnez l’abonnement approprié dans la zone de liste déroulante **Abonnement Azure** et le serveur approprié dans la zone de liste déroulante **Nom du serveur**.
1. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Effectuez les étapes ci-dessous afin d’analyser Azure Database pour MySQL pour identifier les ressources et classifier vos données automatiquement. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Azure Database pour MySQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez l’identifiant pour vous connecter à votre source de données et vérifiez votre connexion pour vous assurer que votre identifiant est correctement configuré.

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.-->

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="examiner votre analyse" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
