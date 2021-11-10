---
title: Gérer le cycle de vie des licences basées sur des groupes dans Azure AD
description: Ce tutoriel pas à pas montre comment créer un package d’accès pour gérer les licences basées sur des groupes dans la gestion des droits d’utilisation Azure Active Directory.
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d9b2f8ae5d679bf6642cd4cc2872e53b1234f11
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989163"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutoriel : Gérer le cycle de vie de vos licences basées sur des groupes dans Azure AD
 
Avec Azure Active Directory (Azure AD), vous pouvez utiliser des groupes pour gérer les [licences de vos applications](../enterprise-users/licensing-groups-assign.md). Vous pouvez rendre la gestion de ces groupes encore plus facile grâce à la gestion des droits d’utilisation : 

* Configurez des révisions d’accès régulières pour vous assurer que seuls les collaborateurs qui ont besoin des licences sont dans le groupe. 
* Autorisez d’autres collaborateurs à demander d’appartenir au groupe.

Dans ce tutoriel, vous jouez le rôle d’un administrateur informatique chez Woodgrove Bank. Vous êtes chargé de créer un package d’accès pour que les collaborateurs de votre organisation puissent accéder facilement aux licences Office. (Vous devez déjà disposer d’un groupe qui gère vos [licences Office](../enterprise-users/licensing-groups-assign.md).) Vous souhaitez pouvoir réviser ces membres de groupe chaque année. Vous souhaitez également autoriser les nouveaux collaborateurs à demander des licences Office que le responsable devra approuver.
 
Pour utiliser la gestion des droits d’utilisation Azure AD, vous devez disposer d’une de ces licences :

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [Exigences des licences](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Étape 1 : Configurer les bases pour votre package d’accès

**Rôle prérequis :** Administrateur général, Administrateur de la gouvernance des identités, Administrateur d’utilisateurs, Propriétaire de catalogue ou Gestionnaire de package d’accès

1. Dans le portail Azure, dans le volet de gauche, sélectionnez **Azure Active Directory**.

2. Sous **Gérer**, sélectionnez **Gouvernance des identités**.

3. Sous **Gestion des droits d’utilisation**, sélection **Packages d’accès**. 

4. Sélectionnez **Nouveau package d’accès**.

5. Sous l’onglet **Informations de base**, dans la zone **Nom**, entrez **Licences Office**. Dans la zone **Description**, entrez **Accès aux licences des logiciels Office**.

6. Vous pouvez laisser **Général** dans la liste **Catalogue**.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Étape 2 : Configurer les ressources pour votre package d’accès

1. Sélectionnez **Suivant : Rôles des ressources** pour accéder à l’onglet **Rôles des ressources**.

2. Sous cet onglet, sélectionnez les ressources et le rôle de ressource à inclure dans le package d’accès. Dans ce scénario, sélectionnez **Groupes et équipes** et recherchez votre groupe auquel a été affecté [Licences Office](../enterprise-users/licensing-groups-assign.md).

3. Dans la liste **Rôle**, sélectionnez **Membre**.

## <a name="step-3-configure-requests-for-your-access-package"></a>Étape 3 : Configurer les demandes pour votre package d’accès

1. Sélectionnez **Suivant : Demandes** pour accéder à l’onglet **Demandes**.

   Sous cet onglet, vous créez une stratégie de demande. Une *stratégie* définit les règles pour accéder à un package d’accès. Vous allez créer une stratégie qui permet aux collaborateurs de l’annuaire de ressources de demander le package d’accès.

3. Dans la section **Utilisateurs qui peuvent demander l’accès**, sélectionnez **Pour les utilisateurs de votre annuaire**, puis **Tous les membres (à l’exclusion des invités)** . Avec ces paramètres, seuls les membres de votre annuaire peuvent demander des licences Office.

4. Assurez-vous que l’option **Exiger l’approbation** est définie sur **Oui**.

5. Pour **Exiger la justification du demandeur**, laissez sur **Oui**.

6. Laissez **De combien de phases avez-vous besoin ?** défini sur **1**.

7. Sous **Approbateur**, sélectionnez **Gestionnaire comme approbateur**. Cette option permet au gestionnaire du demandeur d’approuver la demande. Si le système ne parvient pas à trouver le gestionnaire, vous pouvez sélectionner une autre personne comme approbateur de secours.

8. Laissez **La décision doit être prise dans combien de jours ?** défini sur **14**.

9. Laissez **Exiger la justification de l’approbateur** défini sur **Oui**.

10. Sous **Activer les nouvelles demandes et les attributions**, sélectionnez **Oui** pour que les collaborateurs demandent le package d’accès dès qu’il est créé.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Étape 4 : Configurer les informations du demandeur pour votre package d’accès

1. Sélectionnez **Suivant** pour accéder à l’onglet **Informations sur le demandeur**.

2. Sous cet onglet, vous pouvez poser des questions pour collecter plus d’informations sur votre demandeur. Les questions sont affichées dans le formulaire de demande et peuvent être obligatoires ou facultatives. Dans ce scénario, vous n’avez pas été invité à inclure d’informations sur le demandeur pour le package d’accès, donc vous pouvez laisser ces zones vides.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Étape 5 : Configurer le cycle de vie pour votre package d’accès

1. Sélectionnez **Suivant : Cycle de vie** pour accéder à l’onglet **Cycle de vie**.

2. Dans la section **Expiration**, sélectionnez **Nombre de jours** pour **Les attributions de package d’accès expirent**.
    
3. Dans **Les attributions expirent après**, entrez **365**. Cette zone spécifie à quel moment les membres qui ont accès au package d’accès devront renouveler leur accès. 

4. Vous pouvez également configurer des révisions d’accès pour pouvoir vérifier régulièrement si le collaborateur a toujours besoin d’accéder au package d’accès. Une révision peut être une auto-révision effectuée par le collaborateur. Vous pouvez également définir le responsable du collaborateur ou une autre personne comme réviseur. Pour plus d’informations, consultez [Révisions d’accès](entitlement-management-access-reviews-create.md). 
 
    Dans ce scénario, vous voulez que tous les collaborateurs vérifient s’ils ont toujours besoin d’une licence pour Office chaque année.

    1. Sous **Exiger des révisions d’accès**, sélectionnez **Oui**.
    2. Vous pouvez laisser **Date de début** défini sur la date du jour. La date correspond au démarrage de la révision d’accès. Après avoir créé une révision d’accès, vous ne pouvez pas mettre à jour sa date de début.
    3. Sous **Fréquence de révision**, sélectionnez **Annuelle** parce que la révision aura lieu une fois par an. La zone **Fréquence de révision** vous permet de déterminer la fréquence d’exécution de la révision d’accès.
    4. Spécifiez une **Durée (en jours)** .  La zone Durée est là où vous indiquez le nombre de jours pendant lesquels chaque occurrence de la série de révisions d’accès s’exécutera.
    5. Sous **Réviseurs**, sélectionnez **Responsable**.

## <a name="step-6-review-and-create-your-access-package"></a>Étape 6 : Vérifier et créer votre package d’accès

1. Sélectionnez **Suivant : Vérifier + créer** pour accéder à l’onglet **Vérifier + créer**.

   Sous cet onglet, vous pouvez vérifier la configuration de votre package d’accès avant de le créer. En cas de problème, vous pouvez utiliser les onglets pour naviguer jusqu’à un point spécifique dans la procédure pour apporter des modifications.

3. Lorsque vous êtes content de votre configuration, sélectionnez **Créer**. Après un moment, vous devriez voir une notification indiquant que le package d’accès est créé.

4. Une fois que le package d’accès est créé, vous allez voir la page **Vue d’ensemble** du package. Vous trouverez le **lien du portail Mon Accès** ici. Copiez le lien et partagez-le avec votre équipe afin que les membres puissent demander que le package d’accès reçoive des licences pour Office.

## <a name="step-7-clean-up-resources"></a>Étape 7 : Nettoyer les ressources

Dans cette étape, vous pouvez supprimer le package d’accès Licences Office. 

**Rôle prérequis :** Administrateur général, Administrateur de la gouvernance des identités ou Gestionnaire de package d’accès

1. Dans le portail Azure, dans le volet de gauche, sélectionnez **Azure Active Directory**.

2. Sous **Gérer**, sélectionnez **Gouvernance des identités**.

3. Sous **Gestion des droits d’utilisation**, sélection **Packages d’accès**. 

4. Ouvrez le package d’accès **Licences Office**. 

5. Sélectionnez **Rôles des ressources**.

6. Sélectionnez le groupe que vous avez ajouté au package d’accès. Dans le volet de détails, sélectionnez **Supprimer le rôle de ressource**. Dans la zone de message qui s’affiche, sélectionnez **Oui**.

7. Ouvrez la liste des packages d’accès.

8. Pour **Licences Office**, sélectionnez le bouton représentant des points de supsension (...), puis **Supprimer**. Dans la zone de message qui s’affiche, sélectionnez **Oui**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des packages d’accès pour gérer l’accès à d’autres types de ressources comme les applications et les sites : 

[Gérer l’accès aux ressources dans la gestion des droits d’utilisation Azure AD](./entitlement-management-access-package-first.md)