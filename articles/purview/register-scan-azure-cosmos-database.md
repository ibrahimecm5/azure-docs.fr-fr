---
title: Inscription et analyse d’Azure Cosmos DB (API SQL)
description: Cet article décrit le processus d’inscription d’une source de données Azure Cosmos (API SQL) dans Azure Purview, y compris des instructions pour l’authentification et l’interaction avec la base de données Azure Cosmos
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ba4c12c51ad744e6c68d63c1aaad17f32fbec15e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841977"
---
# <a name="connect-to-azure-cosmos-database-sql-api-in-azure-purview"></a>Se connecter à la base de données Azure Cosmos (API SQL) dans Azure Purview

Cet article décrit le processus d’inscription d’une base de données Azure Cosmos (API SQL) dans Azure Purview, y compris des instructions pour l’authentification et l’interaction avec la source de base de données Azure Cosmos

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)|[Oui](#scan) | [Oui](#scan)|[Oui](#scan)|Non|Non** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section vous permet d’inscrire la base de données Azure Cosmos (API SQL) et de configurer un mécanisme d’authentification approprié pour garantir la réussite de l’analyse de la source de données.

### <a name="steps-to-register"></a>Procédure d’inscription

Il est important d’inscrire la source de données dans Azure Purview avant de configurer une analyse de la source de données.

1. Allez sur le [portail Azure](https://portal.azure.com) et accédez à la page des **comptes Purview** et sélectionnez votre _compte Azure Purview_

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-purview-acct.png" alt-text="Capture d’écran montrant le compte Purview utilisé pour enregistrer la source de données":::

1. **Ouvrez Purview Studio** et accédez au **Mappage de données --> Collections**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-open-purview-studio.png" alt-text="Capture d’écran qui navigue jusqu’au lien sources dans le mappage de données":::

1. Créez la [hiérarchie de collection](./quickstart-create-collection.md) à l’aide du menu **Collections** et affecter des autorisations à des sous-groupes individuels, selon les besoins

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collections.png" alt-text="Capture d’écran montrant le menu de collection pour créer une hiérarchie de collections":::

1. Accédez à la collection appropriée dans le menu **Sources** et sélectionnez l’icône de **Inscrire** pour inscrire une nouvelle base de données Azure Cosmos

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-register-data-source.png" alt-text="Capture d’écran montrant la collection utilisée pour enregistrer la source de données":::

1. Sélectionnez la source de données **Azure Cosmos DB (SQL API)** et sélectionnez **Continuer**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-data-source.png" alt-text="Capture d’écran qui permet de sélectionner la source de données":::

1. Fournissez un **Nom** approprié pour la source de données, sélectionnez l'**Abonnement Azure** approprié, le **nom du compte Cosmos DB** et la **collection**, puis sélectionnez **Appliquer**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-data-source-details.png" alt-text="Capture d’écran montrant les détails à entrer pour inscrire la source de données":::

1. Le compte de stockage de la _base de données Azure Cosmos_ s’affiche sous la collection sélectionnée

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collection-mapping.png" alt-text="Capture d’écran montrant la source de données mappée à la collection pour lancer l’analyse":::

## <a name="scan"></a>Analyser

### <a name="authentication-for-a-scan"></a>Authentification pour une analyse

Pour pouvoir analyser la source de données, vous devez configurer une méthode d’authentification dans le compte de stockage de la base de données Azure Cosmos.

Il n’existe qu’une seule façon de configurer l’authentification pour Azure Cosmos Database :

**Clé de compte** : les secrets peuvent être créés à l’intérieur d’un Azure Key Vault pour stocker les informations d’identification afin d’autoriser l’accès à Azure Purview pour analyser les sources de données en toute sécurité à l’aide des secrets. Un secret peut être une clé de compte de stockage, un mot de passe de connexion SQL ou un mot de passe.

> [!Note]
> Vous devez déployer une ressource _coffre de clés Azure_ dans votre abonnement et attribuer un MSI _de compte Azure Purview_ avec l’autorisation d’accès requise aux secrets dans le _coffre de clés Azure_.

#### <a name="using-account-key-for-scanning"></a>Utilisation de la clé de compte pour l’analyse

Vous devez disposer de votre clé d’accès et de votre magasin dans le coffre de clés :

1. Accédez à votre compte de stockage de base de données Azure Cosmos
1. Sélectionnez **Paramètres > Clés**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-access-keys.png" alt-text="Capture d’écran montrant les clés d’accès dans le compte de stockage":::

1. Copiez votre *clé* et enregistrez-la quelque part pour la suite

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key.png" alt-text="Capture d’écran montrant la clé d’accès à copier":::

1. Accédez à votre coffre de clés.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault.png" alt-text="Capture d’écran montrant le coffre de clés":::

1. Sélectionnez **Paramètres > Secrets**, puis **+ Générer/Importer**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-generate-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour générer une clé secrète":::

1. Entrez le **Nom** et la **Valeur** comme *Clé* de votre compte de stockage, puis sélectionnez **Créer** pour terminer

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault-options.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour entrer les valeurs de secret":::

1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

### <a name="creating-the-scan"></a>Création de l’analyse

1. Ouvrez votre **compte Purview** et sélectionnez **Ouvrir Purview Studio**
1. Accéder aux **sources** -->  **de mappage de données** pour afficher la hiérarchie de collection
1. Sélectionnez l’icône **Nouvelle analyse** sous la **Base de données Azure Cosmos** inscrite précédemment

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-create-scan.png" alt-text="Capture d’écran montrant l’écran de création d’une nouvelle analyse":::

1. Donnez un **Nom** à l’analyse, choisissez la collection appropriée pour l’analyse et sélectionnez **+ Nouveau** sous **Informations d’identification**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-option.png" alt-text="Capture d’écran montrant l’option de clé de compte pour l’analyse":::

1. Sélectionnez la **Connexion de coffre de clés** appropriée et le **nom du secret** qui a été utilisé lors de la création de la _Clé de compte_. Sélectionner la **méthode d’authentification** comme _Clé de compte_

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-details.png" alt-text="Capture d’écran montrant les options de clé de compte":::

1. Sélectionnez **Tester la connexion**. Sur une connexion réussie, sélectionnez **Continuer**

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-test-connection.png" alt-text="Capture d’écran montrant la réussite de Tester la connexion":::

### <a name="scoping-and-running-the-scan"></a>Étendue et exécution de l’analyse

1. Vous pouvez limiter votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-new-scan-rule-set.png" alt-text="Nouvelle règle d’analyse":::

1. Vous pouvez sélectionner les **règles de classification** à inclure dans la règle d’analyse

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-classification.png" alt-text="Règles de classification de l’ensemble de règles d’analyse":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-scan-rule-set.png" alt-text="Sélection de l’ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-trigger.png" alt-text="déclencheur d’analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-review-scan.png" alt-text="Vérifier l’analyse":::

### <a name="viewing-scan"></a>Affichage de l’analyse

1. Accédez à la _source de données_ dans la _Collection_ et sélectionnez **Afficher les détails** pour vérifier l’état de l’analyse

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-view-scan.png" alt-text="Afficher l’analyse":::

1. Les détails de l’analyse indiquent la progression de l’analyse dans l’**état de la dernière exécution** et le nombre de ressources _analysées_ et _classées_

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-last-run-status.png" alt-text="Afficher les détails de l’analyse":::

1. L'**État de la dernière exécution** sera mis à jour sur **En cours** , puis **Terminé** une fois l’analyse complète exécutée avec succès

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-in-progress.png" alt-text="afficher l’analyse en cours":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-completed.png" alt-text="afficher l’analyse terminée":::

### <a name="managing-scan"></a>Gestion de l’analyse

Les analyses peuvent être gérées ou réexécutées à la fin.

1. Sélectionner le **Nom de l’analyse** pour gérer l’analyse

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan.png" alt-text="Gérer l’analyse":::

1. Vous pouvez _réexécuter l'analyse_, _modifier l’analyse_, _Supprimer l’analyse_  

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan-options.png" alt-text="gérer les options d’analyse":::

1. Vous pouvez réexécuter une _analyse complète_

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-full-scan.png" alt-text="Analyse complète":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
