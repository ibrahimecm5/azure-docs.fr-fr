---
title: Se connecter à des pools SQL dédiés (anciennement SQL DW) et les gérer
description: Ce guide explique comment se connecter à des pools de SQL dédiés (anciennement SQL DW) dans Azure Purview, et utiliser les fonctionnalités de Purview pour analyser et gérer votre source de pools de SQL dédiés.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0978293d82de908ca31706fa40ebfdc489fab8fd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848853"
---
# <a name="connect-to-and-manage-dedicated-sql-pools-in-azure-purview"></a>Se connecter à des pools SQL dédiés et les gérer dans Azure Purview

Cet article explique comment inscrire des pools SQL dédiés (anciennement SQL DW), ainsi que s’authentifier et interagir avec des pools SQL dédiés dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

> [!NOTE]
> Si vous cherchez à inscrire et à analyser une base de données SQL dédiée au sein d’un espace de travail Synapse, vous devez suivre les instructions fournies [ici](register-scan-synapse-workspace.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)| [Oui](#scan)| [Oui](#scan)| [Oui](#scan)| Non | Non** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

### <a name="known-limitations"></a>Limitations connues

* Azure Purview prend en charge 300 colonnes au maximum sous l’onglet Schéma. Au-delà, il affiche « Additional-Columns-Truncated » (Colonnes-Supplémentaires-Tronquées).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire des pools SQL dédiés dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Il existe trois façons de configurer l’authentification :

- [Identité managée](#managed-identity-to-register) (recommandé)
- [Principal du service](#service-principal-to-register)
- [Authentification SQL](#sql-authentication-to-register)

    > [!Note]
    > Seule la connexion principale au niveau du serveur (créée par le processus de configuration) ou les membres du rôle de base de données `loginmanager` dans la base de données master peuvent créer des connexions. Cela nécessite environ 15 minutes une fois l’autorisation accordée. Le compte Purview doit disposer des autorisations appropriées pour pouvoir analyser la ou les ressources.

#### <a name="managed-identity-to-register"></a>Identité managée pour l’inscription

Votre compte Purview possède sa propre identité managée, qui est fondamentalement votre nom Purview lorsque vous l’avez créé. Créez un utilisateur Azure AD dans le pool SQL dédié avec le nom d’identité managée exact de Purview en suivant les conditions préalables et le didacticiel [Créer des utilisateurs Azure AD avec des applications Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Exemple de syntaxe SQL pour créer l’utilisateur et accorder l’autorisation :

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

L’authentification doit avoir l’autorisation d’obtenir des métadonnées pour la base de données, les schémas et les tables. Elle doit également être en mesure d’interroger les tables à échantillonner pour la classification. Il est recommandé d’attribuer l’autorisation `db_datareader` à l’identité.

#### <a name="service-principal-to-register"></a>Principal de service pour l’inscription

Si vous souhaitez appliquer l’authentification par principal de service pour les analyses, vous pouvez utiliser un principal de service existant ou en créer un.

Si vous devez créer un principal de service, procédez comme suit :
 1. Accédez au [portail Azure](https://portal.azure.com).
 1. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
 1. 1. Sélectionnez **Inscriptions d’applications**.
 1. Sélectionnez **+ Nouvelle inscription d’application**.
 1. Entrez un nom pour l’**application** (nom du principal de service).
 1. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.
 1. Pour l’URI de redirection, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ni qu’elle fonctionne.
 1. Sélectionnez ensuite **Inscription**.

Il est nécessaire de récupérer l’ID d’application et le secret du principal de service :

1. Accédez à votre principal de service sur le [Portail Azure](https://portal.azure.com).
1. Copiez les valeurs **ID d’application (client)** dans **Vue d’ensemble** et **Secret client** dans **Certificats et secrets**.
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **nom** de votre choix et la **valeur** comme **secret client** de votre principal de service.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du principal de service pour configurer votre analyse.

##### <a name="granting-the-service-principal-access"></a>Accorder l’accès au principal de service

En outre, vous devez créer un utilisateur Azure AD dans le pool dédié en suivant les conditions préalables et le didacticiel sur [Créer des utilisateurs Azure AD avec des applications Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Exemple de syntaxe SQL pour créer l’utilisateur et accorder l’autorisation :

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview a besoin de l’**ID d’application (client)** et du **secret client** pour effectuer l’analyse.

#### <a name="sql-authentication-to-register"></a>Authentification SQL pour l’inscription

Vous pouvez suivre les instructions dans [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) pour créer une connexion pour votre pool SQL dédié (anciennement SQL DW) si vous n’en avez pas encore.

Lorsque la méthode d’authentification sélectionnée est **Authentification SQL**, vous devez vous procurer votre mot de passe et le stocker dans le coffre de clés :

1. Obtenir le mot de passe pour votre connexion SQL
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer** et entrez le **Nom** et la **Valeur** comme *mot de passe* pour votre connexion SQL.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire un nouveau pool SQL dédié dans Purview, procédez comme suit :

1. Accédez à votre compte Purview.
1. Sélectionnez **Data Map** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Dans **Inscrire des sources**, sélectionnez **Pool SQL dédié Azure (anciennement SQL DW)** .
1. Sélectionnez **Continue** (Continuer)

Dans l’écran **Inscrire des sources**, procédez comme suit :

1. Entrez un **nom** avec lequel la source de données sera répertoriée dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer les pools SQL dédiés.
3. Sélectionnez votre pool SQL dédié.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser des pools SQL dédiés afin d’identifier des ressources et de classer vos données automatiquement. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source de pool SQL dédié que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
