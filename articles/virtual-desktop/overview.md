---
title: Qu’est-ce qu’Azure Virtual Desktop ? - Azure
description: Une vue d’ensemble d’Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c0e61760b130631c4f688b06f2cebdb163ea3910
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452145"
---
# <a name="what-is-azure-virtual-desktop"></a>Qu’est-ce qu’Azure Virtual Desktop ?

Azure Virtual Desktop est un service de virtualisation de bureau et d’application qui s’exécute dans le cloud.

Voici ce que vous pouvez faire quand vous exécutez Azure Virtual Desktop sur Azure :

* Configurer un déploiement Windows 10 multisession qui délivre une version complète de Windows 10 avec extensibilité
* Virtualisez les applications Microsoft 365 pour l’entreprise et optimisez-les pour qu’elles s’exécutent dans des scénarios virtuels multi-utilisateurs.
* Fournir des bureaux virtuels Windows 7 avec Mises à jour de sécurité étendues gratuites
* Tirer parti de vos applications et bureaux Windows Server et Services Bureau à distance existants sur n’importe quel ordinateur
* Virtualiser des bureaux et des applications
* Gérer des bureaux et des applications Windows 10, Windows Server et Windows 7 avec une expérience de gestion unifiée

## <a name="introductory-video"></a>Vidéo d’introduction

Découvrez Azure Virtual Desktop, pourquoi il est unique et les nouveautés dans cette vidéo :

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Pour obtenir d’autres vidéos sur Azure Virtual Desktop, consultez [notre sélection](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev).

## <a name="key-capabilities"></a>Fonctionnalités clés

Avec Azure Virtual Desktop, vous pouvez configurer un environnement extensible et flexible :

* Créez un environnement de virtualisation de bureaux complet dans votre abonnement Azure sans avoir à exécuter de serveurs de passerelle supplémentaires.
* Publiez autant de pools hôtes que nécessaire pour prendre en charge vos diverses charges de travail.
* Importez votre propre image pour les charges de travail de production ou de test à partir de la galerie Azure.
* Réduisez les coûts avec des ressources mises en pool et multisessions. Avec la nouvelle fonctionnalité multisession de Windows 10 Entreprise exclusive à Azure Virtual Desktop et le rôle Hôte de session Bureau à distance sur Windows Server, vous pouvez réduire considérablement le nombre de machines virtuelles et la charge sur le système d’exploitation tout en continuant à fournir les mêmes ressources à vos utilisateurs.
* Bénéficiez d’une propriété individuelle par le biais des bureaux personnels (persistants).

Vous pouvez déployer et gérer des bureaux virtuels :

* Utilisez le portail Azure, les interfaces Azure Virtual Desktop PowerShell et REST pour configurer les pools hôtes, créer des groupes d’applications, affecter des utilisateurs et publier des ressources.
* Publiez des applications distantes individuelles ou des applications de bureau complètes à partir d’un pool hôte unique, créez des groupes d’applications individuels pour différents groupes d’utilisateurs, ou affectez des utilisateurs à plusieurs groupes d’applications afin de réduire le nombre d’images.
* Lors de la gestion de votre environnement, utilisez l’accès délégué intégré pour attribuer des rôles et recueillir des données de diagnostics afin de comprendre différentes erreurs dues aux utilisateurs ou à la configuration.
* Utilisez le nouveau service Diagnostics pour résoudre les problèmes.
* Gérez uniquement l’image et les machines virtuelles, pas l’infrastructure. Vous n’avez pas besoin de gérer personnellement les rôles Bureau à distance comme vous le faites avec les Services Bureau à distance, mais uniquement les machines virtuelles dans votre abonnement Azure.

Vous pouvez également affecter et connecter des utilisateurs à vos bureaux virtuels :

* Une fois affectés, les utilisateurs peuvent lancer n’importe quel client Azure Virtual Desktop pour se connecter à leurs bureaux et applications Windows publiés. Connectez-vous à partir de n’importe quel appareil par le biais d’une application native sur votre appareil ou du client web HTML5 Azure Virtual Desktop.
* Établissez une communication sécurisée entre les utilisateurs et le service par le biais de connexions inverses, vous évitant ainsi de devoir laisser des ports entrants ouverts.

## <a name="requirements"></a>Spécifications

Il existe quelques conditions à remplir avant de configurer Azure Virtual Desktop et de connecter vos utilisateurs à leurs bureaux et applications Windows.

Nous prenons en charge les systèmes d’exploitation suivants ; ainsi, vérifiez que vous disposez des [licences appropriées](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour vos utilisateurs, en fonction de l’appareil de bureau et des applications que vous envisagez de déployer :

|Système d''exploitation|Licence obligatoire|
|---|---|
|Windows 10 Entreprise multisession ou Windows 10 Entreprise|Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows 7 Entreprise |Microsoft 365 E3, E5, A3, A5, F3, Business Premium<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019, 2022|Licence d’Accès Client (CAL) des services Bureau à distance avec Software Assurance|

Votre infrastructure doit contenir les éléments suivants pour prendre en charge Azure Virtual Desktop :

* Une instance [Azure Active Directory](../active-directory/index.yml).
* Un annuaire Windows Server Active Directory synchronisé avec Azure Active Directory. Vous pouvez configurer cela à l’aide d’Azure AD Connect (pour les organisations hybrides) ou d’Azure AD Domain Services (pour les organisations hybrides ou cloud).
  * Un domaine Windows Server AD synchronisé avec Azure Active Directory. L’utilisateur provient de Windows Server AD et la machine virtuelle Azure Virtual Desktop est jointe au domaine Windows Server AD.
  * Un domaine Windows Server AD synchronisé avec Azure Active Directory. L’utilisateur provient de Windows Server AD et la machine virtuelle Azure Virtual Desktop est jointe au domaine Azure AD Domain Services.
  * Un domaine Azure AD Domain Services. L’utilisateur provient d’Azure Active Directory et la machine virtuelle Azure Virtual Desktop est jointe au domaine Azure AD Domain Services.
* Un abonnement Azure, apparenté au même locataire Azure AD, comprenant un réseau virtuel qui contient l’instance Windows Server Active Directory ou Azure AD DS, ou qui y est connecté.

Conditions utilisateur nécessaires pour se connecter à Azure Virtual Desktop :

* L’utilisateur doit provenir de la même instance Active Directory que celle qui est connectée à Azure AD. Azure Virtual Desktop ne prend pas en charge les comptes B2B ni MSA.
* L’UPN que vous utilisez pour vous abonner à Azure Virtual Desktop doit se trouver dans le domaine Active Directory auquel la machine virtuelle est jointe.

Les machines virtuelles Azure que vous créez pour Azure Virtual Desktop doivent :

* être [standard jointes à un domaine](../active-directory-domain-services/compare-identity-solutions.md) ou [hybrides jointes à AD](../active-directory/devices/hybrid-azuread-join-plan.md). Les machines virtuelles [jointes à Azure AD](deploy-azure-ad-joined-vm.md) sont disponibles en version préliminaire.
* exécuter l’une des images de [système d’exploitation prises en charge](#supported-virtual-machine-os-images) suivantes.

>[!NOTE]
>Si vous avez besoin d’un abonnement Azure, vous pouvez demander un [essai gratuit d’un mois](https://azure.microsoft.com/free/). Si vous utilisez l’essai gratuit d’Azure, vous devez utiliser Azure AD Domain Services pour synchroniser votre annuaire Windows Server Active Directory avec Azure Active Directory.

Si vous souhaitez obtenir la liste des URL que vous devez débloquer pour que votre déploiement Azure Virtual Desktop fonctionne comme prévu, consultez la [liste des URL requises](safe-url-list.md).

Azure Virtual Desktop comprend les bureaux et applications Windows que vous fournissez aux utilisateurs ainsi que la solution de gestion, qui est hébergée en tant que service sur Azure par Microsoft. Les bureaux et applications peuvent être déployés sur des machines virtuelles dans n’importe quelle région Azure, et la solution de gestion et les données pour ces machines virtuelles résident aux États-Unis. Cela peut entraîner le transfert des données vers les États-Unis.

Pour bénéficier de performances optimales, vérifiez que votre réseau remplit les conditions suivantes :

* La latence aller-retour entre le réseau du client et la région Azure où les pools hôtes ont été déployés doit être inférieure à 150 ms Utilisez l’[estimateur d’expérience](https://azure.microsoft.com/services/virtual-desktop/assessment) pour afficher l’intégrité de votre connexion et votre région Azure recommandée.
* Le trafic réseau peut circuler au-delà des frontières du pays ou de la région lorsque des machines virtuelles qui hébergent des bureaux et des applications se connectent au service de gestion
* Pour optimiser les performances du réseau, nous vous recommandons de localiser les machines virtuelles de l’hôte de session dans la région Azure la plus proche de l’utilisateur.

Vous pouvez consulter une configuration architecturale classique d’Azure Virtual Desktop pour l’entreprise dans notre [documentation d’architecture](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## <a name="supported-remote-desktop-clients"></a>Clients Bureau à distance pris en charge

Les clients Bureau à distance suivants prennent en charge Azure Virtual Desktop :

* [Windows Desktop](./user-documentation/connect-windows-7-10.md)
* [Web](./user-documentation/connect-web.md)
* [macOS](./user-documentation/connect-macos.md)
* [iOS](./user-documentation/connect-ios.md)
* [Android](./user-documentation/connect-android.md)
* Client Microsoft Store

> [!IMPORTANT]
> Azure Virtual Desktop ne prend pas en charge le client RADC (Connexions RemoteApp et Bureau à distance) ni le client MSTSC (Connexion Bureau à distance).

Pour connaître les URL que vous devez débloquer pour utiliser les clients, consultez la [liste des URL sécurisées](safe-url-list.md).

## <a name="supported-virtual-machine-os-images"></a>Images de système d’exploitation de machine virtuelle prises en charge

Azure Virtual Desktop suit la [Stratégie de cycle de vie de Microsoft](/lifecycle/) et prend en charge les images de système d’exploitation x64 suivantes :

* Windows 11 Entreprise multisession (préversion)
* Windows 11 Entreprise (préversion)
* Windows 10 Entreprise multisession
* Windows 10 Entreprise
* Windows 7 Entreprise
* Windows Server 2022
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Azure Virtual Desktop ne prend pas en charge les images de système d’exploitation x86 (32 bits), Windows 10 Entreprise N, Windows 10 LTSB, Windows 10 LTSC, Windows 10 Professionnel ou Windows 10 Entreprise KN. Windows 7 ne prend pas non plus en charge les solutions de profil VHD ou VHDX hébergées sur le stockage Azure managé en raison d’une limitation de la taille de secteur.

Les options d’automatisation et de déploiement qui sont disponibles dépendent du système d’exploitation et de la version que vous choisissez, comme indiqué dans le tableau suivant :

|Système d’exploitation|Galerie d’images Azure|Déploiement manuel d’une machine virtuelle|Intégration du modèle Azure Resource Manager|Provisionner des pools d’hôtes sur la Place de marché Azure|
|--------------------------------------|:------:|:------:|:------:|:------:|
|Windows 11 Entreprise multisession (préversion)|Oui|Oui|Oui|Oui|
|Windows 11 Entreprise (préversion)|Oui|Oui|Oui|Oui|
|Windows 10 Entreprise multisession, version 1909 et ultérieure|Oui|Oui|Oui|Oui|
|Windows 10 Entreprise, version 1909 et ultérieure|Oui|Oui|Oui|Oui|
|Windows 7 Entreprise|Oui|Oui|Non|Non|
|Windows Server 2022|Oui|Oui|Non|Non|
|Windows Server 2019|Oui|Oui|Non|Non|
|Windows Server 2016|Oui|Oui|Oui|Oui|
|Windows Server 2012 R2|Oui|Oui|Non|Non|

## <a name="next-steps"></a>Étapes suivantes

Si vous utilisez Azure Virtual Desktop (classique), vous pouvez consulter l’article [Créer un locataire dans Azure Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) pour vous familiariser avec notre tutoriel.

Si vous utilisez Azure Virtual Desktop avec l’intégration Azure Resource Manager, vous devez créer un pool d’hôtes à la place. Pour commencer, consultez le tutoriel suivant.

> [!div class="nextstepaction"]
> [Créer un pool d’hôtes avec le portail Azure](create-host-pools-azure-marketplace.md)
