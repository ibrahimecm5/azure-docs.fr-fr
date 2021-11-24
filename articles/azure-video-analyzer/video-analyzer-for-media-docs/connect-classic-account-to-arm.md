---
title: Connecter un compte Azure Video Analyzer for Media classique à ARM
description: Cette rubrique explique comment connecter un compte Azure Video Analyzer for Media payant classique existant à un compte basé sur ARM.
ms.topic: how-to
ms.author: itnorman
ms.date: 10/19/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14ab4e2b3c95823359dc1f1f88b8368ce58afef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312592"
---
# <a name="connect-an-existing-classic-paid-video-analyzer-for-media-account-to-arm-based-account"></a>Connecter un compte Azure Video Analyzer for Media payant classique existant à un compte basé sur ARM  

Cet article explique comment connecter un compte Azure Video Analyzer for Media payant classique existant à un compte basé sur Azure Resource Manager (ARM).
Aujourd’hui, Azure Video Analyzer for Media (anciennement Video Indexer) est un produit en disponibilité générale qui n’est pas une ressource ARM sur Azure.
Dans cet article, nous allons passer en revue les options de connexion de votre compte Video Analyzer for Media **existant** à [ARM][docs-arm-overview].

## <a name="prerequisites"></a>Prérequis

* Compte Video Analyzer for Media payant illimité (compte classique).
  * Pour effectuer la connexion à ARM, vous devez disposer d’autorisations de propriétaire sur le compte Video Analyzer for Media.
* Abonnement Azure.
* Identité managée affectée par l’utilisateur (peut être créée dans le flux).

#### <a name="transition-state-and-recommended-steps-before-connecting-a-classic-account-to-be-arm-based"></a>État de transition et étapes recommandées avant la connexion d’un compte classique pour le convertir en compte basé sur ARM

Dans le processus de connexion, la gestion du compte est connectée à ARM qui déclenchera 30 jours d’état de transition pour le compte. Dans cet état, un compte connecté est accessible via l’API par un jeton d’accès [généré via Gestion des API](https://aka.ms/avam-dev-portal) (méthode classique) ou par un jeton d’accès généré via ARM. L’état de transition qui déplace toute la gestion de compte vers ARM désactivera la fonctionnalité d’invitation de l’utilisateur du portail Video Analyzer for Media, car la gestion de comptes sera traitée par [RBAC Azure][docs-rbac-overview]. En conséquence, tous les utilisateurs invités sur ce compte perdent leur accès au portail du compte Video Analyzer for Media. Bien entendu, vous pouvez facilement résoudre ce problème en affectant l’attribution de rôle appropriée à tous ces utilisateurs via un RBAC Azure [Comment attribuer un RBAC][docs-rbac-assignment]. Seul le propriétaire du compte, qui a effectué l’action de connexion, est automatiquement affecté en tant que propriétaire sur le compte connecté. Si les utilisateurs ne sont pas ajoutés au compte via un RBAC Azure, au bout de 30 jours, ils perdent également l’accès via API car, une fois l’état de transition terminé, aucun utilisateur ne peut plus générer de jeton d’accès valide via APIM (mode classique), mais uniquement via ARM. Faire du RBAC Azure la seule méthode pour gérer le contrôle d’accès en fonction du rôle sur le compte.

> [!NOTE]
> S’il y a des utilisateurs invités dont vous souhaitez supprimer l’accès avant la fin des 30 jours d’état de transition, vous devez le faire via les paramètres du compte Azure Video Analyzer for Media **avant** la connexion du compte à ARM. 

## <a name="get-started"></a>Bien démarrer

### <a name="browse-to-video-analyzer-for-media-portal"></a>Accéder au [portail Video Analyzer for Media](https://aka.ms/vi-portal-link)

1. Connectez-vous à l’aide de votre compte AAD.
1. Dans la barre supérieure droite, appuyez sur *Compte d’utilisateur* pour ouvrir la liste des comptes du volet latéral.
3. Sélectionnez le compte Video Analyzer for Media classique que vous souhaitez connecter à ARM (les comptes classiques sont marqués d’une *étiquette Classic*).
4. Cliquez sur **Paramètres**.

  ![account-settings](media/connect-classic-account-to-arm/user-account-settings.png)
   
5. Cliquez sur **Se connecter à un compte basé sur ARM**.

  ![connect-button-portal](media/connect-classic-account-to-arm/connect-button.png)

7. Connectez-vous au portail Azure
8. Le panneau Créer de Video Analyzer for Media s’ouvre.
10. Dans la section **Créer un compte Video Analyzer for Media**, entrez les valeurs requises.
    * Si vous avez suivi les étapes, les champs devraient être remplis automatiquement. Veillez à valider les valeurs éligibles.

 ![connect-to-arm](media/connect-classic-account-to-arm/connect-blade-new.png)

 | Nom | Description |
 | ---|---|
 |**Abonnement**| L’abonnement contient actuellement le compte classique et d’autres ressources associées telles que les Media Services.|
 |**Groupe de ressources**|Sélectionnez une ressource existante ou créez-en une. Le groupe de ressources doit être dans le même emplacement que le compte classique en cours de connexion|
 |**Compte Video Analyzer for Media** (case d’option)| Sélectionnez l’option *Connexion d’un compte classique existant*.|
 |**ID de compte existant**| Entrez l’ID du compte Video Analyzer for Media classique existant.|
 |**Nom de la ressource**|Entrez le nom du nouveau compte Video Analyzer for Media. La valeur par défaut devrait être le nom du compte classique.|
 |**Lieu**|Vous pouvez modifier la région géographique dans le processus de connexion, mais le compte connecté doit rester dans la même région. |
 |**Nom du compte Media Services**|Nom du compte Media Services d’origine qui associé au compte classique.|
 |**Identité managée affectée par l’utilisateur**|Sélectionnez une identité managée affectée par l’utilisateur ou créez-en une. Le compte Video Analyzer for Media l’utilisera pour accéder aux Media Services. L’identité managée affectée par l’utilisateur se verra attribuer les rôles de contributeur pour le compte de service multimédia.|

1. Au bas du formulaire, cliquez sur **Examiner et créer**.

### <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Charger une vidéo à l’aide de C#](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/ApiUsage/ArmBased).
  
<!-- links -->
[docs-arm-overview]: ../../azure-resource-manager/management/overview.md
[docs-rbac-overview]: ../../role-based-access-control/overview.md
[docs-rbac-assignment]: ../../role-based-access-control/role-assignments-portal.md
