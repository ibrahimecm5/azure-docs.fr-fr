---
title: Approvisionnement de stratégie d’accès aux données pour le service Stockage Azure
description: Guide pas à pas sur la façon d’intégrer le service Stockage Azure dans des stratégies d’accès Azure Purview, et de permettre aux propriétaires de données de créer des stratégies d’accès.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/15/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eab2c720aafe2cfd5a1ca46f2549b42d6f644b25
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555714"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Approvisionnement du jeu de données par le propriétaire des données pour le service Stockage Azure

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge
Ce guide décrit comment configurer stockage Azure pour appliquer des stratégies d’accès aux données créées et gérées à partir d’Azure Purview. La création de stratégie Azure Purview prend en charge les fonctionnalités suivantes :
-   Stratégies d’accès aux données pour contrôler l’accès aux données stockées dans des fichiers Blob ou ADLS Gen2

> [!IMPORTANT]
> Ces fonctionnalités sont actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et ne doit pas être utilisée pour des charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="important-limitations"></a>Limitations importantes
1. La fonctionnalité de stratégie d’accès n’est disponible que sur de nouveaux comptes Azure Purview et Stockage Azure.
2. Inscrivez toutes les sources de données pour utiliser la gouvernance et gérer toutes les stratégies d’accès associées dans un seul compte Azure Purview.
3. Cette fonctionnalité ne peut être utilisée que dans les régions répertoriées ci-dessous, où les fonctionnalités de gestion des stratégies d’accès et d’application sont déployées.

### <a name="supported-regions"></a>Régions prises en charge

#### <a name="azure-purview-management-side"></a>Azure Purview (côté gestion)
-   Europe Nord
-   Europe Ouest
-   Sud du Royaume-Uni
-   USA Est
-   USA Est 2
-   États-Unis - partie centrale méridionale
-   USA Ouest 2
-   Asie Sud-Est
-   Australie Est
-   Centre du Canada
-   France Centre

#### <a name="azure-storage-enforcement-side"></a>Stockage Azure (côté application)
-   France Centre
-   Centre du Canada

## <a name="prerequisites"></a>Prérequis
> [!Important]
> Lisez attentivement cette section. Il existe plusieurs conditions préalables pour que les stratégies d’accès fonctionnent correctement.

### <a name="select-an-isolated-test-subscription"></a>Sélectionner un abonnement de test isolé
Créez ou utilisez un abonnement de test isolé et suivez les étapes ci-dessous pour créer un nouveau compte Stockage Azure et un nouveau compte Azure Purview dans cet abonnement.

### <a name="create-azure-storage-account"></a>Créer un compte de stockage Azure
Créez un compte de stockage Azure dans les régions mentionnées ci-dessus sous limitations. Voir [Créer un compte de stockage - Compte de stockage Azure](../storage/common/storage-account-create.md)

### <a name="configure-azure-storage-to-enforce-access-policies-from-purview"></a>Configurer le stockage Azure pour appliquer des stratégies d’accès à partir de Purview

#### <a name="enable-access-policy-enforcement-in-the-subscription"></a>Activer l’application de la stratégie d’accès dans l’abonnement
Pour vous inscrire et confirmer que la fonctionnalité de stratégie d’accès est activée dans l’abonnement où se trouve le compte stockage Azure, exécutez les commandes suivantes dans PowerShell :

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
Si la sortie de la dernière commande affiche la valeur de « RegistrationState » « Registered », cela signifie que votre abonnement est activé pour cette fonctionnalité.

#### <a name="check-access-permissions-in-azure-storage"></a>Vérifier les autorisations d’accès dans le Stockage Azure
Un utilisateur doit avoir le rôle « propriétaire » dans le compte stockage Azure pour inscrire ultérieurement cette source de données dans Azure Purview pour les stratégies d’accès : [cochez l’option accès pour un utilisateur aux ressources Azure Purview](../role-based-access-control/check-access.md)

### <a name="create-azure-purview-account"></a>Créer un compte Azure Purview
Créez un compte Azure Purview dans les régions où la nouvelle fonctionnalité est activée, sous l’abonnement test isolé. Pour créer un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview dans le portail Azure](create-catalog-portal.md).

### <a name="configure-azure-purview-to-manage-access-policies"></a>Configurer Azure Purview pour gérer les stratégies d’accès
Suivez les étapes ci-dessous pour activer Azure Puview pour gérer les stratégies d’accès 

#### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Acceptez de participer à la préversion de la stratégie d’utilisation des données d’Azure Purview
Cette fonctionnalité étant actuellement en préversion, vous devez [accepter de participer à la préversion des stratégies d’utilisation des données d’Azure Purview](https://aka.ms/opt-in-data-use-policy)

#### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>Inscrire Purview en tant que fournisseur de ressources dans d’autres abonnements
Exécutez cette étape uniquement si le compte Stockage auquel vous souhaitez gérer l’accès se trouve dans un autre abonnement que le compte Azure Purview. Inscrivez Azure Purview en tant que fournisseur de ressources dans ces abonnements en suivant ce guide :  
[Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md)

#### <a name="configure-permissions-for-policy-management-actions"></a>Configurer des autorisations pour les actions de gestion de stratégie
- L’utilisateur doit être le propriétaire de la source de données et l’administrateur de la source de données Purview pour inscrire une source pour la gouvernance d’utilisation des données. Toutefois, l’un de ces rôles peut annuler l’inscription de la source pour la gouvernance des données.
- L’utilisateur doit faire partie du rôle d’auteur de stratégie portée au niveau de la collection racine pour exécuter des actions de création/gestion de stratégie.
- L’utilisateur doit faire partie du rôle d’administrateur de source de données portée au niveau de la collection racine pour publier la stratégie.

Consultez la section relative à la gestion des attributions de rôles dans le guide [Comment créer et gérer des collections](how-to-create-and-manage-collections.md).

En plus de ces éléments, consultez la section « problèmes connus » en bas de ce document.

#### <a name="register-and-scan-data-sources-in-purview"></a>Inscrire et analyser des sources de données dans Purview
Enregistrez et analysez chaque source de données avec Purview pour définir ultérieurement des stratégies d’accès. Pour inscrire votre compte de stockage, suivez les guides d’inscription de Purview :

-   [Comment analyser un Blob de Stockage Azure – Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Inscrire et analyser Azure Data Lake Storage (ADLS) Gen2 – Azure Purview](register-scan-adls-gen2.md)

Pendant l’inscription, activez la source de données pour la **gouvernance de l’utilisation des données**, comme illustré dans l’image.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Image montrant comment inscrire une source de données pour la stratégie.":::

> [!NOTE]
> Le comportement du bouton bascule permet d’imposer que toutes les sources de données d’un abonnement donné puissent être inscrites uniquement pour la gouvernance des données à l’aide d’un seul compte Purview. Ce compte Purview lui-même peut être dans n’importe quel abonnement dans le locataire.

## <a name="policy-authoring"></a>Création de stratégie

Cette section décrit les étapes de création, de mise à jour et de publication des stratégies d’accès Purview.

### <a name="create-a-new-policy"></a>Créer une nouvelle stratégie

Cette section décrit les étapes de création de stratégie dans Azure Purview.

1. Connectez-vous au portail Purview.

1. Accédez à application de **Gestion des stratégies** dans le volet de gauche.

1. Sélectionnez le bouton **Nouvelle stratégie** dans la page des stratégies.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="Image montrant comment un propriétaire de données peut accéder à la fonctionnalité Stratégie dans Azure Purview quand il souhaite créer des stratégies.":::

1. La page Nouvelle stratégie s’affiche. Entrez le **Nom** et la **Description** de la stratégie.

1. Pour ajouter des instructions de stratégie à la nouvelle stratégie, sélectionnez le bouton **Nouvelle instruction de stratégie**. Cela a pour effet d’afficher le générateur d’instructions de stratégie.

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="Image montrant comment un propriétaire de données peut créer une instruction de stratégie.":::

1. Sélectionnez le bouton **Action**, puis choisissez Lire ou Modifier dans la liste déroulante.

1. Sélectionnez le bouton **Effet**, puis choisissez Autoriser dans la liste déroulante.

1. Sélectionnez le bouton **Ressources de données** pour afficher les options permettant de fournir le chemin d’accès à la ressource de données.

1. Dans la zone **Ressources**, entrez le **Type de source de données** et sélectionnez le **Nom** d’une source de données précédemment inscrite.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="Image montrant comment un propriétaire de données peut sélectionner une ressource de données lors de la modification d’une instruction de stratégie.":::

1. Sélectionnez le bouton **Continuer**, puis parcourez la hiérarchie pour sélectionner le dossier ou le fichier. Sélectionnez ensuite le bouton **Ajouter**. Cela a pour effet de vous ramener à l’éditeur de stratégie.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="Image montrant comment un propriétaire de données peut sélectionner la ressource lors de la création ou de la modification d’une instruction de stratégie.":::

1. Sélectionnez le bouton **Sujets**, puis entrez l’identité du sujet, à savoir principal, groupe ou MSI. Sélectionnez ensuite le bouton **OK**. Cela a pour effet de vous ramener à l’éditeur de stratégie.

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="Image montrant comment un propriétaire de données peut sélectionner le sujet lors de la création ou de la modification d’une instruction de stratégie.":::

1. Répétez les étapes #5 à #11 pour entrer toute autre instruction de stratégie.

1. Sélectionnez le bouton **Enregistrer** pour enregistrer la stratégie.

### <a name="update-or-delete-a-policy"></a>Mettre à jour ou supprimer une stratégie

Les étapes de création d’une stratégie dans Purview sont les suivantes.

1. Connectez-vous au portail Purview.

1. Accédez à application Stratégie Purview dans le volet de gauche.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Image montrant comment un propriétaire de données peut accéder à la fonctionnalité Stratégie dans Azure Purview quand il souhaite mettre à jour une stratégie.":::

1. Le portail Stratégie présente la liste des stratégies existantes dans Purview. Sélectionnez la stratégie à mettre à jour.

1. La page des détails de la stratégie s’affiche, avec les options Modifier et Supprimer. Sélectionnez le bouton **Modifier** qui affiche le générateur d’instructions de stratégie pour les instructions de cette stratégie. Vous pouvez à présent mettre à jour toutes les parties des instructions de cette stratégie. Pour supprimer la stratégie, utilisez le bouton **Supprimer**.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="Image montrant comment un propriétaire de données peut modifier ou supprimer une instruction de stratégie.":::

### <a name="publish-the-policy"></a>Publier la stratégie

Une stratégie nouvellement créée est à l’état de brouillon. Le processus de publication associe la nouvelle stratégie à une ou plusieurs sources de données sous gouvernance.
Les étapes de publication d’une stratégie sont les suivantes

1. Connectez-vous au portail Purview.

1. Accédez à l’application Stratégie Purview dans le volet de gauche.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="Image montrant comment un propriétaire de données peut accéder à la fonctionnalité Stratégie dans Azure Purview quand il souhaite publier une stratégie.":::

1. Le portail Stratégie présente la liste des stratégies existantes dans Purview. Recherchez la stratégie à publier. Sélectionnez le bouton **Publier** dans l’angle supérieur droit de la page.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="Image montrant comment un propriétaire de données peut publier une stratégie.":::

1. Une liste de sources de données s’affiche. Vous pouvez entrer un nom pour la filtrer. Ensuite, sélectionnez chaque source de données dans laquelle cette stratégie doit être publiée, puis cliquez sur le bouton **Publier**.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Image montrant comment un propriétaire de données peut sélectionner la source de données dans laquelle la stratégie sera publiée.":::

>[!NOTE]
> La publication est une opération en arrière-plan. Jusqu’à **2 heures** peuvent s’écouler avant que les modifications soient reflétées dans la source de données.

## <a name="azure-purview-policy-action-to-azure-storage-action-mapping"></a>Action de stratégie Azure Purview pour le mappage d’action du stockage Azure

Cette section contient une référence sur la façon dont les actions des stratégies de données Azure Purview sont mappées à des actions spécifiques dans le stockage Azure.

| **Action de stratégie Purview** | **Actions spécifiques de la source de données**                                                        |
|---------------------------|-----------------------------------------------------------------------------------------|
|||
| *Lire*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|||
| *Modify*                  |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write               |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action          |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action         |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete              |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/write                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/delete                    |
|||

## <a name="known-issues"></a>Problèmes connus
Les problèmes connus de la version actuelle sont les suivants :
1. En plus du rôle auteur de la stratégie, l’utilisateur doit disposer de l’autorisation lecteur de répertoire dans Azure Active Directory (AAD) pour créer une stratégie de propriétaire de données.
1. Le rôle auteur de la stratégie n’est pas suffisant pour créer des stratégies. Il nécessite également le rôle d’administrateur de la source de données portée.

## <a name="next-steps"></a>Étapes suivantes
Consultez le blog et la démonstration relatifs aux fonctionnalités mentionnées dans ce guide pratique.

* [Nouveautés Azure Purview chez Microsoft Ignite 2021](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Démonstration de la stratégie d’accès pour le stockage Azure](https://www.youtube.com/watch?v=CFE8ltT19Ss)
