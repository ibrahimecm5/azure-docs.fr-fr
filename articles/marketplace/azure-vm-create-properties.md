---
title: Configurer les propriétés d’une offre de machine virtuelle sur Place de marché Azure
description: Configurer les propriétés d’une offre de machine virtuelle sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
msreviewer: amhindma
ms.date: 11/10/2021
ms.openlocfilehash: 44f500f59169398910b7ede32ebb1c96ecf6fdbc
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134889"
---
# <a name="configure-virtual-machine-offer-properties"></a>Configurer les propriétés de l’offre de machine virtuelle

Dans la page **Propriétés** (à partir du menu de navigation de gauche dans Espace partenaires), définissez les catégories utilisées pour regrouper votre offre de machine virtuelle sur Place de marché Azure et les contrats légaux associés.

## <a name="select-a-category"></a>Sélectionner une catégorie

Sélectionnez des catégories et des sous-catégories pour placer votre offre dans les zones de recherche appropriées sur Place de marché Azure. Veillez à décrire ultérieurement dans la description de l’offre la façon dont votre offre prend en charge ces catégories.

- Sélectionnez une catégorie principale.
- Pour ajouter une deuxième catégorie facultative (secondaire), sélectionnez le lien **+ Catégories** .
- Sélectionnez jusqu’à deux sous-catégories pour la catégorie principale et/ou secondaire. Utilisez Ctrl + clic pour sélectionner une deuxième sous-catégorie. Si aucune sous-catégorie n’est applicable à votre offre, l’option **Non applicable** est automatiquement sélectionnée.

Consultez la liste complète des catégories et sous-catégories dans [Catégories et sous-catégories dans la Place de marché commerciale](categories.md). Les offres de machines virtuelles apparaissent toujours sous la catégorie **Calcul** sur la Place de marché Azure.

## <a name="provide-terms-and-conditions"></a>Entrer des conditions d’utilisation

Sous **Juridique**, entrez les conditions générales de votre offre. Deux options s'offrent à vous :

- [Utiliser le contrat standard avec des avenants facultatifs](#use-the-standard-contract)
- [Utiliser vos propres conditions générales](#use-your-own-terms-and-conditions)

Pour découvrir le contrat standard et les avenants facultatifs, consultez [Contrat standard pour la place de marché commerciale de Microsoft](standard-contract.md). Vous pouvez télécharger le fichier PDF [Contrat Standard](https://go.microsoft.com/fwlink/?linkid=2041178) (assurez-vous que le bloqueur de fenêtres publicitaires est désactivé).

### <a name="use-the-standard-contract"></a>Utiliser le contrat standard

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard que vous pouvez utiliser pour vos offres dans la place de marché commerciale. Lorsque vous proposez votre logiciel dans le cadre du contrat standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées. Pour en savoir plus sur l’expérience client pendant le processus de découverte et d’achat, consultez [Contrat standard pour le marketplace commercial de Microsoft](standard-contract.md#customer-experience).

1. Activez la case à cocher **Utiliser le contrat Standard pour le marketplace commercial de Microsoft**.

1. Dans la boîte de dialogue **Confirmation**, sélectionnez **Accepter**. Selon la taille de votre écran, vous devrez peut-être faire défiler la page vers le haut pour la voir.
1. Sélectionnez **Enregistrer le brouillon** avant de continuer.

   > [!NOTE]
   > Après avoir publié une offre à l’aide du contrat standard pour la place de marché commerciale, vous ne pouvez pas utiliser vos propres conditions générales. Proposez votre solution avec le contrat standard assorti d’avenants facultatifs ou vos propres conditions générales.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Ajouter des avenants au contrat standard (facultatif)

Deux types d’avenants sont disponibles : *universel* et *personnalisé*.

##### <a name="add-universal-amendment-terms"></a>Ajouter des termes d’avenant universel

Dans la zone **Termes d’avenant universel au contrat standard pour la place de marché commerciale de Microsoft**, entrez les termes de votre avenant universel. Vous pouvez entrer un nombre illimité de caractères dans cette zone. Ces termes sont affichés pour les clients dans AppSource, la Place de marché Azure et/ou le portail Azure pendant le processus de découverte et d’achat.

##### <a name="add-one-or-more-custom-amendments"></a>Ajouter une ou plusieurs avenants personnalisés

Les modifications personnalisées sont des avenants spéciaux du contrat standard qui sont destinés à des clients spécifiques utilisant des ID de locataire Azure. Seuls les clients du locataire recevront les conditions d’avenant personnalisées lors de l’achat de l’offre.

> [!NOTE]
> Les clients ciblés par les modifications personnalisées reçoivent aussi la modification universelle apportée (le cas échéant) au contrat Standard lors de l’achat.

1. Sous **Termes d’avenants personnalisés au contrat standard pour la place de marché commerciale de Microsoft**, sélectionnez le lien **Ajouter des termes d’avenant personnalisé (max 10)** .
2. Saisissez vos **conditions d’amendement personnalisées** dans la zone concernée.
3. Entrez l’**ID de locataire** dans la zone.

   > [!TIP]
   > L’ID d’un locataire identifie votre client dans Azure. Vous pouvez demander cet ID à votre client et le trouver en accédant à [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **Propriétés**. La valeur d’ID d’annuaire est l’ID de locataire (par exemple, `50c464d3-4930-494c-963c-1e951d15360e`). Vous pouvez également Rechercher l’ID de locataire de l’organisation de votre client à l’aide de son URL de nom de domaine sur [Qu’est-ce que mon Microsoft Azure et mon ID de locataire Office 365 ?](https://www.whatismytenantid.com/).

4. Si vous le souhaitez, entrez une **description** conviviale de l’ID de locataire. Cette description vous aide à identifier le client que vous ciblez avec l’avenant.
5. Pour ajouter un autre ID de locataire, sélectionnez le lien **Ajouter l’ID de locataire d’un client (10 max.)** , puis répétez les étapes 3 et 4. Vous pouvez ajouter jusqu’à 10 ID de locataire.
6. Pour ajouter un autre terme d’avenant, répétez les étapes 1 à 5. Vous pouvez fournir jusqu’à 10 termes d’amendement personnalisés par offre.
7. Sélectionnez **Enregistrer le brouillon** avant de continuer.

### <a name="use-your-own-terms-and-conditions"></a>Utiliser vos propres conditions générales

Vous pouvez fournir vos propres conditions générales au lieu d’utiliser le contrat standard. Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

1. Sous **Juridique**, désactivez la case à cocher **Utiliser le contrat Standard pour le marketplace commercial de Microsoft**.
1. Dans la zone **Conditions générales**, entrez jusqu’à 10 000 caractères de texte.

   > [!NOTE]
   > Si vous avez besoin d’une description plus longue, entrez une adresse web unique pointant vers l’emplacement où se trouvent vos conditions générales. Elle sera présentée aux clients sous forme de lien actif.

1. Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation gauche **Référencement de l’offre**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer l’annonce d’une offre de machine virtuelle](azure-vm-create-listing.md)
