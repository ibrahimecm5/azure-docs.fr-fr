---
title: Créer une offre Dynamics 365 for Operations sur Microsoft AppSource (Place de marché Azure)
description: Créez une offre Dynamics 365 for Operations sur Microsoft AppSource (Place de marché Azure).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 7b155c07edebf80ee45467829c57024d0ad2845f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552277"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Créer une offre Dynamics 365 for Operations

Cet article explique comment créer une offre [Dynamics 365 for Operations](https://dynamics.microsoft.com/finance-and-operations). Ce type d’offre est un service ERP (Enterprise Resource Planning) qui gère les aspects avancés des opérations, de la finance, de la fabrication et de la gestion de la chaîne logistique. Toutes les offres pour Dynamics 365 passent par notre processus de certification.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](./create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial.

## <a name="before-you-begin"></a>Avant de commencer

Examinez [Planifier une offre Dynamics 365](marketplace-dynamics-365.md). Cet article explique les exigences techniques de cette offre et répertorie les informations et ressources dont vous aurez besoin pour la créer.

## <a name="create-a-new-offer"></a>Créer une offre

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vues d’espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2166002).

1. Dans la page d’accueil, sélectionnez la vignette **Offres de la Place de marché**.

    [ ![Illustre la vignette Offres de la Place de marché sur la page d’accueil de l’Espace partenaires.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Sur la page Offres de la Place de marché, sélectionnez **+ Nouvelle offre** > **Dynamics 365 for Operations**.

    [ ![Capture d’écran du bouton « Nouvelle offre » sur la page des offres de la Place de marché, mettant en évidence le type d’offre Dynamics 365 for Operations.](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-operations-workspaces.png#lightbox)

> [!IMPORTANT]
> Après publication d’une offre, les modifications que vous y apportez dans Espace partenaires apparaissent sur Microsoft AppSource uniquement après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
1. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Dynamics 365 for Operations**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-operations.png" alt-text="Options du menu du volet gauche et bouton « Nouvelle offre »":::

> [!IMPORTANT]
> Après publication d’une offre, les modifications que vous y apportez dans Espace partenaires apparaissent sur Microsoft AppSource uniquement après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

---

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre et dans les modèles Azure Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur AppSource. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.
- Ce nom ne peut pas être modifié après que vous ayez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="alias"></a>Alias

Entrez un nom descriptif que nous utiliserons pour faire référence à cette offre uniquement dans l’Espace partenaires. L’alias de l’offre (pré-rempli avec les informations que vous avez entrées lors de la création de l’offre) n’est pas utilisé dans la Place de marché et est différent du nom de l’offre présenté aux clients. Si vous souhaitez mettre à jour le nom de l’offre par la suite, consultez la page [Annonce de l’offre](dynamics-365-operations-offer-listing.md).

## <a name="setup-details"></a>Détails de la configuration

Pour **Comment voulez-vous qu’interagissent les clients avec l’offre de liste ?** , sélectionnez **Me contacter**.

Collectez des informations de contact client en connectant votre système de gestion de la relation client (CRM). Le client devra autoriser le partage de ses informations. Ces informations client, ainsi que le nom de l’offre, son ID et la place de marché sur laquelle le client a trouvé votre offre, seront envoyées au système CRM que vous avez configuré. Pour plus d’informations sur la configuration de votre CRM, consultez [Prospects](#customer-leads).

## <a name="test-drive"></a>Test drive

Une version d’évaluation constitue un excellent moyen de présenter votre offre à des clients potentiels en leur donnant accès à un environnement préconfiguré pendant un nombre fixe d’heures. L’offre d’une version d’évaluation entraîne un taux de conversion accru et génère des prospects hautement qualifiés. Pour plus d’informations, commencez par [Qu’est-ce qu’une version d’évaluation ?](what-is-test-drive.md)

> [!TIP]
> Une version d’évaluation n’est pas un essai gratuit. Vous pouvez proposer une version d’évaluation, un essai gratuit ou les deux. Dans les deux cas, vos clients disposent de votre solution pendant une période fixe. Cependant, une version d’évaluation propose en outre une visite autoguidée concrète des principales fonctionnalités de votre produit, ainsi qu’une démonstration des avantages de celui-ci au travers d’un scénario d’implémentation réel.

Pour activer une version d’évaluation, cochez la case **Activer une version d’évaluation** et sélectionnez le **type de version d’évaluation**. Vous configurerez ultérieurement la version d'évaluation. Avec la version d’évaluation, vous devez également configurer votre offre auprès d’un système CRM pour les prospects (voir la section suivante). Pour supprimer une version d'évaluation de votre offre, désactivez cette case à cocher.

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Pour plus d’informations, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="business-applications-isv-program"></a>Programme Business Applications ISV

Votre offre est initialement inscrite au niveau Standard.

Avant de publier votre offre, complétez le [module de covente](https://aka.ms/BizAppsISVProgram).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- Configurer les [propriétés](dynamics-365-operations-properties.md) de l’offre
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)
