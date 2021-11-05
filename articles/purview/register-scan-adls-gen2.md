---
title: Inscrire et analyser Azure Data Lake Storage (ADLS) Gen2
description: Cet article décrit le processus d’inscription d’une source de données Azure Data Lake Storage Gen2 dans Azure Purview, et donne des instructions pour vous authentifier auprès de la source Azure Data Lake Storage Gen2 et interagir avec elle
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 6459dc9cfccac5f17e0fac155121a817a0fad060
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038240"
---
# <a name="connect-to-azure-data-lake-gen2-in-azure-purview"></a>Se connecter à Azure Data Lake Gen2 dans Azure Purview

Cet article décrit le processus d’inscription d’une source de données Azure Data Lake Storage Gen2 dans Azure Purview, et donne des instructions pour vous authentifier auprès de la source Azure Data Lake Storage Gen2 et interagir avec elle

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan)|[Oui](#scan) | [Oui](#scan)|[Oui](#scan)| Oui | [Traçabilité des données Data Factory](how-to-link-azure-data-factory.md) |

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section vous permet d’inscrire la source de données ADLS Gen2 et de configurer un mécanisme d’authentification approprié pour garantir la réussite de l’analyse de la source de données.

### <a name="steps-to-register"></a>Procédure d’inscription

Il est important d’inscrire la source de données dans Azure Purview avant de configurer une analyse de la source de données.

1. Allez sur le [portail Azure](https://portal.azure.com) et accédez à la page **Comptes Purview**, puis cliquez sur votre _Compte Purview_

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-purview-acct.png" alt-text="Capture d’écran montrant le compte Purview utilisé pour enregistrer la source de données":::

1. **Ouvrez Purview Studio** et accédez à **Data Map --> Sources**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-open-purview-studio.png" alt-text="Capture d’écran montrant le lien permettant d’ouvrir Purview Studio":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sources.png" alt-text="Capture d’écran qui navigue jusqu’au lien sources dans le mappage de données":::

1. Créez la [hiérarchie de collection](./quickstart-create-collection.md) à l’aide du menu **Collections** et attribuez des autorisations à des sous-collections individuelles, selon les besoins

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-collections.png" alt-text="Capture d’écran montrant le menu de collection pour créer une hiérarchie de collections":::

1. Accédez à la collection appropriée dans le menu **Sources** et cliquez sur l’icône **Inscription** pour inscrire une nouvelle source de données ADLS Gen2

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-register-source.png" alt-text="Capture d’écran montrant la collection utilisée pour enregistrer la source de données":::

1. Sélectionnez la source de données **Azure Data Lake Storage Gen2**, puis cliquez sur **Continuer**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-data-source.png" alt-text="Capture d’écran qui permet de sélectionner la source de données":::

1. Fournissez un **Nom** approprié pour la source de données, sélectionnez l’**Abonnement Azure** approprié, le **Nom du compte Data Lake Store** existant et la **Collection**, puis cliquez sur **Appliquer**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-details.png" alt-text="Capture d’écran montrant les détails à entrer pour inscrire la source de données":::

1. Le compte de stockage ADLS Gen2 s’affiche sous la collection sélectionnée

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-data-source-collection.png" alt-text="Capture d’écran montrant la source de données mappée à la collection pour lancer l’analyse":::

## <a name="scan"></a>Analyser

### <a name="prerequisites-for-scan"></a>Prérequis pour l’analyse

Pour pouvoir analyser la source de données, une méthode d’authentification dans le compte de stockage ADLS Gen2 doit être configurée.
Les options suivantes sont prises en charge :

> [!Note]
> Si un pare-feu est activé pour le compte de stockage, vous devez utiliser la méthode d’authentification Identité managée lors de la configuration d’une analyse.

* **Identité managée (recommandé)**  : dès que le compte Azure Purview est créé, une **identité managée** système est créée automatiquement dans le locataire Azure AD. Selon le type de ressource, des attributions de rôles RBAC spécifiques sont nécessaires pour permettre au MSI Azure Purview d’effectuer les analyses.

* **Clé de compte** : les secrets peuvent être créés à l’intérieur d’un Azure Key Vault pour stocker les informations d’identification afin d’autoriser l’accès à Azure Purview pour analyser les sources de données en toute sécurité à l’aide des secrets. Un secret peut être une clé de compte de stockage, un mot de passe de connexion SQL ou un mot de passe.

   > [!Note]
   > Si vous utilisez cette option, vous devez déployer une ressource _coffre de clés Azure_ dans votre abonnement et attribuer le MSI du _compte Azure Purview_ avec l’autorisation d’accès nécessaire aux secrets dans le _coffre de clés Azure_.

* **Principal de service** : dans cette méthode, vous pouvez créer un principal de service ou en utiliser un existant dans votre locataire Azure Active Directory.

### <a name="authentication-for-a-scan"></a>Authentification pour une analyse

#### <a name="using-managed-identity-for-scanning"></a>Utilisation d’une identité managée pour l’analyse

Il est important de donner à votre compte Purview l’autorisation d’analyser la source de données ADLS Gen2. Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note]
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter une identité managée sur une ressource Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, un compte de stockage Azure Data Lake Storage Gen2) que le catalogue doit être autorisé à analyser.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Capture d’écran montrant le compte de stockage":::

1. Cliquez sur **Contrôle d’accès (IAM)** dans le menu de navigation de gauche, puis sur **+ Ajouter** --> **Ajouter une attribution de rôle**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Capture d’écran montrant le contrôle d’accès pour le compte de stockage":::

1. Définissez le **Rôle** sur **Lecteur des données Blob du stockage**, puis entrez votre _nom de compte Azure Purview_ sous la zone d’entrée **Sélectionner**. Ensuite, sélectionnez **Enregistrer** pour fournir cette attribution de rôle à votre compte Purview.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-assign-permissions.png" alt-text="Capture d’écran montrant les détails permettant d’attribuer des autorisations pour le compte Purview":::

> [!Note]
> Pour plus d’informations, consultez les étapes sous [Autoriser l’accès aux objets blob et files d’attente avec Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md)

> [!NOTE]
> Si un pare-feu est activé pour le compte de stockage, vous devez utiliser la méthode d’authentification **Identité managée** lors de la configuration d’une analyse.

1. Accédez à votre compte de stockage ADLS Gen2 sur le [portail Azure](https://portal.azure.com)
1. Accédez à **Sécurité + réseau >Réseau**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-networking.png" alt-text="Capture d’écran montrant les détails permettant de fournir un accès par le pare-feu":::

1. Sélectionnez **Réseaux sélectionnés** sous **Autoriser l’accès depuis**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-network-access.png" alt-text="Capture d’écran montrant les détails permettant d’autoriser l’accès aux réseaux sélectionnés":::

1. Dans la section **Exceptions**, sélectionnez **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** et appuyez sur **Enregistrer**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-permission-microsoft-services.png" alt-text="Capture d’écran montrant les exceptions pour autoriser les services Microsoft approuvés à accéder au compte de stockage":::

#### <a name="using-account-key-for-scanning"></a>Utilisation de la clé de compte pour l’analyse

Lorsque la méthode d’authentification sélectionnée est **Clé de compte**, vous devez récupérer votre clé d’accès et la stocker dans le coffre de clés :

1. Accéder à votre compte de stockage ADLS Gen2
1. Sélectionnez **Sécurité + mise en réseau > Clés d’accès**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-keys.png" alt-text="Capture d’écran montrant les clés d’accès dans le compte de stockage":::

1. Copiez votre *clé* et enregistrez-la quelque part pour la suite

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key.png" alt-text="Capture d’écran montrant la clé d’accès à copier":::

1. Accédez à votre coffre de clés.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-key-vault.png" alt-text="Capture d’écran montrant le coffre de clés":::

1. Sélectionnez **Paramètres > Secrets**, puis cliquez sur **+ Générer/Importer**
:::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-generate-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés permettant de générer un secret":::

1. Entrez le **Nom** et la **Valeur** comme *clé* de votre compte de stockage

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret-values.png" alt-text="Capture d’écran montrant l’option de coffre de clés pour entrer les valeurs de secret":::

1. Sélectionnez **Créer** pour terminer.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-secret.png" alt-text="Capture d’écran montrant l’option de coffre de clés permettant de créer un secret":::

1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

#### <a name="using-service-principal-for-scanning"></a>Utilisation du principal de service pour l’analyse

##### <a name="creating-a-new-service-principal"></a>Création d’un principal de service

Si vous avez besoin de [Créer un principal de service](./create-service-principal-azure.md), vous devez inscrire une application dans votre locataire Azure AD et fournir l’accès au principal de service dans vos sources de données. Votre Administrateur général Azure AD ou d’autres rôles, comme l’Administrateur d’application, peuvent effectuer cette opération.

##### <a name="getting-the-service-principals-application-id"></a>Obtention de l’ID d’application du principal de service

1. Copiez l’**ID d’application (client)** présent dans la **Vue d’ensemble** du [_Principal de service_](./create-service-principal-azure.md) déjà créé

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-appln-id.png" alt-text="Capture d’écran montrant l’ID d’application (client) pour le principal de service":::

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Octroi au principal de service de l’accès à votre compte ADLS Gen2

Il est important de donner à votre principal de service l’autorisation d’analyser la source de données ADLS Gen2. Vous pouvez ajouter le MSI de Data Catalog au niveau de l’abonnement, du groupe de ressources ou de la ressource, en fonction des autorisations d’analyse que vous souhaitez lui accorder.

> [!Note]
> Vous devez être propriétaire de l’abonnement pour pouvoir ajouter un principal de service sur une ressource Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez l’abonnement, le groupe de ressources ou la ressource (par exemple, un compte de stockage Azure Data Lake Storage Gen2) que le catalogue doit être autorisé à analyser.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-storage-acct.png" alt-text="Capture d’écran montrant le compte de stockage":::

1. Cliquez sur **Contrôle d’accès (IAM)** dans le menu de navigation de gauche, puis sur **+ Ajouter** --> **Ajouter une attribution de rôle**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-access-control.png" alt-text="Capture d’écran montrant le contrôle d’accès pour le compte de stockage":::

1. Définissez le **Rôle** sur **Lecteur des données Blob du stockage**, puis entrez votre _principal de service_ dans la zone d’entrée **Sélectionner**. Ensuite, sélectionnez **Enregistrer** pour fournir cette attribution de rôle à votre compte Purview.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-permission.png" alt-text="Capture d’écran montrant les détails permettant de fournir des autorisations de compte de stockage au principal de service":::

### <a name="create-the-scan"></a>Créer l’analyse

1. Ouvrez votre **Compte Purview**, puis cliquez sur **Ouvrir Purview Studio**
1. Accéder aux **sources** -->  **de mappage de données** pour afficher la hiérarchie de collection
1. Cliquez sur l’icône **Nouvelle analyse** sous la **Source de données ADLS Gen2** inscrite précédemment

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-new-scan.png" alt-text="Capture d’écran montrant l’écran de création d’une nouvelle analyse":::

#### <a name="if-using-managed-identity"></a>Si vous utilisez l’identité managée

1. Donnez un **Nom** à l’analyse, sélectionnez **MSI Purview** sous **Informations d’identification**, choisissez la collection appropriée pour l’analyse, puis cliquez sur **Tester la connexion**. En cas de connexion réussie, cliquez sur **Continuer**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-managed-identity.png" alt-text="Capture d’écran montrant l’option Identité managée pour exécuter l’analyse":::

#### <a name="if-using-account-key"></a>Si vous utilisez une clé de compte

1. Donnez un **Nom** à l’analyse, choisissez la collection appropriée pour l’analyse et sélectionnez **Méthode d’authentification** comme _Clé de compte_

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-acct-key.png" alt-text="Capture d’écran montrant l’option de clé de compte pour l’analyse":::

#### <a name="if-using-service-principal"></a>Si vous utilisez le principal de service

1. Donnez un **Nom** à l’analyse, choisissez la collection appropriée pour l’analyse, puis cliquez sur **+ Nouveau** sous **Informations d’identification**

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-sp-option.png" alt-text="Capture d’écran montrant l’option permettant au principal de service d’activer l’analyse":::

1. Sélectionnez la **Connexion de coffre de clés** approprié et le **Nom de secret** qui a été utilisé lors de la création du _principal de service_. L’**ID du principal de service** est l’**ID d’application (client)** copié précédemment.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-service-principal-option.png" alt-text="Capture d’écran montrant l’option du principal de service":::

1. Cliquez sur **Tester la connexion**. En cas de connexion réussie, cliquez sur **Continuer**

### <a name="scope-and-run-the-scan"></a>Délimiter et exécuter l’analyse

1. Vous pouvez limiter votre analyse à des dossiers ou des sous-dossiers spécifiques en choisissant les éléments appropriés dans la liste.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scope-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Si vous créez un _ensemble de règles d’analyse_, sélectionnez les **types de fichiers** à inclure dans la règle d’analyse.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-file-types.png" alt-text="Types de fichiers de l’ensemble de règles d’analyse":::

1. Vous pouvez sélectionner les **règles de classification** à inclure dans la règle d’analyse

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-classification rules.png" alt-text="Règles de classification de l’ensemble de règles d’analyse":::

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-select-scan-rule-set.png" alt-text="Sélection de l’ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification ou exécuter l’analyse une seule fois.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-scan-trigger.png" alt-text="déclencheur d’analyse":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

    :::image type="content" source="media/register-scan-adls-gen2/register-adls-gen2-review-scan.png" alt-text="Vérifier l’analyse":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
