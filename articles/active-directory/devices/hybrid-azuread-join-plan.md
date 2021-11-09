---
title: Planifier l’implémentation de la jonction Azure Active Directory hybride - Azure Active Directory
description: Découvrez comment configurer des appareils hybrides joints à Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a766415eab11a0486b4609d181e5f01dcf4ef2a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049688"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procédure : Planifier l’implémentation de la jonction Azure AD Hybride

À l’instar d’un utilisateur, un appareil est une autre identité essentielle que vous souhaitez protéger et utiliser pour protéger vos ressources à tout moment et en tout lieu. Vous pouvez atteindre cet objectif en intégrant et en gérant les identités des appareils dans Azure AD à l’aide d’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../conditional-access/overview.md).

Si vous disposez d’un environnement Active Directory (AD) local et que vous souhaitez lier vos ordinateurs joints au domaine AD à Azure AD, vous pouvez y parvenir en effectuant une jonction Azure AD Hybride. Cet article présente les étapes à suivre pour implémenter une jointure Azure AD hybride dans un environnement. 

> [!TIP]
> L’accès par authentification unique aux ressources locales est également disponible pour les appareils joints à Azure AD. Pour plus d’informations, consultez [Fonctionnement de l’authentification unique auprès de ressources locales sur des appareils joints à Azure AD](azuread-join-sso.md).
>

## <a name="prerequisites"></a>Prérequis

Cet article suppose que vous avez lu la [Présentation de la gestion des identités des appareils dans Azure Active Directory](./overview.md).

> [!NOTE]
> La version de contrôleur de domaine minimale requise pour une jonction Azure AD Hybride Windows 10 est Windows Server 2008 R2.

Les appareils à jointure hybride Azure AD doivent avoir périodiquement une visibilité réseau directe vers vos contrôleurs de domaine. Sans cette connexion, les appareils deviennent inutilisables.

Scénarios sans visibilité directe vers vos contrôleurs de domaine :

- Changement de mot de passe de l’appareil
- Changement de mot de passe utilisateur (informations d’identification mises en cache)
- Réinitialisation du module de plateforme sécurisée

## <a name="plan-your-implementation"></a>Planifier l’implémentation

Pour planifier votre implémentation Azure AD hybride, prenez connaissance de ces étapes :

> [!div class="checklist"]
> - Vérifier les appareils pris en charge
> - Connaître les points à savoir
> - Vérifier la validation contrôlée de la jonction Azure AD Hybride
> - Sélectionner votre scénario en fonction de votre infrastructure d’identité
> - Vérifier la prise en charge d’un UPN AD local pour la jonction Azure AD Hybride

## <a name="review-supported-devices"></a>Vérifier les appareils pris en charge

La jointure Azure AD hybride prend en charge un large éventail d’appareils Windows. Dans la mesure où la configuration des appareils fonctionnant sous des versions antérieures de Windows implique des étapes supplémentaires ou différentes, les appareils pris en charge sont divisés en deux catégories :

### <a name="windows-current-devices"></a>Appareils Windows actuels

- Windows 10
- Windows 11
- Windows Server 2016
  - **Remarque** : Les clients Cloud national Azure doivent avoir la version 1803
- Windows Server 2019

Pour les appareils exécutant le système d’exploitation Windows, les versions prises en charge sont répertoriées dans l'article [Informations de publication relatives à Windows 10](/windows/release-information/). En guise de meilleure pratique, Microsoft recommande d'effectuer une mise à niveau vers la dernière version de Windows 10.

### <a name="windows-down-level-devices"></a>Appareils Windows de bas niveau

- Windows 8.1
- Le support pour Windows 7 a pris fin le 14 janvier 2020. Pour plus d’informations, consultez [Le support de Windows 7 a pris fin le 14 janvier 2020](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Pour obtenir des informations de support sur Windows Server 2008 et 2008 R2, consultez [Préparez-vous à la fin du support de Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

La première étape de la planification consiste à examiner l’environnement et à déterminer s’il est nécessaire de prendre en charge les appareils Windows de bas niveau.

## <a name="review-things-you-should-know"></a>Connaître les points à savoir

### <a name="unsupported-scenarios"></a>Scénarios non pris en charge

- La jonction Azure AD Hybride n'est pas prise en charge pour Windows Server avec le rôle de contrôleur de domaine.

- La jonction Azure AD Hybride n’est pas prise en charge sur les appareils Windows de bas niveau lors de l’utilisation de l’itinérance des informations d’identification, de l’itinérance du profil utilisateur ou du profil obligatoire.

- Le système d’exploitation Server Core ne prend en charge aucun type d’inscription d’appareil.

- L’outil de migration de l’état utilisateur (USMT) ne fonctionne pas avec l’inscription de l’appareil.  

### <a name="os-imaging-considerations"></a>Considérations relatives aux images de système d’exploitation

- Si vous comptez sur l’outil de préparation système (Sysprep) et utilisez une image **pré-Windows 10 1809** pour l’installation, vérifiez que cette image ne provient pas d'un appareil déjà inscrit auprès d’Azure AD en tant que jonction Azure AD Hybride.

- Si vous utilisez une capture instantanée de machine virtuelle pour créer des machines virtuelles supplémentaires, vérifiez qu’elle ne provient pas d'une machine virtuelle déjà inscrite auprès d'Azure AD en tant que jonction Azure AD Hybride.

- Si vous utilisez [Filtre d’écriture unifié](/windows-hardware/customize/enterprise/unified-write-filter) et des technologies similaires qui effacent les modifications apportées au disque au redémarrage, vous devez les appliquer une fois que l’appareil joint à l’Azure AD Hybride. L’activation de ces technologies avant la jonction à l’Azure AD Hybride entraînera la disjonction de l’appareil à chaque redémarrage

### <a name="handling-devices-with-azure-ad-registered-state"></a>Gestion des appareils avec l’état inscrit auprès d’Azure AD

Si vos appareils Windows 10 joints à un domaine sont [inscrits sur Azure AD](concept-azure-ad-register.md) auprès de votre locataire, cela peut entraîner un double état : appareil joint à Azure AD Hybride et appareil inscrit sur Azure AD. Nous vous recommandons de procéder à une mise à niveau vers Windows 10 1803 (avec KB4489894 appliqué) ou version ultérieure pour répondre automatiquement à ce scénario. Dans les versions antérieures à 1803, vous devrez supprimer manuellement l’état « appareil inscrit sur Azure AD » avant d’activer une jonction Azure AD Hybride. Dans les versions 1803 et ultérieures, les modifications suivantes ont été apportées pour éviter ce double état :

- Tout état existant inscrit sur Azure AD pour un utilisateur est automatiquement supprimé <i>dès lors que l’appareil est joint à Azure AD Hybride et que le même utilisateur se connecte</i>. Par exemple, si l’utilisateur A a un état Azure AD inscrit sur l’appareil, le double état de l’utilisateur A est nettoyé uniquement lorsque l’utilisateur A se connecte à l’appareil. S’il y a plusieurs utilisateurs sur le même appareil, le double état est nettoyé individuellement quand ces utilisateurs se connectent. En plus de supprimer l’état inscrit auprès d’Azure AD, Windows 10 désinscrit également l’appareil d’Intune ou d’autres GPM, si l’inscription s’est produite dans le cadre de l’inscription d’Azure AD via l’inscription automatique.
- L’état inscrit auprès d’Azure AD sur tous les comptes locaux sur l’appareil n’est pas affecté par cette modification. S’applique uniquement aux comptes de domaine. L’état inscrit auprès d’Azure AD sur les comptes locaux n’est donc pas supprimé automatiquement, même après l’ouverture de session de l’utilisateur, car l’utilisateur n’est pas un utilisateur de domaine. 
- Vous pouvez éviter que votre appareil joint au domaine soit inscrit à Azure AD en ajoutant la valeur de registre suivante à HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001.
- Dans Windows 10 1803, si vous avez configuré Windows Hello Entreprise, l’utilisateur doit reconfigurer Windows Hello Entreprise après le nettoyage de double état. Ce problème a été résolu avec KB4512509

> [!NOTE]
> Bien que Windows 10 supprime automatiquement l’état inscrit auprès d’Azure AD localement, l’objet de l’appareil dans Azure AD n’est pas supprimé immédiatement s’il est géré par Intune. Vous pouvez valider la suppression de l’état inscrit auprès d’Azure AD en exécutant « dsregcmd /status » et considérer l’appareil comme n’étant plus inscrit auprès d’Azure AD.

### <a name="hybrid-azure-ad-join-for-single-forest-multiple-azure-ad-tenants"></a>Jonction Azure AD Hybride pour une seule forêt avec plusieurs locataires Azure AD

Pour inscrire des appareils en tant que jonction Azure AD Hybride auprès des locataires respectifs, les organisations doivent configurer le SCP sur les appareils et pas dans AD. Pour plus d’informations sur la procédure à suivre, consultez l’article [Validation contrôlée de la jonction Azure AD Hybride](hybrid-azuread-join-control.md). Il est également important que les organisations sachent que certaines fonctionnalités d’Azure AD ne sont pas disponibles dans les configurations à une seule forêt et plusieurs locataires Azure AD.
- La [réécriture d'appareil](../hybrid/how-to-connect-device-writeback.md) n’est pas disponible. Cela impacte l’[accès conditionnel basé sur les appareils pour les applications locales fédérées à l’aide d’ADFS](/windows-server/identity/ad-fs/operations/configure-device-based-conditional-access-on-premises). Cela impacte aussi le [déploiement de Windows Hello Entreprise avec le modèle d’approbation de certificat hybride](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust).
- La [réécriture de groupes](../hybrid/how-to-connect-group-writeback.md) n’est pas disponible. Cela impacte la réécriture des groupes Office 365 dans une forêt avec Exchange installé.
- L’[authentification unique fluide](../hybrid/how-to-connect-sso.md) n’est pas disponible. Cela impacte les scénarios d’authentification SSO (authentification unique) que les organisations utilisent éventuellement sur les plateformes comprenant différents OS/navigateurs, par exemple iOS/Linux avec Firefox, Safari, Chrome sans l’extension Windows 10.
- La [jonction Azure AD Hybride pour les appareils Windows de bas niveau dans un environnement managé](./hybrid-azuread-join-managed-domains.md#enable-windows-down-level-devices) n’est pas disponible. Par exemple, la jonction Azure AD Hybride sur Windows Server 2012 R2 dans un environnement managé nécessite une authentification unique transparente, mais comme ce mode d’authentification n’est pas disponible, la jonction Azure AD Hybride ne l’est pas non plus dans cette configuration.
- La [protection locale par mot de passe Azure AD](../authentication/concept-password-ban-bad-on-premises.md) n’est pas disponible. Cela empêche les changements et réinitialisations de mots de passe sur les contrôleurs de domaine AD DS (Active Directory Domain Services) locaux utilisant les mêmes listes de mots de passe interdits globaux et personnalisés qui sont stockées dans Azure AD.


### <a name="additional-considerations"></a>Considérations supplémentaires

- Si votre environnement utilise l’infrastructure VDI (Virtual Desktop Infrastructure), consultez [Identité d’appareil et la virtualisation des services de Bureau](./howto-device-identity-virtual-desktop-infrastructure.md).

- La jonction Azure AD Hybride est prise en charge pour les modules TPM 2.0 compatibles FIPS, mais pas pour les modules TPM 1.2. Si vos appareils sont dotés de modules TPM 1.2 compatibles FIPS, vous devez les désactiver avant de procéder à la jonction Azure AD Hybride. Microsoft ne propose aucun outil permettant de désactiver le mode FIPS pour les modules TPM car il dépend du fabricant de ces modules. Pour obtenir de l'aide, contactez votre fabricant OEM. 

- À compter de la version 1903 de Windows 10, les modules TPM 1.2 ne sont pas utilisés avec les jonctions Azure AD hybrides, et les appareils dotés de ces TPM sont considérés comme n’ayant pas de TPM.

- Les modifications d’UPN ne sont prises en charge qu’à partir de la mise à jour Windows 10 2004. Pour les appareils antérieurs à la mise à jour Windows 10 2004, les utilisateurs rencontrent des problèmes liés à l’authentification unique et à l’accès conditionnel sur leurs appareils. Pour résoudre ce problème, vous devez déconnecter l’appareil d’Azure AD (exécutez « dsregcmd /leave » avec des privilèges élevés) et le reconnecter (ce qui s’effectue automatiquement). Cela étant, les utilisateurs qui se connectent avec Windows Hello Entreprise ne rencontrent pas ce problème.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Vérifier la validation contrôlée de la jonction Azure AD Hybride

Lorsque toutes les conditions préalables sont en place, les appareils Windows sont inscrits automatiquement en tant qu’appareils dans votre locataire Azure AD. L’état de ces identités d’appareils dans Azure AD est appelé jonction Azure AD Hybride. Des informations supplémentaires sur les concepts abordés dans cet article sont disponibles dans l’article [Présentation de la gestion des identités des appareils dans Azure Active Directory](overview.md).

Les organisations peuvent vouloir effectuer une validation contrôlée de la jonction Azure AD Hybride avant de l’activer dans leur organisation entière en une fois. Consultez l'article [Validation contrôlée de la jonction Azure AD Hybride](hybrid-azuread-join-control.md) pour savoir comment procéder.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Sélectionner votre scénario en fonction de votre infrastructure d’identité

La jointure Azure AD Hybride fonctionne avec les environnements managés et fédérés, selon que l’UPN est routable ou non routable. Consultez le bas de la page pour voir un tableau des scénarios pris en charge.  

### <a name="managed-environment"></a>Environnement géré

Un environnement managé peut être déployé par le biais de la [synchronisation de hachage de mot de passe (PHS)](../hybrid/whatis-phs.md) ou de l’[authentification directe (PTA)](../hybrid/how-to-connect-pta.md) avec l’[authentification unique fluide](../hybrid/how-to-connect-sso.md).

Ces scénarios ne nécessitent pas la configuration d’un serveur de fédération pour l’authentification.

> [!NOTE]
> [L’authentification Cloud à l’aide du déploiement intermédiaire](../hybrid/how-to-connect-staged-rollout.md) est prise en charge uniquement lors du démarrage de la mise à jour de Windows 10 1903

### <a name="federated-environment"></a>Environnement fédéré

Un environnement fédéré doit disposer d’un fournisseur d’identité qui prend en charge les exigences suivantes : Si vous disposez d’un environnement fédéré utilisant les services de fédération Active Directory (AD FS), les exigences ci-dessous sont déjà prises en charge.

- **Revendication WIAORMULTIAUTHN :** Cette revendication est nécessaire à des fins de jonction Azure AD Hybride pour les appareils Windows de bas niveau.
- **Protocole WS-Trust :** Ce protocole est nécessaire pour authentifier auprès d’Azure AD les appareils Windows actuels ayant fait l’objet d’une jonction Azure AD Hybride. Lorsque vous utilisez AD FS, vous devez activer les points de terminaison WS-Trust suivants : `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** ou **adfs/services/trust/13/windowstransport** doivent tous les deux être activés en tant que points de terminaison uniquement accessibles sur intranet ; ils NE doivent PAS être exposés comme points de terminaison extranet via le proxy d’application web. Pour en savoir plus sur la désactivation des points de terminaison Windows WS-Trust, consultez [Désactiver les points de terminaison Windows WS-Trust sur le proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Vous pouvez visualiser les points de terminaison qui sont activés par le biais de la console de gestion AD FS sous **Service** > **Points de terminaison**.

> [!NOTE]
> Azure AD ne prend pas en charge les cartes à puce ou les certificats dans les domaines gérés.

Depuis la version 1.1.819.0, Azure AD Connect comporte un Assistant permettant de configurer la jointure Azure AD hybride. Il simplifie considérablement le processus de configuration. Si vous n’avez pas la possibilité d’installer la version requise d’Azure AD Connect, consultez le [Guide pratique pour configurer manuellement l’inscription des appareils](hybrid-azuread-join-manual.md). 

Selon le scénario correspondant à votre infrastructure d’identité, consultez :

- [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
- [Configurer la jonction Azure Active Directory Hybride pour un environnement managé](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Vérifier la prise en charge d’un UPN utilisateur AD local pour la jonction Azure AD Hybride

Parfois, vos UPN utilisateur AD locaux peuvent différer de vos UPN AD Azure. Dans ce cas, une jonction Azure AD Hybride Windows 10 offre une prise en charge limitée des UPN AD locaux, variable selon la [méthode d’authentification](../hybrid/choose-ad-authn.md), le type de domaine et la version de Windows 10. Deux types d’UPN AD locaux peuvent exister dans votre environnement :

- UPN des utilisateurs routables : un UPN routable possède un domaine vérifié valide, qui est inscrit auprès d’un bureau d’enregistrement de domaines. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.org est le domaine principal dans l’AD local appartenant à Contoso et [vérifié dans Azure AD](../fundamentals/add-custom-domain.md)
- UPN des utilisateurs non routables : un UPN non routable ne dispose pas d’un domaine vérifié. Il est applicable uniquement au sein du réseau privé de votre organisation. Par exemple, si contoso.com est le domaine principal dans Azure AD, contoso.local est le domaine principal dans l’AD local, mais n’est pas un domaine vérifiable sur Internet et n’est utilisé qu’à l’intérieur du réseau de Contoso.

> [!NOTE]
> Les informations contenues dans cette section s’appliquent uniquement aux UPN utilisateur locaux. Elles ne s’appliquent pas au suffixe de domaine d’ordinateur local (par exemple : ordinateur1.contoso.local).

Le tableau ci-dessous fournit des détails sur la prise en charge de ces UPN AD locaux dans une jointure Azure AD Hybride Windows 10

| Type d’UPN AD local | Type de domaine | version Windows 10 | Description |
| ----- | ----- | ----- | ----- |
| Routable | Adresses IP fédérées | À partir de la version 1703 | Mise à la disposition générale |
| Non routable | Adresses IP fédérées | À partir de la version 1803 | Mise à la disposition générale |
| Routable | Adresses IP gérées | À partir de la version 1803 | En disponibilité générale, Azure AD SSPR sur l’écran de verrouillage Windows n’est pas pris en charge. Le nom UPN local doit être synchronisé avec l’attribut `onPremisesUserPrincipalName` dans Azure AD |
| Non routable | Adresses IP gérées | Non pris en charge | |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la jonction Azure Active Directory Hybride pour un environnement fédéré](hybrid-azuread-join-federated-domains.md)
> [Configurer la jonction Azure Active Directory Hybride pour un environnement managé](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
