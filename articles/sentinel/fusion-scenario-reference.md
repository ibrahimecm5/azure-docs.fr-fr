---
title: Scénarios détectés par le moteur Fusion de Microsoft Sentinel
description: En savoir plus sur les scénarios détectés par Fusion, répertoriés ici, regroupés par classification des menaces.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5c03e94dfd1bea5453a449d7c6ae045371150b7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523540"
---
# <a name="scenarios-detected-by-the-microsoft-sentinel-fusion-engine"></a>Scénarios détectés par le moteur Fusion de Microsoft Sentinel

Ce document répertorie les types d’attaques multiphases basées sur des scénarios, regroupées par classification des menaces, que Microsoft Sentinel détecte à l’aide du moteur de corrélation Fusion.

Comme [Fusion](fusion.md) met en corrélation plusieurs signaux provenant de divers produits pour détecter les attaques multiphases avancées, les détections réussies de Fusion sont présentées en tant qu’**incidents Fusion** sur la page **Incidents** de Microsoft Sentinel, et non en tant qu’**alertes**, et sont stockées dans la table *Incidents* dans **Journaux**, et non dans la table *SecurityAlerts*.

Pour activer ces scénarios de détection d’attaques par Fusion, les sources de données associées doivent être ingérées dans votre espace de travail Log Analytics.

> [!NOTE]
> Certains de ces scénarios sont en **PRÉVERSION**. Cela est spécifié.


## <a name="compute-resource-abuse"></a>Abus de ressource de calcul

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Plusieurs activités de création de machine virtuelle suite à une connexion suspecte à Azure Active Directory
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, impact 

**Techniques MITRE ATT&CK :** Compte valide (T1078), Détournement de ressource (T1496)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de machines virtuelles ont été créées au cours d’une seule session suite à une connexion suspecte à un compte Azure AD. Ce type d’alerte hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour créer des machines virtuelles à des fins non autorisées, telles que l’exécution d’opérations d’extraction de chiffrement. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’activités multiples de création de machines virtuelles sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, à l’origine d’activités multiples de création de machines virtuelles**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’activités multiples de création de machines virtuelles**

- **Événement de connexion à partir d’un appareil infecté, à l’origine d’activités multiples de création de machines virtuelles**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’activités multiples de création de machines virtuelles**

- **Événement de connexion d’un utilisateur à l’aide d’informations d’identification ayant fuité, à l’origine d’activités multiples de création de machines virtuelles**


## <a name="credential-access"></a>Accès aux informations d’identification
(Nouvelle classification des menaces)

### <a name="multiple-passwords-reset-by-user-following-suspicious-sign-in"></a>Plusieurs mots de passe sont réinitialisés par l’utilisateur après une connexion suspecte
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, accès aux informations d’identification

**Techniques MITRE ATT&CK :** Valid Account (Compte valide) T1078 (Compte valide), Brute Force (Force brute) T1110

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Azure Active Directory Identity Protection

**Description :** les incidents de ce type détectés par Fusion indiquent qu’un utilisateur a réinitialisé plusieurs mots de passe suite à une connexion suspecte à un compte Azure AD. Cette preuve suggère que le compte indiqué dans la description de l’incident Fusion a été compromis et utilisé pour exécuter plusieurs réinitialisations de mot de passe afin d’accéder à plusieurs systèmes et ressources. La manipulation de compte (y compris la réinitialisation de mot de passe) peut aider des personnes malveillantes à conserver l’accès aux informations d’identification et à certains niveaux d’autorisation au sein d’un environnement. Les permutations d’alertes de connexion suspecte à Azure AD avec des alertes suite à plusieurs réinitialisations de mot de passe sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, entraînant plusieurs réinitialisations de mot de passe**

- **Événement de connexion à partir d’un emplacement inhabituel, entraînant plusieurs réinitialisations de mot de passe**

- **Événement de connexion à partir d’un appareil infecté, entraînant plusieurs réinitialisations de mot de passe**

- **Événement de connexion à partir d’une adresse IP anonyme, entraînant plusieurs réinitialisations de mot de passe**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, entraînant plusieurs réinitialisations de mot de passe**

### <a name="suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Connexion suspecte coïncidant avec la réussite d’une connexion à Palo Alto VPN par IP avec plusieurs connexions Azure AD ayant échoué
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, accès aux informations d’identification

**Techniques MITRE ATT&CK :** Valid Account (Compte valide) T1078 (Compte valide), Brute Force (Force brute) T1110

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Azure Active Directory Identity Protection

**Description :** les incidents de ce type signalés par Fusion indiquent qu’une connexion suspecte à un compte Azure AD a coïncidé avec une connexion réussie via un VPN Palo Alto à partir d’une adresse IP à partir de laquelle plusieurs connexions Azure AD ayant échoué se sont produites dans un laps de temps similaire. Même si cela ne constitue pas la preuve d’une attaque multiphases, la corrélation de ces deux alertes de moindre fidélité donne lieu à un incident de haute fidélité suggérant un accès initial malveillant au réseau de l'organisation. Cela peut également indiquer qu’une personne malveillante tente d’utiliser des techniques de force brute pour accéder à un compte Azure AD. Les permutations d’alertes de connexion Azure AD suspectes avec « Une adresse IP avec plusieurs échecs de connexion Azure AD a réussi à se connecter à Palo Alto VPN » sont les suivantes :

- **Déplacement impossible vers un lieu atypique, coïncidant avec une IP qui se connecte avec succès à Palo Alto VPN malgré plusieurs échecs de connexion à Azure AD**

- **Événement de connexion à partir d’un lieu inconnu, coïncidant avec une IP qui se connecte avec succès à Palo Alto VPN malgré plusieurs échecs de connexion à Azure AD**

- **Événement de connexion à partir d’un appareil infecté, coïncidant avec une IP qui se connecte avec succès à Palo Alto VPN malgré plusieurs échecs de connexion à Azure AD**

- **Événement de connexion à partir d’une adresse IP anonyme, coïncidant avec une IP qui se connecte avec succès à Palo Alto VPN malgré plusieurs échecs de connexion à Azure AD**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, coïncidant avec une IP qui se connecte avec succès à Palo Alto VPN malgré plusieurs échecs de connexion à Azure AD**

## <a name="credential-harvesting"></a>Collecte des informations d’identification
(Nouvelle classification des menaces)
### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Exécution de l’outil contre le vol d’informations d’identification suite à une connexion suspecte

**Tactiques MITRE ATT&CK :** Accès initial, accès aux informations d’identification

**Techniques MITRE ATT&CK :** Compte valide (T1078), vidage des informations d’identification du système d’exploitation (T1003)

**Sources de connecteur de données :** Azure Active Directory Identity Protection | Microsoft Defender for Endpoint

**Description :** Les incidents de fusion de ce type indiquent qu’un outil de vol d’informations d’identification connu a été exécuté à la suite d’une connexion Azure AD suspecte. Cette preuve indique avec une grande fiabilité que le compte d’utilisateur mentionné dans la description de l’alerte a été compromis et a peut-être utilisé un outil tel que **Mimikatz** pour collecter des informations d’identification telles que des clés, des mots de passe en texte en clair et/ou des hachages de mot de passe à partir du système. Les informations d’identification collectées peuvent permettre à une personne malveillante d’accéder à des données sensibles, d’escalader des privilèges et/ou de se déplacer latéralement sur le réseau. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’outil de vol d’informations d’identification sont les suivantes :

- **Voyage impossible vers des emplacements inhabituels entraînant l’exécution de l’outil de vol d’informations d’identification**

- **Événement de connexion à partir d’un lieu inconnu entraînant l’exécution de l’outil de vol d’informations d’identification**

- **Événement de connexion à partir d’un appareil infecté entraînant l’exécution de l’outil de vol d’informations d’identification**

- **Événement de connexion à partir d’une adresse IP anonyme entraînant l’exécution de l’outil de vol d’informations d’identification**

- **Événement de connexion de l’utilisateur avec des informations d’identification fuitées entraînant l’exécution de l’outil de vol d’informations d’identification**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Activité suspecte de vol d’informations d’identification suite à une connexion suspecte

**Tactiques MITRE ATT&CK :** Accès initial, accès aux informations d’identification

**Techniques MITRE ATT&CK :** Compte valide (T1078), informations d’identification de magasins de mots de passe (T1555), vidage des informations d’identification du système d’exploitation (T1003)

**Sources de connecteur de données :** Azure Active Directory Identity Protection | Microsoft Defender for Endpoint

**Description :** Les incidents de fusion de ce type indiquent qu’une activité associée à des modèles de vols d’informations d’identification s’est produite à la suite d’une connexion Azure AD suspecte. Cette preuve indique avec une grande fiabilité que le compte d’utilisateur mentionné dans la description de l’alerte a été compromis et utilisé pour voler des informations d’identification telles que des clés, des mots de passe en texte brut, des hachages de mot de passe, et ainsi de suite. Les informations d’identification volées peuvent permettre à une personne malveillante d’accéder à des données sensibles, d’escalader des privilèges et/ou de se déplacer latéralement sur le réseau. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’activité de vol d’informations d’identification sont les suivantes :

- **Voyage impossible vers des emplacements inhabituels entraînant une activité suspecte de vol d’informations d’identification**

- **Événement de connexion à partir d’un lieu inconnu entraînant une activité suspecte de vol d’informations d’identification**

- **Événement de connexion à partir d’un appareil infecté entraînant une activité suspecte de vol d’informations d’identification**

- **Événement de connexion à partir d’une adresse IP anonyme entraînant une activité suspecte de vol d’informations d’identification**

- **Événement de connexion de l’utilisateur avec des informations d’identification fuitées entraînant une activité suspecte de vol d’informations d’identification**

## <a name="crypto-mining"></a>Minage de crypto-monnaies
(Nouvelle classification des menaces)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Activité d’exploration crypto à la suite d’une connexion suspecte

**Tactiques MITRE ATT&CK :** Accès initial, accès aux informations d’identification

**Techniques MITRE ATT&CK :** Compte valide (T1078), Détournement de ressource (T1496)

**Sources des connecteurs de données :** Azure Active Directory Identity Protection, Microsoft Defender pour le cloud

**Description :** Les incidents de fusion de ce type indiquent une activité d’exploration crypto associée à une connexion suspecte à un compte Azure AD. Cette preuve indique avec une grande fiabilité que le compte d’utilisateur mentionné dans la description de l’alerte a été compromis et utilisé pour détourner des ressources de votre environnement afin d’explorer des crypto-monnaies. Cela peut priver vos ressources de puissance de calcul et/ou entraîner des factures d’utilisation du cloud beaucoup plus élevées que prévu. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’activité d’exploration crypto sont les suivantes :  

- **Voyage impossible vers des emplacements inhabituels entraînant une activité d’exploration crypto**

- **Événement de connexion à partir d’un lieu inconnu entraînant une activité d’exploration crypto**

- **Événement de connexion à partir d’un appareil infecté entraînant une activité d’exploration crypto**

- **Événement de connexion à partir d’une adresse IP anonyme entraînant une activité d’exploration crypto**

- **Événement de connexion d’utilisateur avec des informations d’identification fuitées entraînant une activité d’exploration crypto**

## <a name="data-destruction"></a>Destruction de données

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Suppression de fichiers en masse suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, impact

**Techniques MITRE ATT&CK :** Compte valide (T1078), Destruction de données (T1485)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers uniques ont été supprimés suite à une connexion suspecte à un compte Azure AD. Cette preuve indique que le compte signalé dans la description de l’incident par Fusion a peut-être été compromis et a été utilisé pour détruire des données à des fins malveillantes. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte de suppression de fichiers en masse sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’une suppression de fichiers en masse**

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’une suppression de fichiers en masse**

- **Événement de connexion à partir d’un appareil infecté à l’origine d’une suppression de fichiers en masse**

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’une suppression de fichiers en masse**

- **Événement de connexion d’un utilisateur avec des informations d’identification fuitées, à l’origine d’une suppression de fichiers en masse**

### <a name="mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Suppression de fichiers en masse après une connexion réussie à Azure AD à partir d’une adresse IP bloquée par un pare-feu Cisco
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, impact

**Techniques MITRE ATT&CK :** Compte valide (T1078), Destruction de données (T1485)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers uniques ont été supprimés après une connexion réussie à Azure AD réussie, alors que l’adresse IP de l’utilisateur était bloquée par un pare-feu Cisco. Cette preuve indique que le compte signalé dans la description de l’incident par Fusion a été compromis et a été utilisé pour détruire des données à des fins malveillantes. Comme l’adresse IP a été bloquée par le pare-feu, cette même adresse IP qui se connecte avec succès à Azure AD est potentiellement suspecte et pourrait indiquer une compromission des informations d'identification du compte d'utilisateur.

### <a name="mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Suppression en masse de fichiers après une connexion réussie à Palo Alto VPN par une adresse IP avec plusieurs connexions Azure AD ayant échoué
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Initial Access (Accès initial), Credential Access (Accès aux identifiants), Impact

**Techniques MITRE ATT&CK :** Valid Account (Compte valide) T1078, Brute Force (Force brute) T1110, Data Destruction (Destruction des données) T1485

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers uniques ont été supprimés par un utilisateur qui s’est connecté avec succès via un VPN Palo Alto à partir d’une adresse IP à partir de laquelle plusieurs échecs de connexion Azure AD ont eu lieu dans un laps de temps similaire. Cette preuve indique que le compte d’utilisateur signalé dans la description de l’incident par Fusion a peut-être été compromis à l’aide de techniques de force brute et a été utilisé pour détruire des données à des fins malveillantes.

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Activité suspecte de suppression de courrier suite à une connexion suspecte à Azure AD
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, impact 

**Techniques MITRE ATT&CK :** Compte valide (T1078), Destruction de données (T1485)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal d’e-mails ont été supprimés au cours d’une seule session suite à une connexion suspecte à un compte Azure AD. Cette preuve indique que le compte signalé dans la description de l’incident par Fusion a peut-être été compromis et a été utilisé pour détruire des données à des fins malveillantes, telles que nuire à l’organisation ou masquer une activité de courrier indésirable. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’activité suspecte de suppression de courrier sont les suivantes :   

- **Déplacement impossible vers un emplacement atypique, à l’origine d’une activité suspecte de suppression de courrier**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’une activité suspecte de suppression de courrier**

- **Événement de connexion à partir d’un appareil infecté, à l’origine d’une activité suspecte de suppression de courrier**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’une activité suspecte de suppression de courrier**

- **Événement de connexion d’utilisateur avec des informations d’identification fuitées, à l’origine d’une activité suspecte de suppression de courrier**

## <a name="data-exfiltration"></a>Exfiltration de données

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>Activités de transfert de courrier suivant une nouvelle activité de compte d’administrateur qui n’a pas été consultée récemment
Ce scénario appartient à deux classifications de menaces dans cette liste : **exfiltration de données** et **activité administrative malveillante**. Par souci de clarté, il apparaît dans les deux sections.

Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Initial Access (Accès initial), Collection (Collecte), Exfiltration

**Techniques MITRE ATT&CK :** Valid Account (Compte valide) T1078, Email Collection T1114 (Collecte d’e-mail), Exfiltration Over Web Service (Exfiltration sur service Web) T1567

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nouveau compte d’administrateur Exchange a été créé ou qu’un compte d’administrateur Exchange existant a effectué une action administrative pour la première fois, au cours des deux dernières semaines, et que le compte a ensuite effectué des actions de transfert de courrier inhabituelles pour un compte d’administrateur. Cette preuve indique que le compte d’utilisateur signalé dans la description de l’incident par Fusion a été compromis ou manipulé, et qu’il a été utilisé pour exfiltrer des données du réseau de votre organisation.

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Téléchargement de fichiers en masse suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration

**Techniques MITRE ATT&CK :** Compte valide (T1078)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers ont été téléchargés par un utilisateur suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour exfiltrer des données du réseau de votre organisation. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte de téléchargement de fichiers en masse sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’un téléchargement de fichiers en masse**

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un téléchargement de fichiers en masse**

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un téléchargement de fichiers en masse**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’un téléchargement de fichiers en masse**

- **Événement de connexion d’un utilisateur à l’aide d’informations d’identification fuitées, à l’origine d’un téléchargement de fichiers en masse**

### <a name="mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Téléchargement de fichiers en masse après une connexion réussie à Azure AD à partir d’une adresse IP bloquée par un pare-feu Cisco
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration

**Techniques MITRE ATT&CK :** Compte valide (T1078), Exfiltration sur service web (T1567)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers ont été téléchargés après une connexion réussie à Azure AD réussie, alors que l’adresse IP de l’utilisateur était bloquée par un pare-feu Cisco. Il peut s’agir d’une tentative d’un attaquant d’exfiltrer des données à partir du réseau de l’organisation après la compromission d’un compte d'utilisateur. Comme l’adresse IP a été bloquée par le pare-feu, cette même adresse IP qui se connecte avec succès à Azure AD est potentiellement suspecte et pourrait indiquer une compromission des informations d'identification du compte d'utilisateur.

### <a name="mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip"></a>Téléchargement de fichiers en masse coïncidant avec l’opération de fichier SharePoint à partir d’une adresse IP auparavant invisible
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Exfiltration

**Techniques Mitre ATT&CK :** Exfiltration Over Web Service (Exfiltration sur service Web) T1567, Data Transfer Size Limits (Limites de taille de transfert des données) T1030

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de fichiers ont été téléchargés par un utilisateur connecté à partir d’une adresse IP auparavant invisible. Bien qu’elle ne soit pas la preuve d’une attaque multiphases, la corrélation de ces deux alertes de plus faible fidélité aboutit à un incident de haute fidélité suggérant une tentative par un attaquant d’exfiltrer des données du réseau de l’organisation à partir d’un compte d’utilisateur potentiellement compromis. Dans les environnements stables, ces connexions par des adresses IP auparavant invisibles peuvent ne pas être autorisées, surtout si elles sont associées à des pics de volume pouvant être associés à l’exfiltration de documents à grande échelle.

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Partage de fichiers en masse suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration

**Techniques MITRE ATT&CK :** Compte valide (T1078), Exfiltration sur service web (T1567)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un certain nombre de fichiers au-dessus d’un seuil particulier ont été partagés avec d’autres personnes suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour exfiltrer des données du réseau de votre organisation en partageant des fichiers tels que des documents, des feuilles de calcul, et autres, avec des utilisateurs non autorisés, à des fins malveillantes. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte de partage de fichiers en masse sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’un partage de fichiers en masse**

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un partage de fichiers en masse**

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un partage de fichiers en masse**

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’un partage de fichiers en masse**

- **Événement de connexion d’un utilisateur à l’aide d’informations d’identification fuitées, à l’origine d’un partage de fichiers en masse**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Activités multiples de partage de rapport Power BI suite à une connexion suspecte à Azure AD 
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration 

**Techniques MITRE ATT&CK :** Compte valide (T1078), Exfiltration sur service web (T1567)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal rapports Power BI ont été partagés au cours d’une seule session suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour exfiltrer des données du réseau de votre organisation en partageant des rapports Power BI avec des utilisateurs non autorisés, à des fins malveillantes. Les permutations d’alertes de connexion suspecte à Azure AD avec les multiples activités de partage de rapport Power BI sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’activités multiples de partage de rapport Power BI**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’activités multiples de partage de rapport Power BI**

- **Événement de connexion à partir d’une appareil infecté, à l’origine d’activités multiples de partage de rapport Power BI**

- **Événement de connexion d’une adresse IP anonyme, à l’origine d’activités multiples de partage de rapport Power BI**

- **Événement de connexion d’utilisateur avec des informations d’identification fuitées, à l’origine d’activités multiples de partage de rapport Power BI**

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Exfiltration de boîte aux lettres Office 365 suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration, Collection

**Techniques MITRE ATT&CK :** Compte valide (T1078), Collection d’e-mails (T1114), Exfiltration automatisée (T1020)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type détectés par Fusion indiquent qu’une règle de transfert de boîte de réception a été définie pour la boîte de réception d’un utilisateur suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte de l’utilisateur (indiqué dans la description de l’incident fournie par Fusion) a été compromis et utilisé pour exfiltrer des données du réseau de votre organisation en activant une règle de transfert de boîtes aux lettres à l’insu de l’utilisateur véritable. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’exfiltration de boîte aux lettres Office 365 sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, à l’origine de l’exfiltration d’une boîte aux lettres Office 365**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine de l’exfiltration d’une boîte aux lettres Office 365**

- **Événement de connexion à partir d’un appareil infecté à l’origine de l’exfiltration d’une boîte aux lettres Office 365**

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine de l’exfiltration d’une boîte aux lettres Office 365**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine de l’exfiltration d’une boîte aux lettres Office 365**

### <a name="sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection"></a>Opération de fichier SharePoint à partir d’une adresse IP auparavant invisible, suite à la détection d’un programme malveillant
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** exfiltration, évasion de défense

**Techniques MITRE ATT&CK :** limites de taille de transfert de données (T1030)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’une personne malveillante a tenté d’exfiltrer de grandes quantités de données en téléchargeant ou en partageant des informations via SharePoint à l’aide de programmes malveillants. Dans les environnements stables, ces connexions par des adresses IP auparavant invisibles peuvent ne pas être autorisées, surtout si elles sont associées à des pics de volume pouvant être associés à l’exfiltration de documents à grande échelle. 

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Règles de manipulation de boîte de réception définies suite à une connexion suspecte à Azure AD
Ce scénario appartient à deux classifications de menaces dans cette liste : l’**exfiltration de données** et le **déplacement latéral**. Par souci de clarté, il apparaît dans les deux sections.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Déplacement latéral, Exfiltration

**Techniques MITRE ATT&CK :** Compte valide (T1078), Harponnage interne (T1534), Exfiltration automatisée (T1020)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type détectés par Fusion indiquent que des règles anormales ont été définies pour la boîte de réception d’un utilisateur suite à une connexion suspecte à un compte Azure AD. Cette preuve indique avec une grande fiabilité que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour manipuler les règles de la boîte de réception de l’utilisateur à des fins malveillantes, potentiellement pour exfiltrer des données à partir du réseau de l’organisation. L’attaquant pourrait également tenter de générer des e-mails de hameçonnage à partir de l’intérieur de l’organisation (en contournant les mécanismes de détection de hameçonnage ciblant le courrier en provenance de sources externes) à des fins de déplacement latéral en accédant à d’autres comptes utilisateur et/ou privilégiés. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte des règles de manipulation suspecte de boîte de réception sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’un appareil infecté, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion d’utilisateur à l’aide d’informations d’identification fuitées**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Partage de rapport Power BI suite à une connexion suspecte à Azure AD
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Exfiltration 

**Techniques MITRE ATT&CK :** Compte valide (T1078), Exfiltration sur service web (T1567)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type détectés par Fusion indiquent qu’une activité suspecte de partage de rapport Power BI s’est produite suite à une connexion suspecte à un compte Azure AD. L’activité de partage a été identifiée comme suspecte parce que le rapport Power BI contenait des informations sensibles identifiées à l’aide d’un traitement en langage naturel, et a été partagé avec une adresse e-mail externe publiée sur le web ou remis en tant que capture instantanée à une adresse e-mail abonnée en externe. Cette alerte hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour exfiltrer des données sensibles de votre organisation en partageant des rapports Power BI avec des utilisateurs non autorisés à des fins malveillantes. Les permutations d’alertes de connexion suspecte à Azure AD avec le partage suspect de rapport Power BI sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’un partage suspect de rapport Power BI**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’un partage suspect de rapport Power BI**

- **Événement de connexion à partir d’un appareil infecté, à l’origine d’un partage suspect de rapport Power BI**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’un partage suspect de rapport Power BI**

- **Événement de connexion d’utilisateur avec des informations d’identification fuitées, à l’origine d’un partage suspect de rapport Power BI**

## <a name="denial-of-service"></a>Denial of service (déni de service)

### <a name="multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in"></a>Plusieurs activités de suppression de machines virtuelles suite à une connexion suspecte à Azure AD
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, impact

**Techniques MITRE ATT&CK :** Compte valide (T1078), Déni de service de point de terminaison (T1499)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal de machines virtuelles ont été supprimées au cours d’une seule session suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour tenter d’interrompre ou de détruire l’environnement cloud de l’organisation. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte de plusieurs activités de suppression de machines virtuelles sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, entraînant plusieurs activités de suppression de machines virtuelles**

- **Événement de connexion à partir d’un emplacement inhabituel, entraînant plusieurs activités de suppression de machines virtuelles**

- **Événement de connexion à partir d’un appareil infecté, entraînant plusieurs activités de suppression de machines virtuelles**

- **Événement de connexion à partir d’une adresse IP anonyme, entraînant plusieurs activités de suppression de machines virtuelles**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, entraînant plusieurs activités de suppression de machines virtuelles**

## <a name="lateral-movement"></a>Mouvement latéral

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Emprunt d’identité d’Office 365 suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, Déplacement latéral

**Techniques MITRE ATT&CK :** Compte valide (T1078), Harponnage interne (T1534)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal d’actions d’emprunt d’identité ont eu lieu suite à une connexion suspecte à partit d’un compte Azure AD. Dans certains logiciels, il existe des options permettant aux utilisateurs d’emprunter l’identité d’autres utilisateurs. Par exemple, les services de courrier permettent aux utilisateurs d’autoriser d’autres utilisateurs à envoyer des e-mails en leur nom. Cette alerte indique avec une degré de fiabilité élevé que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour conduire des activités d’emprunt d’identité à des fins malveillantes, telles que l’envoi de messages de hameçonnage pour la distribution de logiciels malveillants ou un déplacement latéral. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’emprunt d’identité Office 365 sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’un emprunt d’identité Office 365**

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’un emprunt d’identité Office 365**

- **Événement de connexion à partir d’un appareil infecté à l’origine d’un emprunt d’identité Office 365**

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’un emprunt d’identité Office 365**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’un emprunt d’identité Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Règles de manipulation de boîte de réception définies suite à une connexion suspecte à Azure AD
Ce scénario appartient à deux classifications de menaces dans cette liste : le **déplacement latéral** et l’**exfiltration de données**. Par souci de clarté, il apparaît dans les deux sections.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Déplacement latéral, Exfiltration

**Techniques MITRE ATT&CK :** Compte valide (T1078), Harponnage interne (T1534), Exfiltration automatisée (T1020)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type détectés par Fusion indiquent que des règles anormales ont été définies pour la boîte de réception d’un utilisateur suite à une connexion suspecte à un compte Azure AD. Cette preuve indique avec une grande fiabilité que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour manipuler les règles de la boîte de réception de l’utilisateur à des fins malveillantes, potentiellement pour exfiltrer des données à partir du réseau de l’organisation. L’attaquant pourrait également tenter de générer des e-mails de hameçonnage à partir de l’intérieur de l’organisation (en contournant les mécanismes de détection de hameçonnage ciblant le courrier en provenance de sources externes) à des fins de déplacement latéral en accédant à d’autres comptes utilisateur et/ou privilégiés. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte des règles de manipulation suspecte de boîte de réception sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’un emplacement inhabituel, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’un appareil infecté, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine d’une règle de manipulation suspecte de boîte de réception**

- **Événement de connexion d’utilisateur à l’aide d’informations d’identification fuitées**

## <a name="malicious-administrative-activity"></a>Activité administrative malveillante

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Activité administrative d’application cloud suspecte suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, Persistance, Contournement de défense, Déplacement latéral, Collecte, Exfiltration et Impact

**Techniques MITRE ATT&CK :** N/A

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un nombre anormal d’activités administratives ont été effectuées au cours d’une seule session suite à une connexion suspecte à Azure AD à partir du même compte. Cette preuve indique que le compte signalé dans la description de l’incident par Fusion a peut-être été compromis et a été utilisé pour effectuer un nombre quelconque d’actions administratives non autorisées à des fins malveillantes. Cela indique également qu’un compte avec des privilèges administratifs a peut-être été compromis. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’activité administrative d’application cloud suspecte sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine d’une activité administrative suspecte d’application cloud**

- **Événement de connexion à partir d’un lieu inconnu à l’origine d’une activité d’administration suspecte d’application cloud**

- **Événement de connexion à partir d’un appareil infecté à l’origine d’une activité d’administration suspecte d’application cloud**

- **Événement de connexion à partir d’une adresse IP anonyme à l’origine d’une activité d’administration suspecte d’application cloud**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité à l’origine d’une activité d’administration suspecte d’application cloud**

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>Activités de transfert de courrier suivant une nouvelle activité de compte d’administrateur qui n’a pas été consultée récemment
Ce scénario appartient à deux classifications de menaces dans cette liste : **activité administrative malveillante** et **exfiltration de données**. Par souci de clarté, il apparaît dans les deux sections.

Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Initial Access (Accès initial), Collection (Collecte), Exfiltration

**Techniques MITRE ATT&CK :** Valid Account (Compte valide) T1078, Email Collection T1114 (Collecte d’e-mail), Exfiltration Over Web Service (Exfiltration sur service Web) T1567

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent qu’un nouveau compte d’administrateur Exchange a été créé ou qu’un compte d’administrateur Exchange existant a effectué une action administrative pour la première fois, au cours des deux dernières semaines, et que le compte a ensuite effectué des actions de transfert de courrier inhabituelles pour un compte d’administrateur. Cette preuve indique que le compte d’utilisateur signalé dans la description de l’incident par Fusion a été compromis ou manipulé, et qu’il a été utilisé pour exfiltrer des données du réseau de votre organisation.

## <a name="malicious-execution-with-legitimate-process"></a>Exécution malveillante avec un processus légitime

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell a établi une connexion réseau suspecte, suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks.
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Exécution

**Techniques MITRE ATT&CK :** Interpréteur de commandes et de scripts (T1059)

**Sources de connecteur de données :** Microsoft Defender for Endpoint (anciennement Microsoft Defender Advanced Threat Protection, ou MDATP), Palo Alto Networks 

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’une demande de connexion sortante a été effectuée à l’aide d’une commande PowerShell, puis qu’une activité d’entrée anormale a été détectée par le pare-feu Palo Alto Networks. Cette preuve indique qu’un attaquant a probablement obtenu l’accès à votre réseau et tente d’effectuer des actions malveillantes. Les tentatives de connexion par PowerShell qui suivent ce modèle peuvent indiquer une activité de contrôle et de commande de programme malveillant, des demandes de téléchargement de programme malveillant supplémentaires, ou l’établissement d’un accès interactif à distance par une personne malveillante. Comme toutes les attaques « vivant de la terre », cette activité pourrait être une utilisation légitime de PowerShell. Toutefois, l’exécution de la commande PowerShell suivie d’une activité de pare-feu entrante suspecte renforce la suspicion que PowerShell est utilisé de manière malveillante et doit être examiné de plus près. Dans les journaux Palo Alto, Microsoft Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects). Référencez également le journal des menaces Palo Alto correspondant au [type de menace/contenu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) indiqué dans la description de l’incident par Fusion pour obtenir des détails supplémentaires sur l’alerte.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Exécution suspecte de WMI à distance, suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Exécution, Détection

**Techniques MITRE ATT&CK :** Windows Management Instrumentation (T1047)

**Sources de connecteur de données :** Microsoft Defender for Endpoint (anciennement MDATP), Palo Alto Networks 

**Description :** Les incidents de ce type signalés par Fusion indiquent que des commandes Windows Management Instrumentation (WMI) ont été exécutées à distance sur un système, et qu’une activité entrante suspecte a été détectée par le pare-feu Palo Alto Networks. Cette preuve indique qu’un attaquant a peut-être eu accès à votre réseau et tente de se déplacer latéralement, d’élever les privilèges et/ou d’exécuter des charges utiles malveillantes. Comme toutes les attaques « vivant de la terre », cette activité pourrait être une utilisation légitime de WMI. Toutefois, l’exécution de la commande WMI suivie d’une activité de pare-feu entrante suspecte renforce la suspicion que WMI est utilisé de manière malveillante et doit être examiné de plus près. Dans les journaux Palo Alto, Microsoft Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects). Référencez également le journal des menaces Palo Alto correspondant au [type de menace/contenu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) indiqué dans la description de l’incident par Fusion pour obtenir des détails supplémentaires sur l’alerte.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Ligne de commande PowerShell suspecte après une connexion suspecte

**Tactiques MITRE ATT&CK :** Accès initial, Exécution

**Techniques MITRE ATT&CK :** Compte valide (T1078), Interpréteur de commandes et de scripts (T1059)

**Sources de connecteur de données :** Azure Active Directory Identity Protection, Microsoft Defender for Endpoint (anciennement MDATP)

**Description :** Les incidents de fusion de ce type indiquent qu’un utilisateur a exécuté des commandes PowerShell potentiellement malveillantes suite à une connexion suspecte à un compte Azure AD. Cette preuve indique avec une grande fiabilité que le compte mentionné dans la description de l’alerte a été compromis et que des actions malveillantes supplémentaires ont été effectuées. Les attaquants utilisent souvent PowerShell pour exécuter des charges utiles malveillantes en mémoire sans laisser d’artefacts sur le disque, afin d’éviter la détection par des mécanismes de sécurité sur disque tels que les antivirus. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte de commande PowerShell suspecte sont les suivantes :

- **Voyage impossible vers des emplacements inhabituels entraînant une ligne de commande PowerShell suspecte**

- **Événement de connexion à partir d’un lieu inconnu entraînant une ligne de commande PowerShell suspecte**

- **Événement de connexion à partir d’un appareil infecté entraînant une ligne de commande PowerShell suspecte**

- **Événement de connexion à partir d’une adresse IP anonyme entraînant une ligne de commande PowerShell suspecte**

- **Événement de connexion d’utilisateur avec des informations d’identification fuitées entraînant une ligne de commande PowerShell suspecte**

## <a name="malware-c2-or-download"></a>Logiciel malveillant C2 ou téléchargement

### <a name="beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service"></a>Modèle de balise détecté par Fortinet après l’échec de plusieurs connexions à un service par un utilisateur

Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Initial Access (Accès initial), Command and Control (Commande et contrôle)

**Techniques Mitre ATT&CK :** Valid Account (Compte valide) T1078, Non-Standard Port (Port non standard) T1571, T1065 (retiré)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Microsoft Defender for Cloud Apps

**Description :** les incidents de ce type signalés par Fusion indiquent des modèles de communication, d’une adresse IP interne à une adresse externe, cohérents avec des balises, après l’échec de plusieurs connexions à un service par un utilisateur à partir d’une entité interne associée. La combinaison de ces deux événements peut indiquer une infection par un logiciel malveillant ou un hôte compromis qui procède à l’exfiltration de données. 

### <a name="beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in"></a>Modèle de balise détecté par Fortinet après une connexion Azure AD suspecte

Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Initial Access (Accès initial), Command and Control (Commande et contrôle)

**Techniques Mitre ATT&CK :** Valid Account (Compte valide) T1078, Non-Standard Port (Port non standard) T1571, T1065 (retiré)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Azure Active Directory Identity Protection

**Description :** les incidents de ce type signalés par Fusion indiquent des modèles de communication, d’une adresse IP interne à une adresse externe, cohérents avec des balises, après une connexion suspecte d’un utilisateur à Azure AD. La combinaison de ces deux événements peut indiquer une infection par un logiciel malveillant ou un hôte compromis qui procède à l’exfiltration de données. Les permutations de modèle de balise détectées par des alertes Fortinet avec des alertes de connexion Azure AD suspectes sont les suivantes :   

- **Déplacement impossible vers un emplacement atypique, entraînant un modèle de balise détecté par Fortinet**

- **Événement de connexion à partir d’un emplacement inconnu, entraînant un modèle de balise détecté par Fortinet**

- **Événement de connexion à partir d’un appareil infecté, entraînant un modèle de balise détecté par Fortinet**

- **Événement de connexion à partir d’une adresse IP anonyme, entraînant un modèle de balise détecté par Fortinet**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, entraînant un modèle de balise détecté par Fortinet**

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Demande réseau au service d’anonymisation TOR, suivie d’un trafic anormal signalé par le pare-feu de Palo Alto Networks.
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Commande et contrôle

**Techniques MITRE ATT&CK :** Canal chiffré (T1573), Proxy (T1090)

**Sources de connecteur de données :** Microsoft Defender for Endpoint (anciennement MDATP), Palo Alto Networks 

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’une demande de connexion sortante a été adressée au service d’anonymisation TOR, puis qu’une activité d’entrée anormale a été détectée par le pare-feu de Palo Alto Networks. Cette preuve indique qu’un attaquant a probablement obtenu l’accès à votre réseau et tente de dissimuler ses actions et son intention. Les connexions au réseau TOR qui suivent ce modèle peuvent indiquer une activité de contrôle et de commande de programme malveillant, des demandes de téléchargement de programme malveillant supplémentaires, ou l’établissement d’un accès interactif à distance par une personne malveillante. Dans les journaux Palo Alto, Microsoft Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects). Référencez également le journal des menaces Palo Alto correspondant au [type de menace/contenu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) indiqué dans la description de l’incident par Fusion pour obtenir des détails supplémentaires sur l’alerte.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Connexion sortante à l’adresse IP avec un historique des tentatives d’accès non autorisées suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Commande et contrôle

**Techniques MITRE ATT&CK :** Non applicable

**Sources de connecteur de données :** Microsoft Defender for Endpoint (anciennement MDATP), Palo Alto Networks 

**Description :** Les incidents de fusion de ce type indiquent qu’une connexion sortante à une adresse IP avec un historique de tentatives d’accès non autorisées a été établie, puis qu’une activité anormale a été détectée par le pare-feu Palo Alto Networks. Cette preuve indique qu’un attaquant a probablement obtenu l’accès à votre réseau. Les tentatives de connexion qui suivent ce modèle peuvent indiquer une activité de contrôle et de commande de programme malveillant, des demandes de téléchargement de programme malveillant supplémentaires, ou l’établissement d’un accès interactif à distance par une personne malveillante. Dans les journaux Palo Alto, Microsoft Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects). Référencez également le journal des menaces Palo Alto correspondant au [type de menace/contenu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) indiqué dans la description de l’incident par Fusion pour obtenir des détails supplémentaires sur l’alerte.

## <a name="persistence"></a>Persistance
(Nouvelle classification des menaces)

### <a name="rare-application-consent-following-suspicious-sign-in"></a>Consentement d’application rare après une connexion suspecte

Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Persistence, Initial Access (Accès initial)

**Techniques MITRE ATT&CK :** Create Account (Création de compte) T1136, Valid Account (Compte valide) T1078

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Azure Active Directory Identity Protection

**Description :** les incidents de ce type signalés par Fusion indiquent qu’une application a reçu le consentement d’un utilisateur qui ne l’a jamais ou rarement fait, suite à une connexion suspecte à un compte Azure AD. Cette preuve indique que le compte signalé dans la description de l’incident par Fusion a peut-être été compromis et a été utilisé pour accéder à l’application ou pour manipuler celle-ci à des fins malveillantes.  Les événements Consentement pour une application, Ajouter un principal du service et Ajouter OAuth2PermissionGrant sont généralement rares. Des personnes malveillantes peuvent utiliser ce type de modification de configuration pour établir ou maintenir leur emprise sur les systèmes. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte rare de consentement pour une application sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, entraînant un consentement pour une application rare**

- **Événement de connexion à partir d’un lieu inconnu, entraînant un consentement pour une application rare**

- **Événement de connexion à partir d’un appareil infecté, entraînant un consentement pour une application rare**

- **Événement de connexion à partir d’une adresse IP anonyme, entraînant un consentement pour une application rare**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, entraînant un consentement pour une application rare**

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Exécution de rançongiciel suite à une connexion suspecte à Azure AD

**Tactiques MITRE ATT&CK :** Accès initial, impact

**Techniques MITRE ATT&CK :** Compte valide (T1078), Données chiffrées pour l’impact (T1486)

**Sources des connecteurs de données :** Microsoft Defender for Cloud Apps, Azure Active Directory Identity Protection

**Description :** Les incidents de ce type détectés par Fusion signifient qu’un comportement anormal d’un utilisateur indiquant une attaque de rançongiciel a été détecté suite à une connexion suspecte à un compte Azure AD. Cette indication hautement fiable signifie que le compte signalé dans la description de l’incident par Fusion a été compromis et utilisé pour chiffrer les données à des fins d’extorsion à l’encontre du propriétaire des données ou de refus de l’accès du propriétaire des données à ses données. Les permutations d’alertes de connexion suspecte à Azure AD avec l’alerte d’exécution de rançongiciel sont les suivantes :  

- **Déplacement impossible vers un emplacement atypique, à l’origine du déploiement d’un rançongiciel dans une application cloud**

- **Événement de connexion à partir d’un lieu inconnu à l’origine du déploiement d’un rançongiciel dans une application cloud**

- **Événement de connexion à partir d’un appareil infecté à l’origine du déploiement d’un rançongiciel dans une application cloud**

- **Événement de connexion à partir d’une adresse IP anonyme, à l’origine du déploiement d’un rançongiciel dans une application cloud**

- **Événement de connexion d’un utilisateur avec des informations d’identification fuitées, à l’origine du déploiement d’un rançongiciel dans une application cloud**

## <a name="remote-exploitation"></a>Exploitation à distance

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Utilisation présumée d’infrastructure d’attaque, suivie d’un trafic anormal signalé par le pare-feu Palo Alto Networks
Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, Exécution, Déplacement latéral, Élévation des privilèges

**Techniques MITRE ATT&CK :** Exploitation d’application publique (T1190), Exploitation pour l’exécution de client (T1203), Exploitation de services distants (T1210), Exploitation d’élévation des privilèges (T1068)

**Sources de connecteur de données :** Microsoft Defender for Endpoint (anciennement MDATP), Palo Alto Networks 

**Description :** Les incidents de ce type signalés par Fusion indiquent que des utilisations non standard de protocoles, ressemblant à l’utilisation d’infrastructures d’attaque telles que Metasploit, ont été détectées, puis qu’une activité entrante suspecte a été détectée par le pare-feu Palo Alto Networks. Il peut s’agir d’une indication initiale qu’un attaquant a exploité un service pour accéder à vos ressources réseau, ou a déjà obtenu l’accès à celui-ci et tente d’exploiter davantage les systèmes/services disponibles pour déplacer latéralement ou élever des privilèges. Dans les journaux Palo Alto, Microsoft Sentinel se concentre sur les [journaux des menaces](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), et le trafic est considéré comme suspect lorsque des menaces sont autorisées (données, fichiers, saturations, paquets, analyses, logiciels espions, URL, virus, vulnérabilités, virus d’incendie, incendies suspects). Référencez également le journal des menaces Palo Alto correspondant au [type de menace/contenu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) indiqué dans la description de l’incident par Fusion pour obtenir des détails supplémentaires sur l’alerte.

## <a name="resource-hijacking"></a>Piratage des ressources
(Nouvelle classification des menaces)

### <a name="suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in"></a>Déploiement suspect de ressource/groupe de ressources par un appelant auparavant invisible suite à une connexion Azure AD suspecte
Ce scénario utilise les alertes produites par des **règles analytiques planifiées**.

Ce scénario est actuellement en **PRÉVERSION**.

**Tactiques MITRE ATT&CK :** Accès initial, impact

**Techniques MITRE ATT&CK :** Compte valide (T1078), Détournement de ressource (T1496)

**Sources des connecteurs de données :** Microsoft Sentinel (règle d’analyse planifiée), Azure Active Directory Identity Protection

**Description :** Les incidents de ce type signalés par Fusion indiquent qu’un utilisateur a déployé une ressource ou un groupe de ressources Azure (une activité rare), à la suite d’une connexion suspecte, avec des propriétés qui n’étaient pas affichées récemment, à un compte Azure AD. Cela peut être une tentative par une personne malveillante de déployer des ressources ou des groupes de ressources à des fins malveillantes après avoir compromis le compte d’utilisateur indiqué dans la description de l’incident Fusion.

Les permutations d’alertes de connexion Azure AD suspectes avec le déploiement suspect d’une ressource ou d’un groupe de ressources par une alerte d’appelant auparavant invisible sont les suivantes :

- **Déplacement impossible vers un emplacement atypique, entraînant un déploiement suspect de ressources/groupes de ressources par un appelant auparavant invisible**

- **Événement de connexion à partir d’un lieu inconnu, entraînant un déploiement suspect de ressources/groupes de ressources par un appelant auparavant invisible**

- **Événement de connexion à partir d’un appareil infecté, entraînant un déploiement suspect de ressources/groupes de ressources par un appelant auparavant invisible**

- **Événement de connexion à partir d’une adresse IP anonyme, entraînant un déploiement suspect de ressources/groupes de ressources par un appelant auparavant invisible**

- **Événement de connexion d’un utilisateur dont les informations d’identification ont fuité, entraînant un déploiement suspect de ressources/groupes de ressources par un appelant auparavant invisible**

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez plus sur la détection des attaques multiphases avancées, il peut vous être utile de suivre le démarrage rapide suivant pour savoir comment bénéficier d’une visibilité sur vos données et sur les menaces : [Prendre en main Microsoft Sentinel](get-visibility.md).

Si vous êtes prêt à examiner les incidents créés pour vous, consultez le tutoriel suivant : [Examiner les incidents avec Microsoft Sentinel](investigate-cases.md).
