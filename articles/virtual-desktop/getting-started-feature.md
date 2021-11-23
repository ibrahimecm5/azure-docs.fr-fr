---
title: Déployer Azure Virtual Desktop avec la fonctionnalité de prise en main - Azure
description: Guide de démarrage rapide pour configurer rapidement Azure Virtual Desktop avec la fonctionnalité de prise en main du Portail Azure.
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 62ba9a8fa578ab12408160d9a6d19243474921fc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277217"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>Déployer Azure Virtual Desktop avec la fonctionnalité de prise en main

La nouvelle fonctionnalité de prise en main du Portail Azure est un moyen simple et rapide d’installer et de configurer Azure Virtual Desktop sur votre déploiement.

## <a name="requirements"></a>Spécifications

Pour suivre la prise en main, vous devez respecter les exigences suivantes :

- Un locataire Azure Active Directory (AD).
- Un compte disposant d’autorisations d’administrateur général sur Azure AD.

   >[!NOTE]
   >La fonctionnalité de prise en main ne prend pas en charge les comptes MSA, B2B ou invité pour le moment.

- Un abonnement Azure actif.

   >[!NOTE]
   >La fonctionnalité de prise en main ne prend pas actuellement en charge les comptes avec authentification multifacteur.

- Un compte avec des **autorisations Propriétaire** sur l’abonnement.

Si vous utilisez la fonctionnalité de prise en main dans un environnement avec Active Directory Domain Services (AD DS), vous devez également respecter les exigences suivantes :

- Des informations d’identification d’administrateur de domaine AD DS.
- Vous devez configurer Azure AD Connect sur votre abonnement et vérifier que le conteneur « USERS » se synchronise avec Azure AD.
- Le contrôleur de domaine de votre machine virtuelle (VM) ne doit pas avoir d’extensions DSC de type **Microsoft.PowerShell.DSC**.

Si vous utilisez la fonctionnalité de prise en main dans un environnement sans fournisseur d’identité, vous devez suivre les exigences supplémentaires suivantes :

- L’UPN de votre jointure de domaine AD ne doit pas inclure de [mots-clés interdits par la liste de directives sur les noms d’utilisateur](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-), et vous devez utiliser un nom d’utilisateur unique qui ne figure pas encore dans votre abonnement à Azure AD.
- Vous devez créer un pool d’hôtes pour ajouter des hôtes de session que vous créez à l’aide de la fonctionnalité de prise en main. Si vous essayez de créer un hôte de session dans un pool hôte existant, il ne fonctionnera pas.

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>Pour les abonnements avec Azure AD DS ou AD DS

Voici comment utiliser la fonctionnalité de prise en main dans un abonnement possédant déjà Azure AD DS ou AD DS :

1. Ouvrez [le Portail Azure](https://portal.azure.com).

2. Connectez-vous à Azure et ouvrez **Azure Virtual Desktop Management**, puis sélectionnez l’onglet **Démarrage**. La page d’accueil de la fonctionnalité de démarrage s’ouvre.

3. Sélectionnez **Create** (Créer).

4. Dans l’onglet **De base**, sélectionnez les valeurs suivantes :

    - Pour **Abonnement**, accédez à **How is your subscription configured** (Mode de configuration de votre abonnement), puis sélectionnez **Existing setup** (Configuration existante).

    - Dans **Emplacement**, sélectionnez l’emplacement où vous allez déployer vos ressources.

    - Dans **UPN admin UPN** (Nom d’utilisateur principal de l’administrateur Azure), entrez le nom d’utilisateur principal (UPN) complet du compte disposant des autorisations d’administrateur dans Azure AD et des autorisations de propriétaire dans l’abonnement que vous prévoyez d’utiliser.

    - Dans **UPN de jonction de domaine AD**, entrez l’UPN complet du compte avec les autorisations que vous prévoyez d’utiliser pour joindre les machines virtuelles à votre domaine.

    - Pour **Identité**, sélectionnez **Azure AD DS** ou **AD DS** en fonction de votre environnement. Ce que vous choisirez ici aura une incidence sur les entrées dont vos machines virtuelles auront besoin.

5. Dans l’onglet **Machines virtuelles**, sélectionnez les valeurs suivantes :

    - Pour répondre à la question **Voulez-vous que les utilisateurs partagent cet ordinateur ?** , sélectionnez l’une des options suivantes en fonction de vos besoins :
      - Si vous souhaitez créer un pool d’ordinateurs hôtes personnels ou à session unique, sélectionnez **Non**.
      - Si vous souhaitez créer un pool d’hôtes avec plusieurs sessions ou pools, sélectionnez **Oui (multisession)** . Cette opération crée également un compte de stockage Azure Files joint à Azure AD DS ou AD DS.

    - Pour **Type d’image**, sélectionnez une image à partir de la Galerie d’images Azure, une image personnalisée ou un disque dur virtuel à partir d’un objet blob de stockage.

    - Pour **Taille de machine virtuelle**, sélectionnez la taille et la référence (SKU) que vous souhaitez pour les machines virtuelles que vous allez déployer.

    - Pour **Nombre de machines virtuelles**, sélectionnez le nombre de machines virtuelles que vous souhaitez approvisionner dans le pool hôte.

    - Si vous utilisez une configuration existante avec AD DS, ces options s’affichent :

       - Dans **Sous-réseau**, sélectionnez un sous-réseau dans le réseau virtuel. Le sous-réseau que vous choisissez doit être au même emplacement que l’identité (AD DS ou Azure AD DS) ou être homologué.

       - Dans **Domain controller resource group** (Groupe de ressources du contrôleur de domaine), sélectionnez le groupe de ressources dans lequel la machine virtuelle AD DS est localisée ou homologuée. Le groupe de ressources comprenant le contrôleur de domaine doit se trouver dans le même abonnement. La fonctionnalité de prise en main ne prend pas en charge les abonnements homologués pour le moment.

       - Dans **Domain controller virtual machine** (Machine virtuelle du contrôleur de domaine), entrez le nom de la machine virtuelle exécutant l’instance AD DS de votre déploiement.

    - Si vous souhaitez ouvrir le groupe Select Azure AD users or Users (Utilisateurs ou utilisateurs Azure AD sélectionnés), activez la case à cocher **Assign existing users** (Attribuer des utilisateurs existants).

    - Si vous souhaitez créer un compte d’utilisateur de validation pour tester votre déploiement, activez la case à cocher **Create validation user** (Créer un utilisateur de validation), puis entrez un nom d’utilisateur et un mot de passe dans l’invite qui s’affiche.

       >[!NOTE]
       >La mise en route crée le groupe d’utilisateurs de validation dans le conteneur « USERS ». Vous devez vous assurer que votre groupe de validation est synchronisé avec Azure AD. Si la synchronisation ne fonctionne pas, créez au préalable le groupe AVDValidationUsers dans une unité d’organisation en cours de synchronisation avec Azure AD.

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>Pour les abonnements sans Azure AD DS ou AD DS

Cette section vous explique comment utiliser la fonctionnalité de prise en main pour un abonnement sans Azure AD DS ou AD DS. Pour référence, ces abonnements sont parfois qualifiés de « vides ».

Pour déployer Azure Virtual Desktop sur un abonnement sans Azure AD DS ou AD DS :

1. Ouvrez [le Portail Azure](https://portal.azure.com).

2. Connectez-vous à Azure et ouvrez **Azure Virtual Desktop Management**, puis sélectionnez l’onglet **Démarrage**. La page d’accueil de la fonctionnalité de démarrage s’ouvre.

3. Dans l’onglet **De base**, sélectionnez les valeurs suivantes :

    - Sous **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez déployer Azure Virtual Desktop.

    - Dans **How is your subscription configured** (Mode de configuration de votre abonnement), sélectionnez **Empty subscription** (Abonnement vide). Un abonnement « vide » est un abonnement qui ne requiert pas de fournisseur d’identité comme Azure AD ou AD DS.

    - Pour le **préfixe du groupe de ressources**, entrez les préfixes du groupe de ressources que vous allez créer : *-prerequisite*, *-deployment* et *-avd*.

    - Dans **Emplacement**, entrez l’emplacement de la ressource que vous souhaitez utiliser pour votre déploiement.

    - Dans **UPN de l’administrateur Azure**, entrez l’UPN complet d’un compte disposant des autorisations d’administrateur sur les autorisations Azure AD et du propriétaire sur l’abonnement.

    - Dans **AD Domain join UPN** (Nom d'utilisateur principal de jonction de domaine AD), entrez l’UPN complet du compte qui sera ajouté au groupe **AAD DC Administrators**.

    >[!NOTE]
    >Le nom d’utilisateur pour le nom d'utilisateur principal de jonction de domaine Active Directory doit être un nom unique qui n’existe pas déjà dans Azure AD. La fonctionnalité de prise en main ne prend pas actuellement en charge l’utilisation de noms d’utilisateur Azure AD existants pour les comptes sans Azure AD ou AD DS.

4. Dans l’onglet **Machines virtuelles**, sélectionnez les valeurs suivantes :

    - Pour répondre à la question **Voulez-vous que les utilisateurs partagent cet ordinateur ?** , sélectionnez l’une des options suivantes en fonction de vos besoins :
      - Si vous souhaitez créer un pool d’ordinateurs hôtes personnels ou à session unique, sélectionnez **Non**.
      - Si vous souhaitez créer un pool d’hôtes avec plusieurs sessions ou pools, sélectionnez **Oui (multisession)** . Cette opération crée également un compte de stockage Azure Files joint à Azure AD DS ou AD DS.

    - Pour **Type d’image**, sélectionnez une image à partir de la Galerie d’images Azure, une image personnalisée ou un disque dur virtuel à partir d’un objet blob de stockage.

    - Pour **Taille de machine virtuelle**, sélectionnez la taille et la référence (SKU) que vous souhaitez pour les machines virtuelles que vous allez déployer.

    - Pour **Nombre de machines virtuelles**, sélectionnez le nombre de machines virtuelles que vous souhaitez approvisionner dans le pool hôte.

5. Dans l’onglet **Attributions**, sélectionnez **Create validation user** (Créer un utilisateur de validation), puis entrez un nom d’utilisateur et un mot de passe dans les champs **Validation user username** (Nom d’utilisateur de l’utilisateur de validation) et **Validation user password** (Mot de passe de l’utilisateur de validation). L’utilisateur de validation est un utilisateur qui testera votre déploiement une fois que celui-ci sera prêt.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si, après le déploiement, vous changez d’avis et que vous souhaitez supprimer les ressources Azure Virtual Desktop de votre environnement sans encourir de frais de facturation supplémentaires, vous pouvez les supprimer en toute sécurité en suivant les instructions de cette section.

Si vous avez créé vos ressources sur un abonnement avec Azure AD DS ou AD DS, la fonctionnalité aura généré deux groupes de ressources avec les préfixes «  *-deployment* » et «  *-avd* ». Dans le Portail Azure, accédez à **Groupes de ressources** et supprimez tous les groupes de ressources avec ces préfixes afin de supprimer le déploiement.

Si vous avez créé vos ressources sur un abonnement sans Azure AD DS ou AD DS, la fonctionnalité aura généré trois groupes de ressources avec les préfixes *-prerequisite*, *-deployment* et *-avd*. Dans le Portail Azure, accédez à **Groupes de ressources** et supprimez tous les groupes de ressources avec ces préfixes afin de supprimer le déploiement.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez apprendre à déployer Azure Virtual Desktop de façon plus approfondie, consultez nos didacticiels pour configurer votre déploiement manuellement, en commençant par [créer un pool d’ordinateurs hôtes avec le Portail Azure](create-host-pools-azure-marketplace.md).
