---
title: Créer une offre de machine virtuelle Azure sur la Place de marché Azure à l’aide de votre propre image
description: Publier une offre de machine virtuelle sur la Place de marché Azure à l’aide de votre propre image.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 11/10/2021
ms.openlocfilehash: 9aa76bf3c26043a93407ee1543225028e795bb9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315366"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>Créer une machine virtuelle à l’aide de votre propre image

Cet article explique comment publier une image de machine virtuelle que vous avez créée localement.

## <a name="bring-your-image-into-azure"></a>Intégrer votre image dans Azure

Chargez votre disque dur virtuel vers Azure Compute Gallery (anciennement Galerie d’images partagées).

1. Dans le portail Azure, recherchez **Galeries Azure Compute**.
2. Créez ou utilisez une instance Azure Compute Gallery existante. Nous vous suggérons de créer une instance Azure Compute Gallery distincte pour les images publiées sur la place de marché.
3. Créez ou utilisez une définition d’image existante.
4. Sélectionnez **Créer une version**.
5. Choisissez la région et la version de l’image.
6. Si votre disque dur virtuel n’est pas encore chargé dans le portail Azure, sélectionnez **Objets blob de stockage (disques durs virtuels)** en tant que **Source**, puis **Parcourir**. Vous pouvez créer un **compte de stockage**, de même qu’un **conteneur de stockage** si ce n’est pas encore fait. Chargez votre disque dur virtuel.
7. Sélectionnez **Revoir + créer**. Une fois la validation réussie, sélectionnez **Créer**.

> [!TIP]
> Le compte de l’éditeur doit avoir un accès « Propriétaire » pour publier l’image Azure Compute Gallery. Si nécessaire, suivez la procédure décrite dans la section suivante, **Définir les autorisations appropriées** pour accorder l’accès.

## <a name="set-the-right-permissions"></a>Définir les autorisations appropriées

Si votre compte Espace partenaires est le propriétaire de l’abonnement qui héberge Azure Compute Gallery, rien d’autre n’est nécessaire pour les autorisations.

Si vous disposez uniquement d’un accès en lecture à l’abonnement, utilisez l’une des deux options suivantes.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>Option 1 : Demander au propriétaire d’accorder son autorisation

Étapes à suivre pour que le propriétaire accorde son autorisation :

1. Accédez à la galerie Azure Compute Gallery.
2. Sélectionnez **Contrôle d’accès** (IAM) dans le volet gauche.
3. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**.<br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="La fenêtre Ajouter une attribution de rôle s’affiche":::.
1. Sous **Rôle**, sélectionnez **Propriétaire**.
1. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**.
1. Pour **Sélectionner**, entrez l’adresse e-mail Azure de la personne qui publiera l’image.
1. Sélectionnez **Enregistrer**.

### <a name="option-two--run-a-command"></a>Option 2 : Exécuter une commande

Demandez au propriétaire d’exécuter l’une de ces commandes (dans les deux cas, utilisez le SusbscriptionId de l’abonnement où vous avez créé la galerie Azure Compute Gallery).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> Vous n’avez pas besoin de générer d’URI de SAP, car vous pouvez désormais publier une image provenant d’une galerie Azure Compute Gallery sur Espace partenaires sans utiliser d’API. <br/> <br/>Si vous *publiez* avec des API, vous devez générer des URI SAS au lieu d’utiliser Azure Compute Gallery, consultez [Guide pratique pour générer un URI SAS pour une image de machine virtuelle](azure-vm-get-sas-uri.md).

## <a name="next-steps"></a>Étapes suivantes

- [Testez votre image de machine virtuelle](azure-vm-image-test.md) pour vous assurer qu’elle répond aux exigences de publication de la Place de marché Azure (facultatif).
- Si vous ne souhaitez pas tester votre image de machine virtuelle, connectez-vous à l’[Espace partenaires](https://go.microsoft.com/fwlink/?linkid=2165935) et publiez l’image Azure Compute Gallery.
- Si vous rencontrez des difficultés lors de la création de votre nouveau disque dur virtuel basé sur Azure, consultez [Forum aux questions sur la Place de marché Microsoft Azure](azure-vm-create-faq.yml).
