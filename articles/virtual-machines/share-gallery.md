---
title: Partager une galerie à l’aide du contrôle d’accès en fonction du rôle
description: Découvrez comment partager une galerie à l’aide du contrôle d’accès en fonction du rôle.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 373ea9917fb4386ee9b977f64b003eb24a2803fc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462990"
---
# <a name="use-rbac-to-share-gallery-resources"></a>Utiliser le RBAC pour partager des ressources de galerie

La galerie Azure Compute Gallery, les définitions et les versions sont des ressources qui peuvent être partagées à l’aide des contrôles RBAC Azure intégrés. Azure RBAC permet de partager ces ressources avec d’autres utilisateurs, principaux de service et groupes. Vous pouvez même partager l’accès avec des personnes en dehors du locataire au sein duquel ils ont été créés. Un utilisateur ayant accès à la version de l’image ou de l’application peut déployer une machine virtuelle ou un groupe de machines virtuelles identiques.  

Pour une expérience optimale, nous vous recommandons de partager au niveau de la galerie. Nous vous déconseillons de partager des versions d’images ou d’applications individuelles. Pour plus d’informations sur Azure RBAC, consultez [Attribution de rôles Azure](../role-based-access-control/role-assignments-portal.md).

Si l’utilisateur se trouve en dehors de votre organisation, il recevra un message d’invitation à rejoindre l’organisation. L’utilisateur doit accepter l’invitation, puis il pourra voir la galerie et toutes les définitions et versions d’image versions dans sa liste de ressources.

## <a name="share-a-gallery"></a>Partager une galerie

### <a name="portal"></a>[Portail](#tab/portal)

Si l’utilisateur se trouve en dehors de votre organisation, il recevra un message d’invitation à rejoindre l’organisation. L’utilisateur doit accepter l’invitation. Il pourra ensuite voir la galerie ainsi que l’ensemble des définitions et versions dans sa liste de ressources.

1. Sur la page de votre galerie d’images, dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** . 
1. Sous **Ajouter une attribution de rôle**, sélectionnez **Ajouter**. Le volet **Ajouter une attribution de rôle** s’ouvre. 
1. Sous **Rôle**, sélectionnez **Lecteur**.
1. Sous **Attribuer l’accès à**, laissez la valeur par défaut **Utilisateur, groupe ou principal du service Azure AD**.
1. Sous **Sélectionner**, saisissez l’adresse de messagerie de la personne que vous souhaitez inviter.
1. Si l’utilisateur se trouve en dehors de votre organisation, vous verrez le message **Cet utilisateur recevra un e-mail qui lui permettra de collaborer avec Microsoft.** Sélectionnez l’utilisateur avec son adresse e-mail, puis cliquez sur **Enregistrer**.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Pour obtenir l’ID d’objet de votre galerie, utilisez [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilisez l’ID d’objet en tant qu’étendue, ainsi qu’une adresse e-mail et la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour donner aux utilisateurs l’accès à la galerie Azure Compute Gallery. Remplacez `<email-address>` et `<gallery iD>` par vos propres informations.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Pour plus d’informations sur le partage de ressources à l’aide de RBAC, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](../role-based-access-control/role-assignments-cli.md).

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Utilisez une adresse e-mail et la cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) pour obtenir l’ID d’objet de l’utilisateur, puis utilisez [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) pour leur donner accès à la galerie. Remplacez l’exemple d’e-mail, alinne_montes@contoso.com dans cet exemple, par vos propres informations.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>Étapes suivantes

Créez une [définition d’image et une version d’image](image-version.md).

Vous pouvez également créer des ressources Azure Compute Gallery en utilisant des modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une galerie Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
