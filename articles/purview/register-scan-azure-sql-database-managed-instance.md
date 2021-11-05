---
title: Se connecter à une instance managée Azure SQL Database et la gérer
description: Ce guide décrit comment se connecter à Azure SQL Database Managed Instance dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Azure SQL Database Managed Instance.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 9c86bb1d8eea79c7094aacebb891305a1af66115
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048213"
---
# <a name="connect-to-and-manage-an-azure-sql-database-managed-instance-in-azure-purview"></a>Se connecter à une instance managée Azure SQL Database et la gérer dans Azure Purview

Cet article décrit comment inscrire une instance managée Azure SQL Database, et comment s’authentifier et interagir avec elle dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article de présentation](overview.md)

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | Non | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* [Configurer un point de terminaison public dans Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md)

    > [!Note]
    > Nous prenons désormais en charge l’analyse des instances Azure SQL Database Managed Instance qui sont configurées avec des points de terminaison privés à l’aide de points de terminaison privés d’ingestion Azure Purview et d’une machine virtuelle de runtime d’intégration auto-hébergé.
    > Pour plus d’informations sur les prérequis, consultez [Se connecter à Azure Purview et analyser des sources de données de manière privée et sécurisée](./catalog-private-link-end-to-end.md).

## <a name="register"></a>Inscrire

Cette section décrit comment inscrire une instance managée Azure SQL Database dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Si vous avez besoin de créer une nouvelle authentification, vous devez [autoriser l’accès aux bases de données à SQL Database Managed Instance](../azure-sql/database/logins-create-manage.md). Il existe actuellement trois méthodes d’authentification prises en charge par Purview :

- [Identité gérée](#managed-identity-to-register)
- [Principal du service](#service-principal-to-register)
- [Authentification SQL](#sql-authentication-to-register)

#### <a name="managed-identity-to-register"></a>Identité managée pour l’inscription

Votre identité managée Purview est l’identité du compte Purview que nous pouvons utiliser pour l’authentifier comme n’importe quel autre utilisateur, groupe ou principal de service.

Vous pouvez trouver votre ID d’objet d’identité managée dans le portail Azure en suivant ces étapes :

1. Ouvrez le portail Azure et accédez à votre compte Purview.
1. Sélectionnez l’onglet **Propriétés** dans le menu de gauche.
1. Sélectionnez la valeur **ID d’objet d’identité managée** et copiez-la.

L’identité managée a besoin d’une autorisation pour obtenir les métadonnées de la base de données, des schémas et des tables, et pour interroger les tables à des fins de classification.
- Créez un utilisateur Azure AD dans Azure SQL Database Managed Instance en suivant les conditions préalables et le tutoriel [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities).
- Attribuez l’autorisation `db_datareader` à l’identité.

#### <a name="service-principal-to-register"></a>Principal de service pour l’inscription

Plusieurs étapes permettent d’autoriser Purview à utiliser le principal de service pour analyser votre instance managée Azure SQL Database.

#### <a name="create-or-use-an-existing-service-principal"></a>Créer ou utiliser un principal de service existant

Vous pouvez utiliser un principal de service existant ou en créer un nouveau. Si vous voulez utiliser un principal de service existant, passez à l’étape suivante.
Si vous devez créer un principal de service, suivez ces étapes :

 1. Accédez au [portail Azure](https://portal.azure.com).
 1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
 1. Sélectionnez **Inscriptions d’applications**.
 1. Sélectionnez **+ Nouvelle inscription d’application**.
 1. Entrez un nom pour l’**application** (nom du principal de service).
 1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
 1. Pour l’URI de redirection, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ni qu’elle fonctionne.
 1. Sélectionnez ensuite **Inscription**.

#### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurer l’authentification Azure AD dans le compte de base de données

Le principal de service doit avoir une autorisation pour obtenir les métadonnées de la base de données, des schémas et des tables. Ils doivent également être en mesure d’interroger les tables à échantillonner pour la classification.
- [Configurer et gérer l’authentification Azure AD avec Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Créez un utilisateur Azure AD dans Azure SQL Database Managed Instance en suivant les conditions préalables et le tutoriel [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities).
- Attribuez l’autorisation `db_datareader` à l’identité.

#### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Ajouter le principal de service aux informations d’identification de Purview et du coffre de clés

Il est nécessaire d’obtenir l’ID d’application et le secret du principal de service :

1. Accédez à votre principal de service dans le [portail Azure](https://portal.azure.com).
1. Copiez les valeurs **ID d’application (client)** dans **Vue d’ensemble** et **Secret client** dans **Certificats et secrets**.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** de votre choix et la **valeur** comme **secret client** de votre principal de service.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse.

#### <a name="sql-authentication-to-register"></a>Authentification SQL pour l’inscription

> [!Note]
> Seule la connexion principale au niveau du serveur (créée par le processus de configuration) ou les membres du rôle de base de données `loginmanager` dans la base de données master peuvent créer des connexions. Cela nécessite environ **15 minutes** une fois l’autorisation accordée. Le compte Purview doit disposer des autorisations appropriées pour pouvoir analyser la ou les ressources.

Vous pouvez suivre les instructions fournies dans [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) pour créer une connexion pour Azure SQL Database Managed Instance si vous n’en avez pas. Vous aurez besoin d’un **nom d’utilisateur** et d’un **mot de passe** pour les étapes suivantes.

1. Accédez à votre coffre de clés dans le portail Azure.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** et la **valeur** comme *mot de passe* de votre instance Azure SQL Database Managed Instance.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) en utilisant le **nom d’utilisateur** et le **mot de passe** pour configurer votre analyse.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre instance [Purview Studio](https://web.purview.azure.com/resource/)

1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Sélectionnez **Azure SQL Database Managed Instance** puis **Continuer**.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configurer la source de données SQL":::

1. Sélectionnez **À partir de l’abonnement Azure**, puis sélectionnez l’abonnement approprié dans la zone de liste déroulante **Abonnement Azure** et le serveur approprié dans la zone de liste déroulante **Nom du serveur**.

1. Spécifiez le **nom de domaine complet du point de terminaison public** et le **numéro de port**. Ensuite, sélectionnez **Inscrire** pour inscrire la source de données.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Ajouter Azure SQL Database Managed Instance":::

    Par exemple : `foobar.public.123.database.windows.net,3342`

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser une instance managée Azure SQL Database afin d’automatiquement identifier les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de Purview Studio.

1. Sélectionnez la source Azure SQL Database Managed Instance que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
