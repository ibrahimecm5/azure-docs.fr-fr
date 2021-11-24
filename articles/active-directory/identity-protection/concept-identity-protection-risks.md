---
title: Quel est le risque ? Azure AD Identity Protection
description: Explication du risque dans Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c783ec82455e1460e3508357c29167c40ca4e9db
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307969"
---
# <a name="what-is-risk"></a>Quel est le risque ?

Les détections de risques dans Azure AD Identity Protection incluent toutes les actions suspectes identifiées liées aux comptes d’utilisateur dans l’annuaire. Les détections de risques (utilisateur et de connexion) contribuent au score de risque de l’utilisateur global qui se trouve dans le rapport utilisateurs à risque.

Identity Protection offre aux organisations un accès à des ressources puissantes pour voir et traiter rapidement ces actions suspectes. 

![Vue d’ensemble de la sécurité montrant les utilisateurs et les connexions à risque](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection génère des détections de risques uniquement quand les informations d’identification correctes sont utilisées. Si des informations d’identification incorrectes sont utilisées sur une connexion, elles ne représentent pas un risque de compromission des informations d’identification.

## <a name="risk-types-and-detection"></a>Types de risques et détection

Le risque peut être détecté au niveau de l’**utilisateur** ou de la **Connexion** et il existe deux types de détection ou de calcul : **en temps réel** et **hors connexion**.

Les détections en temps réel peuvent ne pas apparaître dans les rapports pendant cinq à dix minutes. Les détections hors connexion peuvent ne pas apparaître dans les rapports pendant 48 heures.

> [!NOTE] 
> Notre système peut détecter que l’événement à risque qui a contribué à la note de risque de l’utilisateur est un faux positif ou que le risque de l’utilisateur a été corrigé par l’application d’une stratégie, par exemple en effectuant une demande d’authentification multifacteur (MFA) ou une modification sécurisée du mot de passe. Par conséquent, notre système ignorera l’état de risque et le détail de risque « L’intelligence artificielle a confirmé que la connexion est sécurisée » apparaîtra et ne contribuera plus au risque de l’utilisateur. 

### <a name="user-linked-detections"></a>Détections liées aux utilisateurs

Une activité à risque peut être détectée pour un utilisateur qui n’est pas lié à une connexion malveillante spécifique, mais à l’utilisateur lui-même. Ces détections de risques sont calculées hors connexion à l’aide de sources de renseignement sur les menaces internes et externes de Microsoft, comme les chercheurs en sécurité, les professionnels de la justice, les équipes de sécurité de Microsoft et autres sources approuvées.

Ces risques sont calculés hors connexion à l’aide de sources d’informations sur les menaces internes et externes de Microsoft, dont des chercheurs en sécurité, des professionnels de la justice, des équipes de sécurité de Microsoft et d’autres sources approuvées.

| Détection d’événements à risque | Description |
| --- | --- |
| Informations d’identification divulguées | Ce type de détection d’événement à risque indique que les informations d’identification valides de l’utilisateur ont fuité. Souvent, lorsque les cybercriminels compromettent les mots de passe valides d’utilisateurs légitimes, ils le font dans le but de les rendre publics. Ce partage se fait généralement en publiant publiquement sur le « dark web », via des sites de pastebin, ou en échangeant et vendant des informations d’identification sur le marché noir. Lorsque le service d’informations de connexion divulguées de Microsoft acquiert des informations d’identification utilisateur à partir du dark Web, de collage de sites ou autres sources, ces informations sont comparées aux informations d’identification valides actuelles des utilisateurs Azure AD pour rechercher des correspondances valides. Pour plus d’informations sur informations de connexion divulguées, consultez [Questions courantes](#common-questions). |
| Azure AD Threat Intelligence | Ce type de détection d’événement à risque indique une activité utilisateur inhabituelle pour l’utilisateur donné ou qui est cohérente avec des modèles d’attaque connus selon les sources internes et externes de Microsoft Threat Intelligence. |

### <a name="sign-in-risk"></a>Risque à la connexion

Un risque de connexion reflète la probabilité qu’une requête d’authentification donnée soit rejetée par le propriétaire de l'identité. 

Ces risques peuvent être calculés en temps réel ou hors connexion à l’aide de sources d’informations sur les menaces internes et externes de Microsoft, dont des chercheurs en sécurité, des professionnels de la justice, des équipes de sécurité de Microsoft et d’autres sources approuvées.

| Détection d’événements à risque | Type de détection | Description |
| --- | --- | --- |
| Adresse IP anonyme | Temps réel | Ce type de détection des risque détecte des connexions à partir d’adresses IP anonymes (par exemple, navigateur Tor VPN anonyme). Ces adresses IP sont généralement utilisées par des acteurs souhaitant masquer leur télémétrie de connexion (adresse IP, emplacement, appareil, etc.) dans un but potentiellement malveillant. |
| Voyage inhabituel | Hors connexion | Ce type de détection d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. Entre autres facteurs, cet algorithme Machine Learning prend en compte le délai écoulé entre les deux connexions et le temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification. <br><br> L’algorithme ignore les « faux positifs » évidents contribuant aux conditions de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale la plus proche de 14 jours ou de 10 connexions lui servant à assimiler le comportement de connexion des nouveaux utilisateurs. |
| Jeton anormal | Hors connexion | Cette détection indique qu’il existe des caractéristiques anormales dans le jeton, telles qu’une durée de vie de jeton inhabituelle ou un jeton émis à partir d’un emplacement inconnu. Cette détection couvre les jetons de session et les jetons d’actualisation. |
| Anomalie de l’émetteur du jeton | Hors connexion |Cette détection des risques indique que l’émetteur de jeton SAML pour le jeton SAML associé est potentiellement compromis. Les revendications incluses dans le jeton sont inhabituelles ou correspondent aux modèles d’attaquants connus. |
| Adresse IP liée à un programme malveillant | Hors connexion | Ce type de détection d’événement à risque indique les connexions depuis des adresses IP infectées par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur bot, Cette détection est effectuée en mettant en corrélation des adresses IP d’appareils d’utilisateurs avec des adresses ayant été en contact avec un serveur robot actif. <br><br> **[Cette détection est déconseillée](../fundamentals/whats-new.md#planned-deprecation---malware-linked-ip-address-detection-in-identity-protection)** . Azure Identity protection ne génère plus de nouvelles détections d’« Adresse IP liée à un programme malveillant ». Les clients qui ont actuellement des détections d’« Adresse IP liée à un programme malveillant » dans leur locataire pourront toujours les afficher, les corriger ou les ignorer jusqu’à ce que la durée de 90 jours de rétention des détections soit atteinte.|
| Navigateur suspect | Hors connexion | La détection de navigateur suspect indique un comportement anormal basé sur des activités de connexion suspectes sur plusieurs locataires de différents pays dans le même navigateur. |
| Propriétés de connexion inhabituelles | Temps réel | Ce type de détection d’événement à risque prend en compte l’historique des connexions antérieures (IP, latitude / longitude et NSA) pour rechercher des connexions anormales. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». La détection d’événement à risque est déclenchée quand la connexion a lieu depuis un emplacement qui ne figure pas déjà dans la liste des emplacements connus. Les utilisateurs nouvellement créés seront en « mode d’apprentissage » pendant une période de temps durant laquelle les détections d’événements à risque des propriétés de connexion inconnues seront désactivées pendant que nos algorithmes apprennent le comportement de l’utilisateur. La durée du mode d’apprentissage est dynamique et varie selon le temps qu’il faut à l’algorithme pour collecter suffisamment d’informations sur les modèles de connexion de l’utilisateur. La durée minimale est de 5 jours. Un utilisateur peut revenir en mode d’apprentissage après une longue période d’inactivité. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. <br><br> Nous exécutons également cette détection pour l’authentification de base (ou les protocoles existants). Étant donné que ces protocoles ne proposent pas les propriétés modernes, telles que l’ID client, les données de télémétrie pour réduire le nombre de faux positifs sont limitées. Nous recommandons à nos clients de passer à l’authentification moderne. <br><br> Des propriétés de connexion inhabituelles peuvent être détectées sur des connexions interactives et non interactives. Lorsque cette détection est détectée sur des connexions non interactives, elle mérite des contrôles accrus en raison du risque d’attaques par relecture de jetons.  |
| L’administrateur a confirmé que cet utilisateur est compromis | Hors connexion | Cette détection indique qu’un administrateur a sélectionné « Confirmer que l’utilisateur est compromis » dans l’interface utilisateur Utilisateurs à risque ou à l’aide de l’API riskyUsers. Pour voir quel administrateur a confirmé que cet utilisateur est compromis, consultez l’historique des risques de l’utilisateur (par le biais de l’interface utilisateur ou de l’API). |
| Adresse IP malveillante | Hors connexion | Cette détection indique une connexion à partir d’une adresse IP malveillante. Une adresse IP est considérée comme malveillante quand elle présente un taux d’échecs élevé en raison d’informations d’identification non valides qu’elle envoie ou d’autres sources relatives à la réputation des adresses IP. |
| Règles suspectes de manipulation de boîte de réception | Hors connexion | Cette détection est découverte par les [Applications Microsoft Defender pour le cloud](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Cette détection dresse le profil de votre environnement et déclenche des alertes lorsque des règles suspectes qui suppriment ou déplacent des messages ou des dossiers sont définies dans la boîte de réception d'un utilisateur. Cela peut indiquer que le compte de l’utilisateur est compromis, que les messages sont intentionnellement masqués et que la boîte aux lettres est utilisée pour distribuer le courrier indésirable ou les logiciels malveillants dans votre organisation. |
| Pulvérisation de mots de passe | Hors connexion | Une attaque par pulvérisation de mots de passe est l’endroit où plusieurs noms d’utilisateur sont attaqués à l’aide de mots de passe communs dans une méthode de force brute unifiée pour obtenir un accès non autorisé. Cette détection des risques est déclenchée lorsqu’une attaque par pulvérisation de mots de passe a été effectuée. |
| Voyage impossible | Hors connexion | Cette détection est découverte par les [Applications Microsoft Defender pour le cloud](/cloud-app-security/anomaly-detection-policy#impossible-travel). Cette détection identifie deux activités de l’utilisateur (dans une seule ou plusieurs sessions) provenant d’emplacements éloignés sur le plan géographique au cours d’une période plus courte que la durée nécessaire à l’utilisateur pour aller du premier emplacement au second, indiquant qu’un autre utilisateur utilise les mêmes informations d’identification. |
| Nouveau pays | Hors connexion | Cette détection est découverte par les [Applications Microsoft Defender pour le cloud](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). Cette détection prend en compte les emplacements d’activité précédents pour déterminer les emplacements nouveaux et peu fréquents. Le moteur de détection d’anomalies stocke des informations sur les emplacements précédents utilisés par les utilisateurs de l’organisation. |
| Activité depuis une adresse IP anonyme | Hors connexion | Cette détection est découverte par les [Applications Microsoft Defender pour le cloud](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). Cette détection identifie que les utilisateurs étaient actifs depuis une adresse IP qui a été identifiée comme une adresse IP de proxy anonyme. |
| Transfert de boîte de réception suspect | Hors connexion | Cette détection est découverte par les [Applications Microsoft Defender pour le cloud](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). Cette détection recherche les règles de transfert des e-mails suspects, par exemple, si un utilisateur a créé une règle de boîte de réception assurant le transfert d’une copie de tous les e-mails à une adresse externe. |
| Azure AD Threat Intelligence | Hors connexion | Ce type de détection d'événement à risque indique une activité de connexion inhabituelle pour l'utilisateur donné ou qui est cohérente avec des modèles d'attaque connus selon les sources internes et externes de Microsoft Threat Intelligence. |

### <a name="other-risk-detections"></a>Autres détections de risques

| Détection d’événements à risque | Type de détection | Description |
| --- | --- | --- |
| Risque supplémentaire détecté | Temps réel ou hors connexion | Cette détection indique que l’une des détections Premium ci-dessus a eu lieu. Étant donné que les détections Premium ne sont visibles que pour les clients Azure AD Premium P2, on les appelle « risque supplémentaire détecté » pour les clients dépourvus de licences Azure AD Premium P2. |

## <a name="common-questions"></a>Questions courantes

### <a name="risk-levels"></a>Niveaux de risque

La protection d’identité catégorise les risques en trois niveaux : faible, moyen, sévère. Quand vous configurez des [stratégies Identity Protection personnalisées](./concept-identity-protection-policies.md#custom-conditional-access-policy), vous pouvez également les configurer pour qu’elles se déclenchent au niveau **Pas de risque**. Le niveau Aucun risque signifie qu’il n’existe aucune indication active que l’identité de l’utilisateur a été compromise.

Bien que Microsoft ne communique pas en détail sur la manière dont le risque est calculé, nous pouvons affirmer que chaque niveau souligne avec un peu plus de certitude que l’utilisateur ou la connexion est compromis(e). Par exemple, une instance de propriétés de connexion non connues pour un utilisateur pourrait être moins dangereuse que la divulgation d’informations d’identification pour un autre utilisateur.

### <a name="password-hash-synchronization"></a>Synchronisation de hachage du mot de passe

Les détections de risque comme les informations d’identification divulguées nécessitent la présence de hachages de mot de passe. Pour plus d’informations sur la synchronisation de hachage du mot de passe, consultez l’article [Implémenter la synchronisation de hachage de mot de passe avec la synchronisation Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

### <a name="why-are-there-risk-detections-generated-for-disabled-user-accounts"></a>Pourquoi ces détections de risques sont-elles générées pour les comptes d’utilisateurs désactivés ?
          
Vous pouvez réactiver des comptes d’utilisateurs désactivés. Si les informations d’identification d’un compte désactivé sont compromises et que le compte est réactivé, des acteurs malveillants pourraient utiliser ces informations d’identification pour y accéder. C’est pourquoi, Azure Identity Protection génère des détections de risques pour des activités suspectes sur les comptes d’utilisateurs désactivés afin d’alerter les clients en cas de compromission de compte potentielle. Si un compte n’est plus utilisé et ne sera pas réactivé, les clients doivent envisager de le supprimer afin d’éviter toute compromission. Aucune détection de risque n’est générée pour les comptes supprimés.

### <a name="leaked-credentials"></a>Informations d’identification divulguées

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Où Microsoft trouve-t-il les informations d’identification divulguées ?

Microsoft découvre des fuites d'informations d'identification à divers endroits, notamment :

- Les sites de téléchargement publics tels que pastebin.com et paste.ca où les malfaiteurs publient généralement ce genre de contenu. Ce genre de site est la première étape pour les malfaiteurs en quête d'informations d’identification volées.
- Forces de l'ordre.
- D'autres groupes chez Microsoft qui s’occupent des recherches sur le dark web.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Pourquoi ne vois-je aucune information d'identification divulguée ?

Les informations d'identification divulguées sont traitées chaque fois que Microsoft trouve une nouvelle occurrence de données accessibles au public. En raison de leur nature sensible, les informations d'identification divulguées sont supprimées peu après le traitement. Seules les nouvelles informations d’identification divulguées détectées après l’activation de la synchronisation de hachage de mot de passe (PHS) seront traitées pour votre locataire. La vérification par rapport aux paires d’informations d’identification précédemment détectées n’est pas effectuée. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Je n’ai pas vu d’événements à risque concernant les informations d’identification divulguées depuis un moment.

Si vous n’avez pas vu d’événements à risque concernant les informations d’identification divulguées, cela peut être dû à plusieurs raisons :

- Vous n'avez aucun PHS activé pour votre locataire.
- Microsoft n'a trouvé aucune paire d’informations d’identification divulguées et correspondant à vos utilisateurs.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>À quelle fréquence Microsoft traite-t-il les nouvelles informations d’identification ?

Les informations d’identification sont traitées immédiatement après avoir été détectées, normalement en plusieurs lots par jour.

### <a name="locations"></a>Emplacements

L'emplacement dans les détections de risques est déterminé par la recherche d'adresse IP.

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies disponibles pour atténuer les risques](concept-identity-protection-policies.md)
- [Vue d’ensemble de la sécurité](concept-identity-protection-security-overview.md)
