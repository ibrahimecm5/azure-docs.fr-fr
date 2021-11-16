---
title: 'Démarrage rapide : Registre confidentiel Microsoft Azure à l’aide du portail Azure'
description: En savoir plus sur l’utilisation de Registre confidentiel Microsoft Azure via le portail Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/18/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 265dfc023d0adf384eb483efc16a40380e395fb3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458149"
---
# <a name="quickstart-create-a-confidential-ledger-using-the-azure-portal"></a>Démarrage rapide : Créer un registre confidentiel à l’aide du portail Azure

Registre confidentiel Azure est un service cloud qui fournit un magasin d’intégrité élevée pour les journaux de données sensibles et les enregistrements qui exigent que les données restent intactes. Pour plus d’informations sur Registre confidentiel Azure et pour obtenir des exemples de ce qui peut être stocké dans un registre confidentiel, consultez [À propos de Registre confidentiel Microsoft Azure](overview.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Dans ce guide de démarrage rapide, vous allez créer un registre confidentiel avec le [portail Azure](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-confidential-ledger"></a>Créer un registre confidentiel

1. Dans le menu du Portail Azure ou dans la page Accueil, sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, entrez « registre confidentiel ».

1. Dans la liste des résultats, choisissez **registre confidentiel**.

1. Dans la section Registre confidentiel, choisissez **Créer**.

1. Dans la section Créer un registre confidentiel, renseignez les informations suivantes :
    - **Nom** : indiquez un nom unique pour votre registre confidentiel.
    - **Abonnement**: Choisissez un abonnement.
    - **Groupe de ressources** : sélectionnez **Créer un nouveau***, puis entrez un nom de groupe de ressources.
    - **Emplacement** : dans le menu déroulant, choisissez un emplacement.
    - Conservez les valeurs par défaut des autres options.
   
1. Sélectionnez l'onglet **Sécurité** .

1. Vous devez maintenant ajouter un utilisateur Azure AD ou un utilisateur basé sur certificat à votre registre confidentiel avec le rôle « Administrateur ». Dans ce guide de démarrage rapide, nous allons ajouter un utilisateur Azure AD. Sélectionnez **+ Ajouter un utilisateur AAD**.

1. Vous devez ajouter un utilisateur Azure AD ou un utilisateur basé sur certificat. Recherchez votre adresse e-mail dans le volet de droite. Sélectionnez votre ligne, puis choisissez **Sélectionner** en bas du volet.

1. Dans le champ déroulant **Rôle de registre**, sélectionnez **Administrateur**.

1. Sélectionnez **Vérifier + créer**. Une fois la validation réussie, sélectionnez **Créer**.

Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

:::image type="content" source="./media/confidential-ledger-portal-quickstart.png" alt-text="Écran de création dans le portail ACL":::

Notez les deux propriétés ci-dessous :
- **Nom du registre confidentiel** : dans l’exemple, il s’agit de « test-create-ledger-demo ». Vous allez utiliser ce nom pour les autres étapes.
- **Point de terminaison de registre** : dans l’exemple, ce point de terminaison est `https://test-create-ledger-demo.confidential-ledger.azure.net/`. 

Vous aurez besoin de ces noms de propriétés pour effectuer des transactions avec le registre confidentiel à partir du plan de données.
 
## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres articles relatifs à Registre confidentiel Azure se basent sur ce démarrage rapide. Si vous prévoyez de poursuivre avec les articles suivants, il peut être utile de conserver ces ressources. 

Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le registre confidentiel et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1.  Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.

1.  Sélectionnez **Supprimer le groupe de ressources**.

1.  Dans le champ **Taper le nom du groupe de ressources**, entrez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un registre confidentiel à l’aide du portail Azure. Pour en savoir plus sur Registre confidentiel Azure et sur la manière de l’intégrer à vos applications, poursuivez votre lecture des articles ci-dessous.

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)
