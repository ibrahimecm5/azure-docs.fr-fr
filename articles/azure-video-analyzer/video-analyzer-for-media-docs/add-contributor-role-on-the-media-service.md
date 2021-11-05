---
title: Ajouter un rôle Contributeur sur la plateforme Media Services
description: Cette rubrique explique comment ajouter un rôle Contributeur sur la plateforme Media Services.
ms.author: itnorman
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 294587e9f502a7f927bb905fbd354ab588955a85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097785"
---
# <a name="add-contributor-role-on-the-media-services"></a>Ajouter un rôle Contributeur sur la plateforme Media Services

Cet article explique comment attribuer un rôle Contributeur sur la plateforme Media Services.

> [!NOTE]
> Si vous créez votre Azure Video Analyzer for Media via l’interface utilisateur du portail Azure, l’identité managée sélectionnée est automatiquement attribuée avec une autorisation de contributeur sur le compte de service multimédia sélectionné.

## <a name="prerequisites"></a>Prérequis

1. Azure Media Services (AMS)
2. Identité managée affectée par l’utilisateur
> [!NOTE]
> Vous allez avoir besoin d’un abonnement Azure dans lequel vous avez accès aux rôles [Contributeur][docs-role-contributor] et [Administrateur][docs-role-administrator] pour Azure Media Services et l’identité managée affectée par l’utilisateur. Si vous ne disposez pas des autorisations appropriées, demandez à l’administrateur de compte qu’il vous les octroie. Les services Azure Media Services associés doivent se trouver dans la même région que le compte Video Analyzer for Media.


## <a name="add-contributor-role-on-the-media-services"></a>Ajouter un rôle Contributeur sur la plateforme Media Services
### <a name="azure-portal"></a>[Portail Azure](#tab/portal/)

### <a name="add-contributor-role-on-the-media-services-in-the-azure-portal"></a>Ajouter un rôle Contributeur sur la plateforme Media Services dans le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
    * Dans la barre de recherche située en haut, entrez **Media Services**.
    * Recherchez et sélectionnez votre ressource de service multimédia.
1. Dans le volet de gauche, cliquez sur **Contrôle d’accès (IAM)** .
    * Cliquez sur **Ajouter** > **Ajouter une attribution de rôle**. Si vous n’avez pas les autorisations pour attribuer des rôles, l’option **Ajouter une attribution de rôle** sera désactivée.
1. Dans la liste Rôle, sélectionnez rôle [Collaborateur][docs-role-contributor], puis cliquez sur **suivant**.
1. Dans **Attribuer l’accès à**, sélectionnez la case d’option *Identité managée*.
    * Cliquez sur le bouton **+ Sélectionner des membres**. Le volet **Sélectionner des identités managées** devrait apparaître.
1. **Sélectionnez** les éléments suivants :
    * Dans **Abonnement**, sélectionnez l’abonnement dans lequel se trouve l’identité managée.
    * Dans **Identité managée**, sélectionnez *Identité managée affectée par l’utilisateur*.
    * Dans la section **Sélectionner**, recherchez l’identité managée pour laquelle vous souhaitez accorder des autorisations de contributeur sur la ressource Media Services.    
1. Cliquez sur le principal de sécurité trouvé pour le sélectionner.
1. Pour attribuer le rôle, cliquez sur **Vérifier + attribuer**.

<!-- links -->
[docs-role-contributor]: ../../role-based-access-control/built-in-roles.md#contributor
[docs-role-administrator]: ../../role-based-access-control/built-in-roles.md#user-access-administrator
