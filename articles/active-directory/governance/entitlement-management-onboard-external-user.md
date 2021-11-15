---
title: 'Tutoriel : Intégrer des utilisateurs externes à Azure AD via un processus d’approbation - Azure Active Directory'
description: Tutoriel pas à pas permettant de créer un package d’accès pour les utilisateurs externes nécessitant des approbations dans la gestion des droits d’utilisation Azure Active Directory.
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
ms.openlocfilehash: b97e440d318e1d08137ffa14691c5179d93d60f4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440732"
---
# <a name="tutorial---onboard-external-users-to-azure-ad-through-an-approval-process"></a>Tutoriel : Intégrer des utilisateurs externes à Azure AD via un processus d’approbation

Vous pouvez utiliser la gestion des droits d’utilisation comme moyen d’intégrer des utilisateurs externes. Cette fonctionnalité permet aux utilisateurs externes de demander l’accès à un ensemble de ressources et à vous de configurer des approbations avant qu’ils n’obtiennent l’accès à votre annuaire. Pour les utilisateurs externes intégrés via des droits d’utilisation, vous pouvez gérer leur cycle de vie avec des packages d’accès. Lorsque leur dernier package d’accès expire, ils sont supprimés de votre annuaire.

Dans ce tutoriel, vous travaillez pour WoodGrove Bank comme administrateur informatique. Vous avez été chargé de créer un package d’accès pour intégrer des partenaires d’une organisation externe avec laquelle votre groupe commercial travaille. Ils auront besoin d’accéder à un groupe Teams appelé **Collaboration externe**. L’approbation d’un commanditaire interne est nécessaire pour les organisations collaboratrices. Vous avez aussi été informé que l’accès du partenaire doit expirer dans 60 jours.
Pour utiliser la gestion des droits d’utilisation Azure AD, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

Pour plus d’informations, consultez [Exigences des licences](entitlement-management-overview.md#license-requirements).

## <a name="step-1-configure-basics"></a>Étape 1 : Configurer les bases

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès.

1. Dans le portail Azure, dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

2. Dans le menu gauche, cliquez sur **Identity Governance**.

3. Dans le menu gauche, cliquez sur **Packages d’accès**. Si vous voyez Accès refusé, vérifiez qu’une licence Azure AD Premium P2 est présente dans votre annuaire.

4. Cliquez sur **Nouveau package d’accès**.

5. Sous l’onglet **Informations de base**, entrez le nom **Package des utilisateurs externes** et la description **Approbation en attente pour l’accès des utilisateurs externes**.

6. Vous pouvez laisser la liste déroulante **Catalogue** définie sur **Général**.

## <a name="step-2-configure-resources"></a>Étape 2 : Configurer des ressources

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Rôles des ressources**.
 
   Sous cet onglet, sélectionnez les ressources et le rôle de ressource à inclure dans le package d’accès.

2. Cliquez sur **Groupes et équipes** et recherchez votre groupe **Collaboration externe**.

## <a name="step-3-configure-requests"></a>Étape 3 : Configurer les demandes

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Requêtes**.

   Sous cet onglet, vous créez une stratégie de demande. Une *stratégie* définit les règles ou barrières mises en place pour accéder à un package d’accès. Vous créez une stratégie qui permet à un utilisateur spécifique de l’annuaire de ressources de demander ce package d’accès.

2. Dans la section **Utilisateurs qui peuvent demander l’accès**, cliquez sur **Pour les utilisateurs qui ne sont pas dans votre annuaire**, puis sur **Tous les utilisateurs (toutes les organisations connectées + tous les nouveaux utilisateurs externes)** .

3. Assurez-vous que l’option **Exiger l’approbation** est définie sur **Oui**.

4. Les paramètres suivants vous permettent de configurer le mode de fonctionnement de vos approbations pour vos utilisateurs externes :

5. Pour **Exiger la justification du demandeur**, laissez sur **Oui**.

6. Pour **Combien de phases**, laissez sur **1**.

7. Pour le scénario **Approbateur**, sélectionnez **Sponsor interne**. Cette option provient d’une [organisation connectée](entitlement-management-organization.md) configurée où vous pouvez parrainer des organisations spécifiques avec lesquelles vous travaillez. Cela vous permet de définir comme approbateur une personne spécifiée de l’organisation connectée depuis votre organisation. 

8. Pour **La décision doit être prise dans combien de jours ?** , laissez sur **14**.

9. Pour **Exiger la justification de l’approbateur**, laissez sur **Oui**.

10. Définissez **Activer les nouvelles demandes et les attributions** sur **Oui** afin que ce package d’accès puisse être demandé dès qu’il sera créé.

## <a name="step-4-configure-requestor-information"></a>Étape 4 : Configurer les informations sur le demandeur

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Informations sur le demandeur**.

2. Sur cet écran, vous pouvez poser d’autres questions pour collecter plus d’informations sur votre demandeur. Ces questions sont affichées sur son formulaire de demande et peuvent être définies comme obligatoires ou facultatives. Pour le moment, vous pouvez les laisser vides.

## <a name="step-5-configure-lifecycle"></a>Étape 5 : Configurer le cycle de vie

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Cycle de vie**.

2. Dans la section **Expiration**, définissez **Nombre de jours** pour **Les attributions de package d’accès expirent**.

3. Définissez **Les attributions expirent après** sur **60** jours. Ce champ détermine à quel moment vos utilisateurs invités devront renouveler leur accès.

4. Vous pouvez également configurer des **révisions d’accès** pour pouvoir vérifier régulièrement si l’invité a toujours besoin d’accéder au package d’accès. Une révision peut être une auto-révision ou vous pouvez définir des révisions spécifiques pour cette tâche. Pour plus d’informations, consultez [Révisions d’accès](entitlement-management-access-reviews-create.md).

## <a name="step-6-review-and-create-your-access-package"></a>Étape 6 : Vérifier et créer votre package d’accès

1. Cliquez sur **Suivant** pour ouvrir l’onglet **Vérifier + créer**.

2. Sur cet écran, vous pouvez vérifier la configuration de votre package d’accès avant de le créer. En cas de problème, vous pouvez utiliser les onglets pour naviguer jusqu’à un point spécifique dans l’expérience de création pour apporter des modifications.

3. Une fois que vous êtes content de vos sélections, cliquez sur **Créer**. Après quelques instants, vous devez obtenir une notification indiquant que le package d’accès a été créé.

4. Une fois qu’il est créé, vous êtes dirigé vers la page **Vue d’ensemble** de votre package d’accès. Vous y trouverez le **lien Mon portail d’accès** où vous pourrez copier la valeur. Partagez ce lien avec vos utilisateurs externes afin qu’ils puissent demander ce package et commencer à collaborer.

## <a name="step-7-clean-up-resources"></a>Étape 7 : Nettoyer les ressources

Dans cette étape, vous pouvez supprimer le package d’accès au **package des utilisateurs externes**. 

**Rôle prérequis :** administrateur général, administrateur de la gouvernance des identités ou gestionnaire de package d’accès

1. Dans le **portail Azure**, dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

2. Dans le menu gauche, cliquez sur **Identity Governance**.

3. Dans le menu gauche, cliquez sur **Packages d’accès**. 

4. Ouvrez le package d’accès **Package des utilisateurs externes**. 

5. Cliquez sur **Rôles des ressources**.

6. Sélectionnez le groupe **Collaboration externe** que vous avez ajouté à ce package d’accès, puis dans le volet **Détails**, sélectionnez **Supprimer le rôle de ressource**. Dans le message qui s’affiche, sélectionnez **Oui**.

7. Ouvrez la liste des packages d’accès.

8. Pour **Package des utilisateurs externes**, sélectionnez les points de suspension (...), puis **Supprimer**. Dans le message qui s’affiche, sélectionnez **Oui**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des packages d’accès pour gérer l’accès à d’autres types de ressources comme les applications et les sites. [Tutoriel : Gérer l’accès aux ressources dans la gestion des droits d’utilisation Azure AD](./entitlement-management-access-package-first.md)
