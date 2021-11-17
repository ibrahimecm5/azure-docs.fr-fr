---
title: Que sont les connexions marquées d’un indicateur dans Azure Active Directory ?
description: Présente une vue d’ensemble des connexions marquées d’un indicateur dans Azure Active Directory.
author: MarkusVi
manager: karenhoran
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/27/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: 518c13780cf7de9e31f3a22e7ecb0440aa0128bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456040"
---
# <a name="what-are-flagged-sign-ins-in-azure-active-directory"></a>Que sont les connexions marquées d’un indicateur dans Azure Active Directory ?

En tant qu’administrateur informatique, lorsqu’un utilisateur n’arrive pas à se connecter, vous voulez résoudre le problème dès que possible pour débloquer la situation. En raison de la quantité des données disponibles dans le journal des connexions, il peut être difficile de trouver les bonnes informations.

Cet article vous donne une vue d’ensemble d’une fonctionnalité qui améliore considérablement le temps nécessaire pour résoudre les problèmes de connexion utilisateur en les rendant faciles à trouver.


## <a name="what-it-is"></a>Présentation

Les événements de connexion Azure AD sont essentiels pour comprendre ce qui s’est bien ou mal passé avec les connexions utilisateur et la configuration de l’authentification dans un locataire. Toutefois, Azure AD traite plus de 8 milliards d’authentifications par jour, ce qui peut aboutir à un nombre d’événements de connexion tellement élevé que les administrateurs peuvent trouver difficile de repérer ceux qui sont importants. En d’autres termes, un nombre élevé d’événements de connexion peut rendre le signal qu’envoient les utilisateurs qui ont besoin d’aide introuvable dans la masse des événements.

Les connexions marquées d’un indicateur sont une fonctionnalité conçue pour augmenter le rapport signal/bruit pour les connexions utilisateur demandant de l’aide. Cette fonctionnalité est conçue pour permettre aux utilisateurs d’attirer l’attention sur les erreurs de connexion pour lesquelles ils souhaitent obtenir de l’aide et, pour les administrateurs et les employés du support technique, de trouver les bons événements plus rapidement et plus efficacement. Les événements de connexion avec indicateur contiennent les mêmes informations que les autres événements de connexion, à un renseignement supplémentaire près : ils indiquent également qu’un utilisateur a marqué l’événement pour révision par les administrateurs.
 
Les connexions marquées d’un indicateur donnent à l’utilisateur la possibilité d’activer le marquage lorsqu’une erreur est détectée sur une page de connexion, puis de reproduire cette erreur. L’événement d’erreur apparaît alors comme « Marqué pour révision » dans le panneau Rapports Azure AD pour Connexions.

En résumé, vous pouvez utiliser des connexions avec indicateur pour :

- **Permettre** aux utilisateurs d’indiquer les erreurs de connexion pour lesquelles ils ont besoin de l’aide de leurs administrateurs de locataires.
- **Simplifier** le processus de localisation des erreurs de connexion qui doivent être résolues pour l’utilisateur.
- **Permettre** au personnel du support technique de trouver les problèmes pour lesquels les utilisateurs souhaitent obtenir de l’aide de manière proactive, sans que les utilisateurs finaux n’aient à faire autre chose que de marquer l’événement.

## <a name="how-it-works"></a>Fonctionnement

Les connexions marquées d’un indicateur vous permettent d’activer le marquage quand vous vous connectez en utilisant un navigateur et que vous recevez une erreur d’authentification. Lorsqu’un utilisateur voit une erreur de connexion, il peut choisir d’activer le marquage. Pendant les 20 prochaines minutes, tout événement de connexion de cet utilisateur, sur le même navigateur et le même appareil client ou ordinateur, affiche « Marqué pour révision : Oui » dans le rapport des connexions. Après 20 minutes, le marquage est automatiquement désactivé.

### <a name="user-how-to-flag-an-error"></a>Utilisateur : Comment marquer une erreur

1. L’utilisateur reçoit une erreur lors de la connexion.
2. L’utilisateur clique sur **Afficher les détails** dans la page d’erreur.
3. Dans **Détails de la résolution des problèmes**, cliquez sur **Activer le marquage**. Le texte change en **Désactiver le marquage**. Le marquage est maintenant activé.
4. Fermez la fenêtre du navigateur.
5. Ouvrez une nouvelle fenêtre de navigateur (dans la même application du navigateur) et essayez la même connexion qui a échoué. 
6.  Reproduisez l’erreur de connexion qui a été observée avant.

Après l’activation du marquage, la même application du navigateur et le même client doivent être utilisés, sinon les événements ne seront pas marqués.


### <a name="admin-find-flagged-events-in-reports"></a>Administrateur : Trouver les événements avec indicateur dans les rapports

1.  Dans le portail Azure AD, sélectionnez **Journaux de connexion** dans le volet gauche.
2.  Cliquez sur **Ajouter des filtres**.
3.  Dans le menu de filtre intitulé **Choisir un champ**, sélectionnez **Marqué pour révision** et cliquez sur **Appliquer**.
4.  Tous les événements qui ont été marqués par les utilisateurs s’affichent.
5.  Si nécessaire, appliquez des filtres supplémentaires pour affiner la vue des événements.
6.  Sélectionnez l’événement pour regarder ce qui s’est passé.


### <a name="admin-or-developer-find-flagged-events-using-ms-graph"></a>Administrateur ou développeur : Trouver les événements avec indicateur en utilisant MS Graph

Vous pouvez trouver les connexions marquées d’un indicateur avec une requête filtrée à l’aide de l’API de rapports de connexions. Voici un exemple de requête :
 
`https://graph.microsoft.com/beta/auditlogs/signins?&$filter=(flaggedForReview eq true)`

Pour plus d’informations sur l’utilisation de l’API Graph de connexions, consultez [Type de ressource signIn](/graph/api/resources/signin?view=graph-rest-1.0&preserve-view=true).



 
## <a name="who-can-create-it"></a>Qui peut les créer ?

Tout utilisateur qui se connecte à Azure AD via une page web peut utiliser des connexions avec indicateur pour révision. Les utilisateurs membres et invités peuvent marquer des erreurs de connexion pour révision. 

## <a name="who-can-review-it"></a>Qui peut les réviser ?

Pour examiner les événements de connexion avec indicateur, vous devez avoir des autorisations pour lire les événements des rapports de connexions dans le portail Azure AD. Pour plus d’informations, consultez [Qui peut y accéder ?](concept-sign-ins.md#who-can-access-it)


Pour marquer les échecs de connexion, vous n’avez pas besoin d’autorisations supplémentaires.


## <a name="what-you-should-know"></a>Ce que vous devez savoir 

Même si les noms sont similaires, les **connexions marquées d’un indicateur** et les **connexions risquées** sont des fonctionnalités différentes :

- Les connexions marquées d’un indicateur sont des événements d’erreur de connexion pour lesquels les utilisateurs demandent de l’aide. 
- Une connexion risquée est une fonctionnalité d’Identity Protection. Pour plus d’informations, consultez [Qu’est-ce qu’Identity Protection](../identity-protection/overview-identity-protection.md).




## <a name="next-steps"></a>Étapes suivantes

- [Journaux des connexions dans Azure Active Directory](concept-sign-ins.md)
- [Diagnostics de connexion pour les scénarios de Azure AD](concept-sign-in-diagnostics-scenarios.md)
