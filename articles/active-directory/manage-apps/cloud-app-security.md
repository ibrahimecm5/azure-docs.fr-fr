---
title: Visibilité et contrôle des applications avec Microsoft Defender pour les applications cloud
titleSuffix: Azure AD
description: Découvrez des façons d’identifier les niveaux de risque des applications, stopper les violations et les fuites en temps réel et utiliser des connecteurs d’application pour tirer parti d’API de fournisseurs favorisant la visibilité et la gouvernance.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/29/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: bokacevi, dacurwin
ms.openlocfilehash: e1cc07606664d39b6cc6e1c030935323d868b2a9
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549087"
---
# <a name="cloud-app-visibility-and-control"></a>Visibilité et contrôle des applications cloud

Pour tirer pleinement parti des applications et des services cloud, une équipe informatique doit trouver le bon équilibre entre faciliter l’accès et garder le contrôle de façon à protéger les données critiques. Microsoft Cloud App Security offre une visibilité complète, un contrôle sur le déplacement des données et des capacités analytiques sophistiquées pour identifier et combattre les cybermenaces dans l’ensemble de vos services Microsoft et tiers.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Découvrir et gérer le Shadow IT dans votre réseau

À la question « Combien d’applications cloud vos employés utilisent-ils ? », les administrateurs informatiques avancent en moyenne le nombre de 30 ou 40. Or, dans la réalité, ce sont plus de 1 000 applications distinctes qui sont utilisées par les employés d’une grande entreprise. Le Shadow IT vous aide à identifier les applications qui sont utilisées ainsi que le niveau de risque associé. Quatre-vingt pour cent des employés utilisent des applications non approuvées que personne n’a examinées et qui peuvent ne pas être conformes à vos stratégies de sécurité et de conformité. Et sachant que vos employés peuvent accéder à vos ressources et applications en dehors de votre réseau d’entreprise, il ne suffit plus de définir des règles et des stratégies sur vos pare-feu.

Microsoft Cloud App Discovery (fonctionnalité Azure Active Directory Premium P1) permet de découvrir les applications qui sont utilisées, d’explorer les risques associés à ces applications, de configurer des stratégies pour identifier de nouvelles applications à risque et de désapprouver ces applications de façon à les bloquer en mode natif à l’aide de votre appliance de pare-feu ou proxy.

- Découvrir et identifier Shadow IT
- Évaluer et analyser
- Gérer vos applications
- Génération de rapports Shadow IT Discovery avancés
- Contrôler les applications approuvées

### <a name="learn-more"></a>En savoir plus

- [Découvrir et gérer le Shadow IT dans votre réseau](/cloud-app-security/tutorial-shadow-it)
- [Applications découvertes avec Defender pour les applications Cloud](/cloud-app-security/discovered-apps)

## <a name="user-session-visibility-and-control"></a>Visibilité et contrôle de session utilisateur

Dans l’espace de travail d’aujourd’hui, il ne suffit pas de savoir ce qu’il se passe dans votre environnement cloud après que les faits se sont produits. Les violations et les fuites doivent être arrêtées en temps réel avant que les employés exposent de façon intentionnelle ou involontaire les données et l’organisation à des risques. De concert avec Azure Active Directory (Azure AD), Microsoft Defender pour les applications cloud permet cela à travers une expérience globale et intégrée au moyen du contrôle d’application par accès conditionnel.

Le contrôle de session utilise une architecture de proxy inverse et est intégré de manière unique à l’accès conditionnel Azure AD. L’accès conditionnel Azure AD vous permet d’appliquer des contrôles d’accès aux applications de votre organisation en fonction de certaines conditions. Les conditions définissent à qui (utilisateur ou groupe d’utilisateurs), à quoi (quelles applications cloud) et où (quels emplacements et réseaux) s’applique une stratégie d’accès conditionnel. Une fois que vous avez défini les conditions, vous pouvez router les utilisateurs vers Defender pour les applications cloud pour les applications cloud où vous pouvez protéger les données en temps réel.  

Ce contrôle vous offre les possibilités suivantes :

- Contrôler les téléchargements de fichiers
- Superviser les scénarios B2B  
- Contrôler l’accès aux fichiers  
- Protéger les documents pour le téléchargement  

### <a name="learn-more"></a>En savoir plus

- [Protéger les applications avec le contrôle de session dans Defender pour les applications cloud](/cloud-app-security/proxy-intro-aad)

## <a name="advanced-app-visibility-and-controls"></a>Visibilité et contrôle avancés des applications

Les connecteurs d’applications utilisent les API de fournisseurs d’applications pour que Microsoft Defender pour les applications cloud bénéficie d’une plus grande visibilité et d’un plus grand contrôle sur les applications auxquelles vous vous connectez.
Defender pour les applications cloud exploite les API fournies par le fournisseur de cloud. L’infrastructure et les API propres à chaque service ont des limitations, notamment en matière de bande passante, de limites d’API, de fenêtres d’API de décalage temporel dynamique, etc. L’équipe du produit Defender pour les applications cloud a collaboré avec les fournisseurs de ces services pour optimiser l’utilisation des API et assurer le meilleur niveau de performance. En prenant en compte les différentes limitations que les services imposent à leurs API, les moteurs Defender pour les applications cloud utilisent leur capacité maximale autorisée. Étant donné que certaines opérations, comme l’analyse de l’ensemble des fichiers d’un locataire, exigent de nombreux appels d’API, elles sont étendues sur une plus longue période. Plusieurs heures ou jours peuvent être nécessaires à l’exécution de certaines stratégies.

### <a name="learn-more"></a>En savoir plus

- [Connecter des applications dans Defender pour les applications Cloud](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir et gérer le Shadow IT dans votre réseau](/cloud-app-security/tutorial-shadow-it)
- [Applications découvertes avec Defender pour les applications Cloud](/cloud-app-security/discovered-apps)
- [Protéger les applications avec le contrôle de session dans Defender pour les applications cloud](/cloud-app-security/proxy-intro-aad)
- [Connecter des applications dans Defender pour les applications Cloud](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
