---
title: Approvisionnement de stratégie d’accès aux données pour le service Stockage Azure
description: Guide pas à pas sur la façon d’intégrer le service Stockage Azure dans des stratégies d’accès Azure Purview, et de permettre aux propriétaires de données de créer des stratégies d’accès.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ea5285c5fd29bfe34f97c87b2ac0c9bd7a5502a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425232"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Approvisionnement du jeu de données par le propriétaire des données pour le service Stockage Azure

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

La création de stratégie Purview prend en charge les fonctionnalités suivantes :
-   Stratégie d’accès aux données pour permettre au service Stockage Azure de contrôler l’accès aux données stockées dans des fichiers Blob ou ADLS Gen2

> [!IMPORTANT]
> Ces fonctionnalités sont actuellement en préversion. Cette préversion est fournie sans contrat de niveau de service et ne doit pas être utilisée pour des charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="prerequisites"></a>Prérequis

### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Acceptez de participer à la préversion de la stratégie d’utilisation des données d’Azure Purview
Cette fonctionnalité étant actuellement en préversion, vous devez [accepter de participer à la préversion des stratégies d’utilisation des données d’Azure Purview](https://aka.ms/opt-in-data-use-policy).

### <a name="provision-new-accounts-in-an-isolated-test-subscription"></a>Approvisionner de nouveaux comptes dans un abonnement de test isolé
Suivez les étapes ci-dessous pour créer un compte Azure Purview et un compte Stockage Azure dans un abonnement de test isolé. Activez ensuite la fonctionnalité de stratégie d’accès dans ces comptes.

### <a name="supported-regions"></a>Régions prises en charge

> [!IMPORTANT]
> 1. La fonctionnalité de stratégie d’accès n’est disponible que sur de nouveaux comptes Azure Purview et Stockage Azure.
> 2. Cette fonctionnalité ne peut être utilisée que dans les régions répertoriées ci-dessous, où la fonctionnalité de stratégie d’accès est déployée.

#### <a name="azure-purview"></a>Azure Purview 

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


#### <a name="azure-storage"></a>Stockage Azure

-   France Centre
-   Centre du Canada


### <a name="create-azure-purview-account"></a>Créer un compte Azure Purview

Créez un compte Azure Purview dans les régions où la nouvelle fonctionnalité est activée, sous l’abonnement isolé pour la nouvelle fonctionnalité.

Pour créer un compte Purview, consultez [Démarrage rapide : Créer un compte Azure Purview dans le portail Azure](create-catalog-portal.md).

### <a name="create-azure-storage-account"></a>Créer un compte de stockage Azure

Pour créer un compte Stockage Azure, consultez [Créer un compte de stockage – Stockage Azure](../storage/common/storage-account-create.md).

### <a name="configure-azure-purview-and-storage-for-access-policies"></a>Configurer Azure Purview et Stockage Azure pour les stratégies d’accès

Cette section décrit les étapes permettant de configurer Azure Purview et Stockage Azure pour activer les stratégies d’accès.

#### <a name="register-the-access-policies-functionality-in-azure-storage"></a>Inscrire la fonctionnalité des stratégies d’accès dans Stockage Azure

Pour inscrire cette fonctionnalité et vérifier qu’elle est activée pour votre abonnement, exécutez les commandes suivantes dans PowerShell.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

Si la sortie de la dernière commande affiche la valeur de « RegistrationState » « Registered », cela signifie que votre abonnement est activé pour cette fonctionnalité.

#### <a name="register-and-scan-data-sources-in-purview"></a>Inscrire et analyser des sources de données dans Purview

Vous devez inscrire et analyser la source de données avec Purview afin de définir des stratégies. Pour inscrire votre compte de stockage, suivez les guides d’inscription de Purview :

-   [Comment analyser un Azure Storage Blob – Azure Purview](register-scan-azure-blob-storage-source.md)

-   [Inscrire et analyser Azure Data Lake Storage (ADLS) Gen2 – Azure Purview](register-scan-adls-gen2.md)

Pendant l’inscription, activez la source de données pour la gouvernance de l’utilisation des données, comme illustré dans l’image.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="Image montrant comment inscrire une source de données pour la stratégie.":::

#### <a name="configure-permissions-for-policy-management-actions"></a>Configurer des autorisations pour les actions de gestion de stratégie

-   Pour pouvoir effectuer des actions de création ou de gestion de stratégie, un utilisateur doit faire partie du rôle conservateur de données Purview.
-   Pour pouvoir publier la stratégie, un utilisateur doit faire partie du rôle administrateur de source de données Purview.

Consultez la section relative à la gestion des attributions de rôles dans le guide [Comment créer et gérer des collections](how-to-create-and-manage-collections.md).

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

1. Une liste de sources de données s’affiche. Vous pouvez entrer un nom pour la filtrer. Ensuite, sélectionnez chaque source de données dans laquelle cette stratégie doit être publiée, puis cliquez sur le bouton **Publier**. La publication est une opération en arrière-plan. Jusqu’à 2 heures peuvent s’écouler avant que les modifications soient reflétées dans la source de données.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="Image montrant comment un propriétaire de données peut sélectionner la source de données dans laquelle la stratégie sera publiée.":::

## <a name="next-steps"></a>Étapes suivantes

Consultez cet article pour comprendre les concepts liés à Azure Purview :

* [Vue d’ensemble d’Azure Purview](overview.md)
