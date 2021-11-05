---
title: Se connecter à des instances de serveur SQL local et les gérer
description: Ce guide explique comment se connecter à des instances de serveur SQL local dans Azure Purview, et comment utiliser les fonctionnalités de Purview pour analyser et gérer votre source de serveur SQL local.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: d517f3a54963f08a4607e7f95cb5cffbea2486d9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010989"
---
# <a name="connect-to-and-manage-an-on-premises-sql-server-instance-in-azure-purview"></a>Se connecter à une instance de serveur SQL local et la gérer dans Azure Purview

Cet article explique comment inscrire des instances de serveur SQL local, et comment s’authentifier auprès d’une instance de serveur SQL local et interagir avec elle dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | Non| [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

* Configurez le dernier [Runtime d’intégration auto-hébergé](https://www.microsoft.com/download/details.aspx?id=39717). Pour plus d’informations, consultez [le guide Créer et configurer un runtime d’intégration auto-hébergé](../data-factory/create-self-hosted-integration-runtime.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire une instance de serveur SQL local dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Il n’existe qu’une seule façon de configurer l’authentification pour un serveur SQL local :

- Authentification SQL

#### <a name="sql-authentication-to-register"></a>Authentification SQL pour l’inscription

Le compte SQL doit avoir accès à la base de données **MASTER**. Cela est dû au fait que `sys.databases` se trouve dans la base de données MASTER. Le scanneur Purview doit énumérer `sys.databases` pour rechercher toutes les bases de données SQL sur le serveur.

##### <a name="creating-a-new-login-and-user"></a>Création d’une connexion et d’un utilisateur

Si vous souhaitez créer une connexion et un utilisateur pour pouvoir analyser votre serveur SQL, procédez comme suit :

> [!Note]
> Toutes les étapes ci-dessous peuvent être exécutées à l’aide du code fourni [ici](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql).

1. Accédez à SQL Server Management Studio (SSMS), connectez-vous au serveur, accédez à la sécurité, sélectionnez et maintenez l’appui (ou cliquez avec le bouton droit) sur la connexion et créez une connexion. Veillez à sélectionner l’authentification SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Créer une connexion et un utilisateur.":::

1. Sélectionnez Rôles serveur dans le volet de navigation gauche et vérifiez que le rôle public est attribué.

1. Sélectionnez Mappage d’utilisateur dans le volet de navigation de gauche, sélectionnez toutes les bases de données dans le mappage et sélectionnez le rôle de base de données : **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mappage d’utilisateur.":::

1. Sélectionnez OK pour enregistrer.

1. Accédez de nouveau à l’utilisateur que vous avez créé, en sélectionnant et en maintenant l’appui (ou en cliquant avec le bouton droit) et en sélectionnant **Propriétés**. Entrez un nouveau mot de passe et confirmez-le. Sélectionnez « Spécifier l’ancien mot de passe », puis entrez l’ancien mot de passe. **Vous devez modifier votre mot de passe dès que vous créez une connexion.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="modifier un mot de passe.":::

##### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Stockage de votre mot de passe de connexion SQL dans un coffre de clés et création d’informations d’identification dans Purview

1. Accédez à votre coffre de clés dans le portail Azure1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer**, puis entrez le **Nom** et la **Valeur** en tant que *mot de passe* de votre connexion au serveur SQL.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez de nouvelles informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide du **nom d’utilisateur** et du **mot de passe** pour configurer votre analyse.

### <a name="steps-to-register"></a>Procédure d’inscription

1. Accédez à votre compte Purview.

1. Sous sources et analyse dans le volet de navigation de gauche, sélectionnez les **runtimes d’intégration**. Assurez-vous qu’un runtime d’intégration auto-hébergé est configuré. Si ce n’est pas le cas, suivez les étapes mentionnées [ici](manage-integration-runtimes.md) afin de créer un runtime d’intégration auto-hébergé pour les analyses sur une machine virtuelle locale ou Azure qui a accès à votre réseau local.

1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.

1. Sélectionnez **Inscrire**.

1. Sélectionnez **SQL Server**, puis **Continuer**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configurer la source de données SQL.":::

1. Fournissez un nom convivial. Il s’agit d’un nom court que vous pouvez utiliser pour identifier votre serveur et le point de terminaison de serveur.

1. Sélectionnez **Terminer** pour inscrire la source de données.

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser des instances de serveur SQL local afin d’identifier automatiquement des ressources et classifier vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter une analyse

Pour créer une analyse et l’exécuter, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source de serveur SQL que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-set-up-scan.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des tables spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-on-premises-sql-server/on-premises-sql-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-on-premises-sql-server/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
