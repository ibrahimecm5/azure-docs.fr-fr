---
title: Diagnostics de connexion pour les scénarios Azure AD
description: Répertorie les scénarios pris en charge par les diagnostics de connexion pour Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/08/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f530f406f049876ca393610dc7b7fa02433116a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997150"
---
# <a name="sign-in-diagnostics-for-azure-ad-scenarios"></a>Diagnostics de connexion pour les scénarios Azure AD

Vous pouvez utiliser le diagnostic de connexion pour Azure AD pour analyser ce qui s’est produit lors d’une tentative de connexion et obtenir des suggestions afin de résoudre les problèmes sans avoir à contacter le support Microsoft.  

Cet article vous donne une vue d’ensemble des types de scénarios que vous pouvez identifier et résoudre lors de l’utilisation de cet outil.

## <a name="supported-scenarios"></a>Scénarios pris en charge

Le diagnostic de connexion pour Azure AD vous offre la prise en charge des scénarios suivants :

- **Accès conditionnel**

    - Blocage par l’accès conditionnel  

    - Échec de l’accès conditionnel  

    - Authentification multifacteur (MFA) à partir de l’accès conditionnel  

    - Connexion bloquée B2B due à un accès conditionnel 

- **Authentification multifacteur (MFA)**  

    - Authentification multifacteur à partir d’autres conditions exigées  

    - Validation par l’authentification multifacteur exigée  

    - Validation par l’authentification multifacteur exigée (emplacement de connexion risqué)  

- **Informations d’identification correctes et incorrectes**  

    - Connexion réussie  

    - Compte verrouillé  

    - Nom d’utilisateur ou mot de passe non valide  

- **Applications d’entreprise**  

    - Fournisseur de services des applications d’entreprise  

    - Configuration des applications d’entreprise  

- **Autres scénarios**   

    - Paramètres de sécurité par défaut  
    
    - Insights du code d’erreur  

    - Authentification héritée  

    - Bloqué par la stratégie de risque 







## <a name="conditional-access"></a>Accès conditionnel  


### <a name="blocked-by-conditional-access"></a>Blocage par l’accès conditionnel 

Dans ce scénario, une tentative de connexion a été bloquée par une stratégie d’accès conditionnel. 


![Capture d’écran montrant la configuration de l’accès avec l’option Bloquer l’accès sélectionnée.](./media/concept-sign-in-diagnostics-scenarios/block-access.png)

La section de diagnostic de ce scénario affiche des informations détaillées sur l’événement de connexion utilisateur et les stratégies appliquées. 

 

### <a name="failed-conditional-access"></a>Échec de l’accès conditionnel 

Ce scénario est le résultat classique de l’échec d’une tentative de connexion dû à une stratégie d’accès conditionnel dont les conditions n’ont pas été remplies. Voici des exemples courants : 



![Capture d’écran montrant la configuration de l’accès avec des exemples de stratégie courants et l’option Accorder l’accès sélectionnée.](./media/concept-sign-in-diagnostics-scenarios/require-controls.png)

- Exiger un appareil joint à Azure AD hybride 

- Demander une application cliente approuvée 

- Exiger une stratégie de protection des applications 

La section de diagnostic de ce scénario affiche des informations détaillées sur la tentative de connexion utilisateur et les stratégies appliquées. 

 

### <a name="mfa-from-conditional-access"></a>Authentification multifacteur à partir de l’accès conditionnel 

Dans ce scénario, une stratégie d’accès conditionnel impose que l’utilisateur se connecte au moyen de l’authentification multifacteur. 



![Capture d’écran montrant la configuration de l’accès avec l’option Exiger l’authentification multifacteur sélectionnée.](./media/concept-sign-in-diagnostics-scenarios/require-mfa.png)

La section de diagnostic de ce scénario affiche des informations détaillées sur la tentative de connexion utilisateur et les stratégies appliquées. 

 

 

## <a name="multifactor-authentication"></a>Authentification multifacteur  

### <a name="mfa-from-other-requirements"></a>Authentification multifacteur à partir d’autres conditions exigées 

Dans ce scénario, une exigence relative à l’authentification multifacteur n’a pas été appliquée par une stratégie d’accès conditionnel. Par exemple, l’authentification multifacteur par utilisateur. 



![Capture d’écran montrant la configuration de l’authentification multifacteur par utilisateur.](./media/concept-sign-in-diagnostics-scenarios/mfa-per-user.png) 

Le but de ce scénario de diagnostic est de fournir plus de détails sur les éléments suivants : 

- Source de l’authentification multifacteur interrompue 

- Résultat de l’interaction avec le client 

Vous pouvez également afficher tous les détails de la tentative de connexion de l’utilisateur. 

 

### <a name="mfa-proof-up-required"></a>Validation par l’authentification multifacteur exigée 

Dans ce scénario, les tentatives de connexion ont été interrompues par des demandes de configuration de l’authentification multifacteur. Cette configuration est également appelée « validation ». 

 

La validation par l’authentification multifacteur se produit lorsqu’un utilisateur est obligé d’utiliser l’authentification multifacteur mais qu’il ne l’a pas encore configurée ou lorsqu’un administrateur a exigé de l’utilisateur qu’il la configure. 

 

L’objectif de ce scénario de diagnostic est de révéler que l’interruption de l’authentification multifacteur était due à une absence de configuration par l’utilisateur. La solution recommandée consiste à ce que l’utilisateur effectue la validation. 

 

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Validation par l’authentification multifacteur exigée (emplacement de connexion risqué) 

Dans ce scénario, les tentatives de connexion ont été interrompues par une demande de configuration de l’authentification multifacteur à partir d’un emplacement de connexion risqué. 

 

L’objectif de ce scénario de diagnostic est de révéler que l’interruption de l’authentification multifacteur était due à une absence de configuration par l’utilisateur. La solution recommandée consiste à ce que l’utilisateur effectue la validation, plus précisément à partir d’un emplacement réseau qui n’est pas risqué. 

 

Par exemple, ce scénario se produit lorsque la stratégie exige que l’utilisateur configure l’authentification MFA uniquement à partir d’emplacements réseau approuvés, mais que l’utilisateur se connecte à partir d’un emplacement réseau non approuvé. 

 

## <a name="correct--incorrect-credential"></a>Informations d’identification correctes et incorrectes

### <a name="successful-sign-in"></a>Connexion réussie 

Dans ce scénario, les événements de connexion ne sont pas interrompus par l’accès conditionnel ou l’authentification multifacteur.  

 

Ce scénario de diagnostic fournit des détails sur les événements de connexion utilisateur qui étaient censés être interrompus en raison des stratégies d’accès conditionnel ou de l’authentification multifacteur. 

 

### <a name="the-account-is-locked"></a>Le compte est verrouillé 

Dans ce scénario, un utilisateur s’est connecté avec des informations d’identification incorrectes trop souvent. Ce scénario se produit lorsque trop de tentatives de connexion basées sur un mot de passe se sont produites avec des informations d’identification incorrectes. Le scénario de diagnostic fournit des informations à l’administrateur pour déterminer l’origine des tentatives et s’il s’agit de tentatives légitimes de connexion de l’utilisateur ou pas. 

 

Ce scénario de diagnostic fournit des détails sur les applications, le nombre de tentatives, l’appareil utilisé, le système d’exploitation et l’adresse IP. 

 

Vous trouverez plus d’informations sur cette rubrique dans la documentation Verrouillage intelligent Azure AD. 

 

 

### <a name="invalid-username-or-password"></a>Nom d’utilisateur ou mot de passe non valide 

Dans ce scénario, un utilisateur a essayé de se connecter à l’aide d’un nom d’utilisateur ou d’un mot de passe non valide. Le diagnostic est destiné à permettre à un administrateur de déterminer si le problème est lié à l’entrée d’informations d’identification incorrectes par un utilisateur ou un client et/ou à une ou des applications, qui ont mis en cache un ancien mot de passe et le renvoyent. 

 

Ce scénario de diagnostic fournit des détails sur les applications, le nombre de tentatives, l’appareil utilisé, le système d’exploitation et l’adresse IP. 

 

## <a name="enterprise-app"></a>Applications d’entreprise 

Dans les applications d’entreprise, il existe deux points où des problèmes peuvent se produire : 

- Configuration de l’application du fournisseur d’identité (Azure AD) 
- Du côté du fournisseur de services (service d’application, également appelé application SaaS)

 

Les diagnostics de ces problèmes résolvent la partie du problème qui doit être examinée pour la résolution et la marche à suivre. 

 

### <a name="enterprise-apps-service-provider"></a>Fournisseur de services des applications d’entreprise 

Dans ce scénario, un utilisateur a essayé de se connecter à une application. La connexion a échoué en raison d’un problème avec le côté de l’application (également appelé fournisseur de services) du flux de connexion. Les problèmes détectés par ce diagnostic doivent généralement être résolus en modifiant la configuration ou en corrigeant les problèmes sur le service d’applications.  

La résolution de ce scénario consiste à se connecter à l’autre service et à modifier une configuration conformément aux instructions de diagnostic. 

 

### <a name="enterprise-apps-configuration"></a>Configuration des applications d’entreprise 

Dans ce scénario, une connexion a échoué en raison d’un problème de configuration de l’application pour le côté Azure AD de l’application. 

 

Pour résoudre ce scénario, vous devez analyser et mettre à jour la configuration de l’application dans l’entrée du panneau Applications de l’application pour l’application. 

 

## <a name="other-scenarios"></a>Autres scénarios 

### <a name="security-defaults"></a>Paramètres de sécurité par défaut 

Ce scénario couvre les événements de connexion où la connexion de l’utilisateur a été interrompue en raison des paramètres de sécurité par défaut. Les valeurs par défaut de sécurité appliquent les meilleures pratiques en matière de sécurité pour votre organisation et nécessitent la configuration et l’utilisation d’une authentification multifacteur (MFA) dans de nombreux scénarios pour empêcher la réussite de la pulvérisation du mot de passe, des attaques par relecture et des tentatives d’hameçonnage. 

Pour plus d’informations, consultez [Présentation des paramètres de sécurité par défaut](../fundamentals/concept-fundamentals-security-defaults.md). 

### <a name="error-code-insights"></a>Insights du code d’erreur 

Lorsqu’un événement ne dispose pas d’une analyse contextuelle dans le diagnostic de la connexion, une explication de code d’erreur mise à jour et du contenu pertinent peuvent s’afficher. Les insights du code d’erreur contiennent du texte détaillé sur le scénario, comment corriger le problème et tout le contenu à lire concernant le problème. 

### <a name="legacy-authentication"></a>Authentification héritée 

Ce scénario de diagnostic diagnostique un événement de connexion bloqué ou interrompu depuis que la tentative du client d’utiliser l’authentification de base (également connue sous le nom de Hérité). 

Il est recommandé d’empêcher la connexion d’authentification héritée pour des raisons de sécurité. Les protocoles d’authentification héritée, tels que POP, SMTP, IMAP et MAPI, ne peuvent pas appliquer l’authentification multifacteur (MFA), ce qui en fait des points d’entrée privilégiés pour les personnes malveillantes qui cherchent à attaquer votre organisation. 

Pour plus d’informations, consultez [Comment bloquer l’authentification héritée sur Azure AD avec l’accès conditionnel](../conditional-access/block-legacy-authentication.md). 

### <a name="b2b-blocked-sign-in-due-to-conditional-access"></a>Connexion bloquée B2B due à un accès conditionnel 

Ce scénario de diagnostic détecte une connexion bloquée ou interrompue en raison de l’utilisateur provenant d’une autre organisation : une connexion B2B, où une stratégie d’accès conditionnel requiert que l’appareil du client soit joint à l’abonné des ressources. 

Pour plus d’informations, voir [Accès conditionnel pour les utilisateurs de collaboration B2B](../external-identities/conditional-access.md). 

### <a name="blocked-by-risk-policy"></a>Bloqué par la stratégie de risque 

Dans ce scénario, la stratégie de protection de l’identité bloque une tentative de connexion en raison de la tentative de connexion identifiée comme risquée. 

Pour plus d’informations, consultez [Comment configurer et activer les stratégies de risque](../identity-protection/howto-identity-protection-configure-risk-policies.md). 


### <a name="pass-through-authentication"></a>Authentification directe 

Étant donné que l’authentification directe est une intégration des technologies d’authentification locale et cloud, il peut être difficile de déterminer où se trouve le problème. Ce diagnostic est conçu pour rendre ces scénarios plus faciles à diagnostiquer et à résoudre. 

Ce scénario de diagnostic identifie les problèmes de connexion spécifiques à l’utilisateur lorsque la méthode d’authentification utilisée est l’authentification directe (PTA, pass through authentication) et qu’il y a une erreur spécifique à cette authentification. Des erreurs liées à d’autres problèmes, même lorsque l’authentification PTA est utilisée, seront quand même diagnostiquées correctement. 

Le diagnostic affiche des informations contextuelles sur l’échec et l’utilisateur qui se connecte, les raisons supplémentaires de l’échec de la connexion et les actions recommandées que l’administrateur peut entreprendre pour résoudre le problème. Pour plus d’informations, consultez [Azure AD Connect : Résolution des problèmes d’authentification directe](../hybrid/tshoot-connect-pass-through-authentication.md). 

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que le diagnostic de connexion dans Azure AD ?](overview-sign-in-diagnostics.md)
