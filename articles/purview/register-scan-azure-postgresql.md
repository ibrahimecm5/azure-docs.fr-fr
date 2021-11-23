---
title: Se connecter à une base de données Azure pour PostgreSQL et la gérer
description: Ce guide décrit comment se connecter à un serveur unique Azure Database pour PostgreSQL dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Azure Database pour PostgreSQL.
author: evangelinew
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 46e2f60adc895b60370ed7109305bb8966057fa3
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402387"
---
# <a name="connect-to-and-manage-an-azure-database-for-postgresql-in-azure-purview"></a>Se connecter à une base de données Azure pour PostgreSQL et la gérer dans Azure Purview

Cet article explique comment inscrire une base de données Azure pour PostgreSQL déployée avec l’option de déploiement monoserveur, et comment s’authentifier et interagir avec elle dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | Non | Non** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

> [!Important]
>  Purview prend uniquement en charge l’option de déploiement monoserveur pour une base de données Azure pour PostgreSQL. Versions 8.x à 12.x

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section décrit comment inscrire une base de données Azure pour PostgreSQL dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Pour gérer un serveur unique Azure Database pour PostgreSQL et interagir avec lui, seule l’authentification SQL est prise en charge.

#### <a name="sql-authentication"></a>Authentification SQL

La connexion d’une base de données Azure Database pour PostgreSQL nécessite un nom de serveur complet et des informations d’identification de connexion. Vous pouvez suivre les instructions fournies dans [CONNECTER ET INTERROGER](../postgresql/connect-python.md) pour créer une connexion pour une base de données Azure pour PostgreSQL si vous n’en avez pas. Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

1. Si vous n’avez pas de coffre de clés Azure, suivez [ce guide pour créer un coffre de clés Azure](../key-vault/certificates/quick-create-portal.md).
1. Accédez à votre coffre de clés dans le portail Azure.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** et la **valeur** comme *mot de passe* de votre base de données Azure PostgreSQL
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) de type authentification SQL en utilisant le **nom d’utilisateur** et le **mot de passe** pour configurer votre analyse

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire une nouvelle base de données Azure pour PostgreSQL dans votre catalogue de données, effectuez les actions suivantes :

1. Accédez à votre compte Purview.

1. Sélectionnez **Data Map** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Sous **Inscrire des sources**, sélectionnez **Azure Database pour PostgreSQL**. Sélectionnez **Continuer**.

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Dans l’écran **Inscrire des sources de base de données Azure pour PostgreSQL**, procédez comme suit :

1. Entrez un **nom** pour votre source de données. Il s’agit du nom d’affichage de cette source de données dans votre catalogue.
1. Sélectionnez **À partir de l’abonnement Azure**, puis sélectionnez l’abonnement approprié dans la zone de liste déroulante **Abonnement Azure** et le serveur approprié dans la zone de liste déroulante **Nom du serveur**.
1. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser une base de données Azure Database pour PostgreSQL afin d’automatiquement identifier les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Azure Database pour PostgreSQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="Déclencher une analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
