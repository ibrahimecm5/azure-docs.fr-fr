---
title: Gestion et supervision des fonctionnalités de sécurité – Microsoft Azure | Microsoft Docs
description: Cet article présente les fonctionnalités et services de sécurité Azure qui facilitent la gestion et la surveillance des services cloud et des machines virtuelles Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: 81306e06d4ac6d0168215cebe7d45b4fb4c051de
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305785"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Présentation de la surveillance et de la gestion de la sécurité Azure
Cet article présente les fonctionnalités et services de sécurité Azure qui facilitent la gestion et la surveillance des services cloud et des machines virtuelles Azure.

## <a name="azure-role-based-access-control"></a>Contrôle d'accès en fonction du rôle Azure

Le contrôle d’accès en fonction du rôle Azure (Azure RBAC) permet de gérer en détail les accès aux ressources Azure. Grâce à Azure RBAC, vous n’accordez aux utilisateurs que les droits d’accès dont ils ont besoin pour effectuer leur travail. Azure RBAC vise également à vous assurer que les utilisateurs perdent l’accès aux ressources dans le cloud lorsqu’ils quittent l’entreprise.

En savoir plus :

* [Blog de l’équipe Active Directory sur Azure RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Contrôle d’accès en fonction du rôle Azure (RBAC Azure)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Logiciel anti-programme malveillant

Azure met à votre disposition des logiciels anti-programmes malveillants provenant de fournisseurs de sécurité reconnus tels que Microsoft, Symantec, Trend Micro, McAfee et Kaspersky. Ces logiciels permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces.

Le logiciel Microsoft Antimalware for Azure Cloud Services and Virtual Machines vous offre la possibilité d’installer un agent anti-programmes malveillants pour les rôles PaaS et les machines virtuelles. Basée sur System Center Endpoint Protection, cette fonctionnalité offre une technologie de sécurité locale éprouvée sur le cloud.

De plus, nous intégrons étroitement les produits [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) et [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) de Trend dans la plateforme Azure. Deep Security est une solution antivirus et SecureCloud, une solution de chiffrement. Deep Security est déployé sur les machines virtuelles via un modèle d’extension. Grâce à l’interface utilisateur du portail Azure et à PowerShell, vous pouvez choisir d’utiliser Deep Security dans les nouvelles machines virtuelles ou dans celles qui sont déjà déployées.

Symantec Endpoint Protection (SEP) est également pris en charge sur Azure. Grâce à l’intégration dans le portail, vous pouvez préciser que vous prévoyez d’utiliser SEP sur une machine virtuelle. SEP peut être installé sur une nouvelle machine virtuelle par le biais du portail Azure, ou sur une machine virtuelle existante à l’aide de PowerShell.

En savoir plus :

* [Déploiement de solutions anti-programmes malveillants sur des machines virtuelles Azure (en anglais)](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](antimalware.md)
* [Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows](/previous-versions/azure/virtual-machines/extensions/trend)
* [Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Windows](../../virtual-machines/extensions/symantec.md)
* [Nouvelles options anti-programmes malveillants pour protéger les machines virtuelles Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication

La méthode d'authentification Azure AD Multi-Factor Authentication requiert l'utilisation de plusieurs méthodes de vérification. Ce service fournit une deuxième couche de sécurité aux connexions et transactions des utilisateurs.

Azure Multi-Factor Authentication contribue à sécuriser l’accès aux données et aux applications, tout en proposant un processus d’authentification simple et conforme à la demande des utilisateurs. Il assure une authentification forte grâce aux diverses options de vérification (appel téléphonique, SMS, notification par application mobile ou code de vérification) et à des jetons OATH tiers.

En savoir plus :

* [Azure Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Présentation d'Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Comment fonctionne l’authentification multifacteur Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Vous pouvez utiliser Microsoft Azure ExpressRoute pour étendre vos réseaux locaux dans Microsoft Cloud via une connexion privée et dédiée, mise en place par un fournisseur de connectivité. Avec ExpressRoute, vous pouvez établir des connexions à des services de cloud computing Microsoft comme Azure, Microsoft 365 et CRM Online. La connectivité peut provenir de :

* Un réseau (VPN IP) universel.
* Un réseau Ethernet point à point.
* Une connexion virtuelle via un fournisseur de connectivité sur un site de colocalisation.

Les connexions ExpressRoute ne passent pas par l’Internet public. Elles offrent une fiabilité accrue, des débits supérieurs, des latences réduites et une sécurité renforcée par rapport aux connexions classiques sur Internet.

En savoir plus :

* [Présentation technique d’ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Passerelles de réseau virtuel

Les passerelles VPN, aussi appelées passerelles de réseau virtuel Azure, servent à envoyer du trafic entre des réseaux virtuels et des emplacements locaux. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure (connexion de réseau à réseau). Les passerelles VPN garantissent la sécurisation de la connectivité entre les locaux entre Azure et votre infrastructure.

En savoir plus :

* [À propos des passerelles VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Vue d’ensemble de la sécurité réseau d’Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Les utilisateurs doivent parfois effectuer des opérations privilégiées dans des ressources Azure ou dans d’autres applications SaaS. Souvent, cela signifie que les entreprises doivent leur octroyer un accès privilégié et permanent à Azure Active Directory (Azure AD).

C’est un risque de sécurité croissant pour les ressources hébergées dans le cloud, car les entreprises ne peuvent pas suffisamment surveiller ce que ces utilisateurs font avec leur accès privilégié. En outre, si un compte d’utilisateur disposant d’un accès privilégié est compromis, cette seule faille peut affecter la sécurité globale du cloud de l’organisation. Azure AD Privileged Identity Management permet de résoudre ce risque en réduisant le temps d’exposition des privilèges et en augmentant la visibilité quant à leur utilisation.  

Privileged Identity Management introduit le concept d’administrateur temporaire pour un rôle ou d’accès administrateur « juste à temps ». Ce type d’administrateur est un utilisateur qui doit suivre un processus d’activation pour obtenir ce rôle. Ce processus d’activation valide l’attribution d’un rôle dans Azure AD à un utilisateur pendant une période spécifiée.

En savoir plus :

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Prise en main d’Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection fournit une vue consolidée des activités de connexion suspectes et des vulnérabilités potentielles pour vous aider à protéger votre entreprise. Il détecte les activités suspectes des utilisateurs et des identités dotées de privilèges (administrateurs), en fonction de signaux tels que :

* Attaques en force brute.
* Informations d’identification divulguées.
* Connexions depuis des emplacements inconnus et d’appareils infectés.

En fournissant des notifications et des mises à jour recommandées, Identity Protection vous permet de limiter les risques en temps réel. Il calcule l’importance du risque pour l’utilisateur. Vous pouvez configurer des stratégies basées sur les risques pour contribuer automatiquement à protéger l’accès à l’application contre les menaces futures.

En savoir plus :

* [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="defender-for-cloud"></a>Defender pour le cloud

Microsoft Defender pour le cloud vous aide à prévenir, détecter les menaces et y répondre. Defender pour le cloud offre une visibilité et un contrôle complets sur la sécurité de vos ressources Azure, ainsi que celles situées dans votre environnement cloud hybride. 

Defender pour le cloud effectue en continu des évaluations de la sécurité de vos ressources connectées et compare leur configuration et leur déploiement au [Benchmark de sécurité Azure](../benchmarks/introduction.md) pour fournir des recommandations de sécurité détaillées adaptées à votre environnement.

Defender pour le cloud vous permet d’optimiser et de surveiller la sécurité de vos ressources Azure grâce aux opérations suivantes :

- Vous pouvez définir des stratégies pour les ressources de votre abonnement Azure, en fonction :
    - Des besoins de sécurité de votre organisation.
    - du type des applications ou de la sensibilité des données dans chaque abonnement.
    - Des normes ou des benchmarks de conformité ou du secteur d’activité que vous appliquez à vos abonnements. 
- Il analyse l’état de vos machines virtuelles, de votre réseau et de vos applications Azure.
- Il fournit une liste hiérarchisée des alertes de sécurité, notamment les alertes envoyées par les solutions partenaires intégrées. Ainsi que les informations dont vous avez besoin pour analyser rapidement une attaque, et des suggestions sur la façon d’y remédier.

En savoir plus :

* [Présentation de Microsoft Defender pour SQL](../../security-center/security-center-introduction.md)
* [Améliorer votre niveau de sécurité dans Microsoft Defender pour le cloud](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph fournit une protection en temps réel contre les menaces dans les produits et services Microsoft. Il utilise une analytique avancée qui associe une multitude de renseignements de thread intelligence et de données de sécurité afin de fournir des insights qui aident les organisations à renforcer leur sécurité. Microsoft utilise une analytique avancée, en traitant plus de 450 milliards d’authentifications par mois, en analysant 400 milliards d’e-mails pour y rechercher des logiciels malveillants et du hameçonnage, et en mettant à jour un milliard d’appareils, dans le but de fournir des insights plus complets. Ces insights peuvent aider votre organisation à détecter les attaques et à y répondre rapidement.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Étapes suivantes
Découvrez le [modèle de responsabilité partagée](shared-responsibility.md), ainsi que les tâches de sécurité respectivement gérées par Microsoft et par vous.

Pour en savoir plus sur la gestion de la sécurité, consultez [Gestion de la sécurité dans Azure](management.md).
