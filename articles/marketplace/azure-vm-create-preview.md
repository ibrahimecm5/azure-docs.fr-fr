---
title: Ajouter un public en version préliminaire pour une offre de machine virtuelle sur la Place de marché Azure
description: Ajoutez une audience en préversion pour une offre de machine virtuelle sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.reviewer: amhindma
ms.date: 11/12/2021
ms.openlocfilehash: b4ae30a1573534fbf0ead8b913a6a397e9b0cd27
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400483"
---
# <a name="add-a-preview-audience-for-a-virtual-machine-offer"></a>Ajout d’une audience en préversion pour une offre de machine virtuelle

Dans la page **Audience en préversion** (sélectionnée dans le menu de navigation gauche d’Espace partenaires), configurez une *audience en préversion* qui sera autorisée à valider votre offre avant de la publier plus largement en ligne sur le marketplace commercial. Lorsque vous publiez votre offre, des liens de préversion sont mis à la disposition des membres de l’audience en préversion que vous avez spécifiée. Seule l’audience en préversion que vous configurez pourra accéder aux liens de préversion et vérifier les détails de la préversion de votre offre avant que vous ne la mettiez officiellement **en ligne**.

Les liens de préversion ne sont pas disponibles pour les plans masqués. Pour les plans masqués, l’audience en préversion peut tester l’offre via l’invite de commandes.

Votre audience en préversion est identifiée par des **ID d’abonnement Azure**, ainsi que par une **description** facultative pour chacun d’entre eux. Aucun de ces champs ne peut être vu par les clients. Vous pouvez trouver votre ID d’abonnement Azure dans la page **[Abonnements](https://go.microsoft.com/fwlink/?LinkId=2122490)** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, soit séparément, soit en chargeant un fichier CSV. Si votre offre est déjà publiée, vous pouvez toujours modifier l’audience en préversion afin de tester d’éventuelles modifications ou mises à jour.

> [!IMPORTANT]
> Toute modification apportée à l’audience en préversion de votre offre doit être enregistrée et prend effet uniquement après la republication de votre offre.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Une audience en préversion est une liste d’ID d’abonnement qui peuvent tester et valider votre offre. Cela comprend tous les plans privés, avant qu’ils ne soient mis à la disposition de vos utilisateurs. En revanche, lorsque vous rendez une offre privée, vous devez spécifier une audience privée pour limiter la visibilité de votre offre aux clients de votre choix. Une audience privée (définie sur la page **Tarification et disponibilité** pour chacun de vos plans) est une liste d’ID d’abonnement ou d’ID de locataire qui auront accès à un plan particulier après la mise en ligne de l’offre.

Après avoir configuré l’audience en préversion, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers l’onglet suivant du menu de navigation gauche, **Vue d’ensemble du plan**.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des plans](azure-vm-create-plans.md)
