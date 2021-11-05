---
title: Inscrire et analyser Azure Data Lake Storage (ADLS) Gen1
description: Cet article décrit le processus d’inscription d’une source de données Azure Data Lake Storage Gen1 dans Azure Purview, et donne des instructions pour vous authentifier auprès de la source Azure Data Lake Storage Gen1 et interagir avec elle
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 2887511d9d92fcf9112473207ba3445282b8b87f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015610"
---
# <a name="connect-to-azure-data-lake-gen1-in-azure-purview"></a>Se connecter à Azure Data Lake Gen1 dans Azure Purview

Cet article décrit le processus d’inscription d’une source de données Azure Data Lake Storage Gen1 dans Azure Purview, et donne des instructions pour vous authentifier auprès de la source Azure Data Lake Storage Gen1 et interagir avec elle.

> [!Note]
> Azure Data Lake Storage Gen2 est maintenant en disponibilité générale. Nous vous recommandons de commencer à l’utiliser dès aujourd'hui. Pour plus d’informations, consultez la [page du produit](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)|[Oui](#scan) | [Oui](#scan)|[Oui](#scan)| Non |[Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section vous permet d’inscrire la source de données ADLS Gen1 et de configurer un mécanisme d’authentification approprié pour garantir la réussite de l’analyse de la source de données.

### <a name="steps-to-register"></a>Procédure d’inscription

Il est important d’inscrire la source de données dans Azure Purview avant de configurer une analyse de la source de données.

1. Allez sur le [portail Azure](https://portal.azure.com) et accédez à la page **Comptes Purview**, puis cliquez sur votre _Compte Purview_

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Capture d’écran montrant le compte Purview utilisé pour enregistrer la source de données":::

1. **Ouvrez Purview Studio** et accédez à **Data Map --> Sources**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Capture d’écran montrant le lien permettant d’ouvrir Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sources.png" alt-text="Capture d’écran qui navigue jusqu’au lien sources dans le mappage de données":::

1. Créez la [hiérarchie de collection](./quickstart-create-collection.md) à l’aide du menu **Collections** et attribuez des autorisations à des sous-collections individuelles, selon les besoins

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-collection.png" alt-text="Capture d’écran montrant le menu de collection pour créer une hiérarchie de collections":::

1. Accédez à la collection appropriée dans le menu **Sources** et cliquez sur l’icône **Inscription** pour inscrire une nouvelle source de données ADLS Gen1

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-register.png" alt-text="Capture d’écran montrant la collection utilisée pour enregistrer la source de données":::

1. Sélectionnez la source de données **Azure Data Lake Storage Gen1**, puis cliquez sur **Continuer**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-source.png" alt-text="Capture d’écran qui permet de sélectionner la source de données":::

1. Fournissez un **Nom** approprié pour la source de données, sélectionnez l’**Abonnement Azure** approprié, le **Nom du compte Data Lake Store** existant et la **Collection**, puis cliquez sur **Appliquer**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-details.png" alt-text="Capture d’écran montrant les détails à entrer pour inscrire la source de données":::

1. Le compte de stockage ADLS Gen1 s’affiche sous la collection sélectionnée

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-source-hierarchy.png" alt-text="Capture d’écran montrant la source de données mappée à la collection pour lancer l’analyse":::

## <a name="scan"></a>Analyser

### <a name="prerequisites-for-scan"></a>Prérequis pour l’analyse

Pour pouvoir analyser la source de données, une méthode d’authentification dans le compte de stockage ADLS Gen1 doit être configurée.
Les options suivantes sont prises en charge :

> [!Note]
> Si un pare-feu est activé pour le compte de stockage, vous devez utiliser la méthode d’authentification Identité managée lors de la configuration d’une analyse.

* **Identité managée (recommandé)**  : dès que le compte Azure Purview est créé, une **identité managée** système est créée automatiquement dans le locataire Azure AD. Selon le type de ressource, des attributions de rôles RBAC spécifiques sont nécessaires pour permettre au MSI Azure Purview d’effectuer les analyses.

* **Principal de service** : dans cette méthode, vous pouvez créer un principal de service ou en utiliser un existant dans votre locataire Azure Active Directory.

### <a name="authentication-for-a-scan"></a>Authentification pour une analyse

#### <a name="using-managed-identity-for-scanning"></a>Utilisation d’une identité managée pour l’analyse

Il est important de donner à votre compte Purview l’autorisation d’analyser la source de données ADLS Gen1. Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note]
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter une identité managée sur une ressource Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, un compte de stockage Azure Data Lake Storage Gen1) que le catalogue doit être autorisé à analyser.
1. Cliquez sur **Vue d’ensemble** et sélectionnez **Explorateur de données**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Capture d’écran montrant le compte de stockage":::

1. Cliquez sur **Accès** dans la barre de navigation supérieure

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Capture d’écran montrant l’Explorateur de données pour le compte de stockage":::

1. Cliquez sur **Sélectionner**, puis ajoutez le _catalogue Purview_ dans **Sélectionner un utilisateur ou un groupe**.
1. Sélectionnez les autorisations **Lire** et **Exécuter**. Veillez à choisir **Ce dossier et tous ses enfants** et **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut** dans les options Ajouter, comme indiqué dans la capture d’écran ci-dessous. Dans le menu Paramètres, cliquez sur **OK**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-assign-permissions.png" alt-text="Capture d’écran montrant les détails qui permettent d’attribuer des autorisations pour le compte Purview":::

> [!Tip]
> Une **entrée d’autorisation d’accès** est une entrée d’autorisation sur les fichiers et les dossiers _actuels_. Une **entrée d’autorisation par défaut** est une entrée d’autorisation dont les nouveaux fichiers et dossiers _vont hériter_.
Pour accorder une autorisation seulement aux fichiers existants, **choisissez une entrée d’autorisation d’accès**.
Pour accorder une autorisation d’analyser les fichiers et dossiers qui seront ajoutés ultérieurement, **incluez une entrée d’autorisation par défaut**.

#### <a name="using-service-principal-for-scanning"></a>Utilisation du principal de service pour l’analyse

##### <a name="creating-a-new-service-principal"></a>Création d’un principal de service

Si vous avez besoin de [Créer un principal de service](./create-service-principal-azure.md), vous devez inscrire une application dans votre locataire Azure AD et fournir l’accès au principal de service dans vos sources de données. Votre Administrateur général Azure AD ou d’autres rôles, comme l’Administrateur d’application, peuvent effectuer cette opération.

##### <a name="getting-the-service-principals-application-id"></a>Obtention de l’ID d’application du principal de service

1. Copiez l’**ID d’application (client)** présent dans la **Vue d’ensemble** du [_Principal de service_](./create-service-principal-azure.md) déjà créé

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-appl-id.png" alt-text="Capture d’écran montrant l’ID d’application (client) pour le principal de service":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen1-account"></a>Octroi au principal de service de l’accès à votre compte ADLS Gen1

Il est important de donner à votre principal de service l’autorisation d’analyser la source de données ADLS Gen2. Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note]
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter un principal de service sur une ressource Azure.

1. Fournissez au principal de service l’accès au compte de stockage en ouvrant le compte de stockage, puis en cliquant sur **Vue d’ensemble** --> **Explorateur de données**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-data-explorer.png" alt-text="Capture d’écran montrant le compte de stockage":::

1. Cliquez sur **Accès** dans la barre de navigation supérieure

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-storage-access.png" alt-text="Capture d’écran montrant l’Explorateur de données pour le compte de stockage":::

1. Cliquez sur **Sélectionner**, puis ajoutez le _principal de service_ dans **Sélectionner un utilisateur ou un groupe**.
1. Sélectionnez les autorisations **Lire** et **Exécuter**. Veillez à choisir **Ce dossier et tous ses enfants** et **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut** dans les options Ajouter. Dans le menu Paramètres, cliquez sur **OK**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-permissions.png" alt-text="Capture d’écran montrant les détails qui permettent d’attribuer des autorisations pour le principal de service":::

### <a name="creating-the-scan"></a>Création de l’analyse

1. Ouvrez votre **Compte Purview**, puis cliquez sur **Ouvrir Purview Studio**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-purview-acct.png" alt-text="Capture d’écran montrant l’option Ouvrir Purview Studio":::

1. Accéder aux **sources** -->  **de mappage de données** pour afficher la hiérarchie de collection

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-open-purview-studio.png" alt-text="Capture d’écran montrant la hiérarchie de collection":::

1. Cliquez sur l’icône **Nouvelle analyse** sous la **Source de données ADLS Gen1** inscrite précédemment

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-new-scan.png" alt-text="Capture d’écran montrant la source de données avec l’icône Nouvelle analyse":::

#### <a name="if-using-managed-identity"></a>Si vous utilisez l’identité managée

Donnez un **Nom** à l’analyse, sélectionnez **MSI Purview** sous **Informations d’identification**, choisissez la collection appropriée pour l’analyse, puis cliquez sur **Tester la connexion**. En cas de connexion réussie, cliquez sur **Continuer**.

:::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-managed-identity.png" alt-text="Capture d’écran montrant l’option Identité managée pour exécuter l’analyse":::

#### <a name="if-using-service-principal"></a>Si vous utilisez le principal de service

1. Donnez un **Nom** à l’analyse, choisissez la collection appropriée pour l’analyse, puis cliquez sur **+ Nouveau** sous **Informations d’identification**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp.png" alt-text="Capture d’écran montrant l’option du principal de service":::

1. Sélectionnez la **Connexion de coffre de clés** approprié et le **Nom de secret** qui a été utilisé lors de la création du _principal de service_. L’**ID du principal de service** est l’**ID d’application (client)** copié comme indiqué précédemment

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-key-vault.png" alt-text="Capture d’écran montrant l’option de coffre de clés du principal de service":::

1. Cliquez sur **Tester la connexion**. En cas de connexion réussie, cliquez sur **Continuer**

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-sp-test-connection.png" alt-text="Capture d’écran montrant l’option Tester la connexion pour le principal de service":::

### <a name="scoping-and-running-the-scan"></a>Étendue et exécution de l’analyse

1. Vous pouvez limiter votre analyse à des dossiers ou sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Si vous créez un _ensemble de règles d’analyse_, sélectionnez les **types de fichiers** à inclure dans la règle d’analyse. 

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-file-types.png" alt-text="Types de fichiers de l’ensemble de règles d’analyse":::

1. Vous pouvez sélectionner les **règles de classification** à inclure dans la règle d’analyse

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-classification-rules.png" alt-text="Règles de classification de l’ensemble de règles d’analyse":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-select-scan-rule-set.png" alt-text="Sélection de l’ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-trigger.png" alt-text="déclencheur d’analyse":::

    :::image type="content" source="media/register-scan-adls-gen1/register-register-adls-gen1-scan-trigger-selection.png" alt-text="Sélection du déclencheur d’analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-review-scan.png" alt-text="Vérifier l’analyse":::

### <a name="viewing-scan"></a>Affichage de l’analyse

1. Accédez à la _source de données_ dans la _Collection_, puis cliquez sur **Afficher les détails** pour vérifier l’état de l’analyse

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-view-scan.png" alt-text="Afficher l’analyse":::

1. Les détails de l’analyse indiquent la progression de l’analyse dans l’**état de la dernière exécution** et le nombre de ressources _analysées_ et _classées_

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-details.png" alt-text="Afficher les détails de l’analyse":::

1. L’**État de la dernière exécution** est mis à jour sur **En cours**, puis sur **Terminé** une fois la totalité de l’analyse exécutée avec succès

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-in-progress.png" alt-text="afficher l’analyse en cours":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-scan-completed.png" alt-text="afficher l’analyse terminée":::

### <a name="managing-scan"></a>Gestion de l’analyse

Les analyses peuvent être gérées ou réexécutées à la fin.

1. Cliquez sur le **Nom de l’analyse** pour la gérer

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan.png" alt-text="Gérer l’analyse":::

1. Vous pouvez _réexécuter l'analyse_, _modifier l’analyse_, _Supprimer l’analyse_  

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-options.png" alt-text="gérer les options d’analyse":::

    > [!NOTE]
    > * La suppression de votre analyse ne supprime pas les ressources de votre catalogue créées à partir des analyses précédentes.
    > * La ressource n’est plus mise à jour avec les modifications de schéma si votre table source a été modifiée et que vous relancez l’analyse de la table source après avoir modifié la description sous l’onglet Schéma de Purview.

1. Vous pouvez _réexécuter une analyse incrémentielle_ ou une _analyse complète_.

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-full-inc-scan.png" alt-text="Gérer l’analyse complète ou incrémentielle":::

    :::image type="content" source="media/register-scan-adls-gen1/register-adls-gen1-manage-scan-results.png" alt-text="Gérer les résultats de l’analyse":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
