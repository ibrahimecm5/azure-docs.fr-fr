---
title: BYOS (apportez votre propre stockage) Azure Monitor Workbooks
description: Découvrez comment sécuriser votre classeur en enregistrant son contenu dans votre stockage.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: af32a91344d2201899757d806019f70eccd736ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072272"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>BYOS (apportez votre propre stockage) pour enregistrer des classeurs

Il est parfois nécessaire de sécuriser une requête ou une logique métier. Workbooks vous offre la possibilité de sécuriser les classeurs en enregistrant leur contenu dans votre stockage. Le compte de stockage peut ensuite être chiffré à l’aide de clés gérées par Microsoft. Sinon, vous pouvez gérer le chiffrement en fournissant vos propres clés. [Consultez la documentation Azure sur Storage Service Encryption.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Enregistrement d’un classeur avec des identités managées

1. Pour pouvoir enregistrer le classeur dans votre stockage, vous devez créer une identité managée (Tous les services > Identités gérées) et lui accorder l’accès `Storage Blob Data Contributor` à votre compte de stockage. [Consultez la documentation Azure sur les identités managées.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Capture d’écran montrant comment ajouter une attribution de rôle](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Créer un classeur.
3. Sélectionnez le bouton **Enregistrer** pour enregistrer le classeur.
4. Il existe une option `Save content to an Azure Storage Account`. Cochez la case pour enregistrer le classeur dans un compte de stockage Azure.

    ![Capture d’écran montrant la boîte de dialogue **Enregistrer**.](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Sélectionnez le compte de stockage et le conteneur souhaités. La liste des comptes de stockage dépend de l’abonnement sélectionné au-dessus.

    ![Capture d’écran montrant la boîte de dialogue **Enregistrer** avec l’option de stockage.](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Sélectionnez ensuite **Modifier** pour choisir une identité managée déjà créée.

    [![Capture d’écran de la boîte de dialogue Modifier l’identité](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Une fois que vous avez sélectionné vos options de stockage, appuyez sur **Enregistrer** pour enregistrer votre classeur.

## <a name="limitations"></a>Limites

- En cas d’enregistrement dans un stockage personnalisé, il n’est pas possible d’épingler certaines parties du classeur à un tableau de bord, car les différentes épingles contiendraient des informations protégées dans le tableau de bord lui-même. Seuls des liens vers le classeur peuvent dans ce cas être épinglés dans des tableaux de bord.
- Un classeur qui a été enregistré dans un stockage personnalisé le sera toujours. Il n’est pas possible de désactiver ce comportement. Pour l’enregistrer ailleurs, vous pouvez utiliser « Enregistrer sous » et choisir de ne pas enregistrer la copie dans le stockage personnalisé.
- Les classeurs de la ressource Application Insights sont des classeurs « hérités » qui ne prennent pas en charge le stockage personnalisé. Les derniers classeurs de la ressource Application Insights sont ceux de la sélection « … Plus ». Les classeurs hérités ne proposent pas d’options Abonnement lors de l’enregistrement.

   ![Capture d’écran d’un classeur hérité](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment créer une visualisation [Carte](workbooks-map-visualizations.md) dans des classeurs.
- Découvrez comment utiliser des [groupes dans des classeurs](../visualize/workbooks-groups.md).