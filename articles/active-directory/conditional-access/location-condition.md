---
title: Conditions d’emplacement dans l’accès conditionnel Azure Active Directory
description: Utilisez la condition de localisation pour contrôler l’accès en fonction de la localisation de l’utilisateur.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, mewal
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: ef2a7671b1db13e1db9fae8339ae33726a2eb3b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444273"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Utilisation de la condition d’emplacement dans une stratégie d’accès conditionnel 

Comme expliqué dans [l’article de vue d’ensemble](overview.md), une stratégie d’accès conditionnel est à la base constituée d’une instruction de type « if-then » combinant des signaux, qui permet de prendre des décisions et d’appliquer les stratégies de l’organisation. L’un des signaux pouvant être inclus dans le processus de prise de décision est la localisation.

![Signal conditionnel conceptuel + Décision = Application](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Les organisations peuvent utiliser cette localisation pour les tâches courantes, telles que : 

- Exiger l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.
- Bloquer l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques.

La localisation est déterminée par l’IP publique qu’un client fournit à Azure Active Directory ou les coordonnées GPS fournies par l’application Microsoft Authenticator. Par défaut, les stratégies d’accès conditionnel s’appliquent à toutes les adresses IPv4 et IPv6. 

## <a name="named-locations"></a>Emplacements nommés

Les emplacements sont désignés dans le Portail Azure sous **Azure Active Directory** > **Sécurité** > **Accès conditionnel** > **Emplacements nommés**. Ces emplacements réseau nommés peuvent inclure les plages réseau du siège de l’organisation ou du VPN, ou les plages que vous souhaitez bloquer. Les emplacements nommés peuvent être définis par des plages d’adresses IPv4/IPv6 ou par pays. 

![Emplacements nommés dans le Portail Azure](./media/location-condition/new-named-location.png)

### <a name="ip-address-ranges"></a>Plages d’adresse IP

Pour définir un emplacement nommé à l’aide de plages d’adresses IPv4/IPv6, vous devez fournir : 

- Un **nom** pour la localisation
- Une ou plusieurs plages d’adresses IP
- Le cas échéant, **Marquer comme emplacement approuvé**

![Nouvelles localisations IP dans le portail Azure](./media/location-condition/new-trusted-location.png)

Les emplacements nommés définis par les plages d’adresses IPv4/IPv6 sont soumis aux limitations suivantes : 

- Configurer jusqu’à 195 emplacements nommés
- Configurer jusqu’à 2 000 plages d’adresses IP par emplacement nommé
- Les adresses IPv4 et IPv6 sont prises en charge
- Les plages d'adresses IP privées ne peuvent pas être configurées
- Le nombre d’adresses IP contenues dans une plage est limité. Seuls les masques CIDR supérieurs à /8 sont autorisés lors de la définition d’une plage d’adresses IP. 

#### <a name="trusted-locations"></a>Emplacements approuvés

Les administrateurs peuvent désigner des localisations définies par des plages d’adresses IP comme des emplacements nommés approuvés. 

Les connexions provenant d'emplacements nommés approuvés améliorent la précision du calcul des risques d'Azure AD Identity Protection, réduisant ainsi le risque de connexion des utilisateurs lorsqu'ils s'authentifient à partir d'un emplacement marqué comme approuvé. En outre, les emplacements nommés approuvés peuvent être ciblés dans des stratégies d’accès conditionnel. Par exemple, vous pouvez [restreindre l’inscription à l’authentification multifacteur aux emplacements approuvés](howto-conditional-access-policy-registration.md). 

### <a name="countries"></a>Pays

Les organisations peuvent déterminer la localisation du pays grâce à l’adresse IP ou aux coordonnées GPS. 

Pour définir un emplacement nommé par pays, vous devez fournir : 

- Un **nom** pour la localisation
- Choisissez de déterminer la localisation par adresse IP ou par coordonnées GPS
- Ajoutez un ou plusieurs pays
- Le cas échéant, choisissez **Inclure des pays/régions inconnus**

![Pays comme localisation dans le portail Azure](./media/location-condition/new-named-location-country-region.png)

Si vous sélectionnez **Déterminer la localisation par adresse IP (IPv4 uniquement)** , le système collecte l’adresse IP de l’appareil auquel l’utilisateur se connecte. Lorsqu’un utilisateur se connecte, Azure AD résout l’adresse IPv4 de l’utilisateur en un pays ou une région, et le mappage est mis à jour périodiquement. Les organisations peuvent utiliser des emplacements nommés définis par les pays pour bloquer le trafic provenant de pays où elles n’ont pas d’activités. 

> [!NOTE]
> Les connexions à partir d’adresses IPv6 ne peuvent pas être mappées à des pays ou régions et sont considérées comme des zones inconnues. Seules les adresses IPv4 peuvent être mappées à des pays ou des régions.

Si vous sélectionnez **Déterminer la localisation par coordonnées GPS**, l’utilisateur doit avoir installé l’application Microsoft Authenticator sur son appareil mobile. Toutes les heures, le système contacte l’application Microsoft Authenticator de l’utilisateur pour collecter la position GPS de son appareil mobile.

La première fois que l’utilisateur est tenu de partager sa localisation à partir de l’application Microsoft Authenticator, il reçoit une notification dans l’application. L’utilisateur doit ouvrir l’application et accorder des autorisations de localisation. 

Pendant les 24 heures suivantes, si l’utilisateur accède toujours à la ressource et a autorisé l’application à fonctionner en arrière-plan, la localisation de l’appareil est partagée silencieusement une fois par heure. Après 24 heures, l’utilisateur doit ouvrir l’application et approuver la notification. Chaque fois que l’utilisateur partage sa position GPS, l’application effectue une détection de débridage (en utilisant la même logique que le Kit de développement logiciel [SDK] Intune MAM). Si l’appareil est débridé, la localisation n’est pas considérée comme valide et l’accès n’est pas accordé à l’utilisateur. 

Une stratégie d’accès conditionnel avec des emplacements nommés basés sur le GPS en mode rapport seul invite les utilisateurs à partager leur position GPS, même si leur connexion n’est pas bloquée.

> [!IMPORTANT]
> Les utilisateurs peuvent recevoir des invites toutes les heures les informant que Azure AD vérifie leur emplacement dans l’application Authenticator. La préversion ne doit être utilisée que pour protéger des applications très sensibles lorsque ce comportement est acceptable ou lorsque l’accès doit être limité à un pays ou une région spécifique.

#### <a name="include-unknown-countriesregions"></a>Inclure des pays/régions inconnus

Certaines adresses IP ne sont pas mappées à un pays ou une région spécifique, notamment toutes les adresses IPv6. Pour capturer ces localisations IP, cochez la case **Inclure des pays/régions inconnus** lors de la définition d’une localisation géographique. Cette option vous permet de choisir si ces adresses IP doivent être incluses dans l’emplacement nommé. Utilisez ce paramètre lorsque la stratégie utilisant l’emplacement nommé doit s’appliquer à des emplacements inconnus.

### <a name="configure-mfa-trusted-ips"></a>Configurer des adresses IP approuvées MFA

Vous pouvez également configurer des plages d’adresses IP représentant l’intranet local de votre organisation dans les [paramètres du service d’authentification multifacteur](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Cette fonctionnalité vous permet de configurer jusqu’à 50 plages d’adresses IP. Les plages d’adresses IP sont au format CIDR. Pour plus d’informations, consultez [Adresses IP approuvées](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Si vous avez approuvé des adresses IP configurées, elles apparaissent en tant qu'**adresses IP approuvées MFA** dans la liste des emplacements de la condition d'emplacement.

#### <a name="skipping-multi-factor-authentication"></a>Ignorer l’authentification multifacteur

Sur la page des paramètres du service d’authentification multifacteur, vous pouvez identifier les utilisateurs de l’intranet d’entreprise en sélectionnant **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet**. Ce paramètre indique que la revendication de réseau d’entreprise interne, qui est émise par AD FS, doit être approuvée et utilisée pour identifier l’utilisateur comme étant sur le réseau d’entreprise. Pour plus d’informations, consultez [Activer la fonctionnalité Adresses IP approuvées à l’aide de l’accès conditionnel](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Après avoir activé cette option, ainsi que l'emplacement nommé, les **Adresses IP approuvées MFA** s'appliqueront à toutes les stratégies dans lesquelles cette option est sélectionnée.

Pour les applications mobiles et de bureau avec des durées de session active longues, l’accès conditionnel est réévalué régulièrement. La valeur par défaut est une fois une heure. Lorsque la revendication de réseau d’entreprise interne n’est émise qu’au moment de l’authentification initiale, Azure AD peut ne pas contenir de liste de plages d’adresses IP approuvées. Dans ce cas, il est plus difficile de déterminer si l’utilisateur est toujours sur le réseau d’entreprise :

1. Vérifiez si l’adresse IP de l’utilisateur se trouve dans l’une des plages d’adresses IP approuvées.
1. Vérifiez si les trois premiers octets de l’adresse IP de l’utilisateur correspondent aux trois premiers octets de l’adresse IP de l’authentification initiale. L’adresse IP est comparée à l’authentification initiale lorsque la revendication de réseau d’entreprise interne a été émise à l’origine et que l’emplacement de l’utilisateur a été validé.

Si les deux étapes échouent, un utilisateur est considéré comme n’étant plus sur une adresse IP approuvée.

## <a name="location-condition-in-policy"></a>Condition d’emplacement dans une stratégie

Lorsque vous configurez la condition de localisation, vous pouvez faire la distinction entre :

- N’importe quel emplacement
- Tous les emplacements approuvés
- Des emplacements sélectionnés

### <a name="any-location"></a>N’importe quel emplacement

Par défaut, la sélection de **N’importe quel emplacement** entraîne l’application d’une stratégie à toutes les adresses IP, à savoir n’importe quelle adresse sur Internet. Ce paramètre ne se limite pas aux adresses IP que vous avez configurées en tant qu’emplacement nommé. Lorsque vous sélectionnez **N’importe quel emplacement**, vous pouvez toujours exclure des emplacements spécifiques d’une stratégie. Par exemple, vous pouvez appliquer une stratégie à tous les emplacements, sauf des emplacements approuvés, afin de définir l’étendue à tous les emplacements, à l’exception du réseau d’entreprise.

### <a name="all-trusted-locations"></a>Tous les emplacements approuvés

Cette option s’applique à :

- Tous les emplacements qui ont été marqués comme emplacement approuvé
- **Adresses IP approuvées MFA** (si configurées)

### <a name="selected-locations"></a>Des emplacements sélectionnés

Avec cette option, vous pouvez sélectionner un ou plusieurs emplacements nommés. Pour une stratégie avec ce paramètre à appliquer, un utilisateur doit se connecter à partir de l’un des emplacements sélectionnés. Lorsque vous **sélectionnez** le réseau nommé, le contrôle de sélection qui affiche la liste des réseaux nommés s’ouvre. La liste indique également si l’emplacement réseau a été marqué comme approuvé. L’emplacement nommé appelé **Adresses IP approuvées MFA** est utilisé pour inclure les paramètres IP pouvant être configurés dans la page des paramètres du service d’authentification multifacteur.

## <a name="ipv6-traffic"></a>Trafic IPv6

Par défaut, les stratégies d’accès conditionnel s’appliquent à tout le trafic IPv6. Vous pouvez exclure des plages d’adresses IPv6 spécifiques d’une stratégie d’accès conditionnel si vous ne souhaitez pas que les stratégies soient appliquées pour des plages IPv6 spécifiques. Par exemple, si vous ne souhaitez pas appliquer une stratégie pour les utilisations sur votre réseau d’entreprise et que votre réseau d’entreprise est hébergé sur des plages IPv6 publiques.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identification du trafic IPv6 dans les rapports d’activité de connexion Azure AD

Vous pouvez découvrir le trafic IPv6 dans votre locataire en accédant aux [rapports d’activité de connexion Azure AD](../reports-monitoring/concept-sign-ins.md). Une fois le rapport d’activité ouvert, ajoutez la colonne « Adresse IP ». Cette colonne vous permettra d’identifier le trafic IPv6.

Vous pouvez également trouver l’adresse IP du client en cliquant sur une ligne du rapport, puis en accédant à l’onglet « Emplacement » dans les détails de l’activité de connexion. 

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Quand mon locataire aura-t-il un trafic IPv6 ?

Actuellement, Azure Active Directory (Azure AD) ne prend pas en charge les connexions réseau directes qui utilisent IPv6. Toutefois, dans certains cas, le trafic d’authentification est transféré par proxy via un autre service. Dans ces cas, l’adresse IPv6 est utilisée lors de l’évaluation de la stratégie.

La majeure partie du trafic IPv6 qui est transférée par proxy à Azure AD provient de Microsoft Exchange Online. Lorsqu’elles sont disponibles, Exchange privilégie les connexions IPv6. **Par conséquent, si vous avez des stratégies d’accès conditionnel pour Exchange, qui ont été configurées pour des plages IPv4 spécifiques, vous devez vous assurer que vous avez également ajouté les plages IPv6 de votre organisation.** Si vous n’incluez pas de plages IPv6, vous risquez de provoquer un comportement inattendu pour les deux cas suivants :

- Lorsqu’un client de messagerie est utilisé pour se connecter à Exchange Online avec l’authentification héritée, Azure AD peut recevoir une adresse IPv6. La demande d’authentification initiale est envoyée à Exchange et est ensuite transmise par proxy à Azure AD.
- Quand Outlook Web Access (OWA) est utilisé dans le navigateur, il vérifie périodiquement que toutes les stratégies d’accès conditionnel sont toujours respectées. Cette vérification permet d’intercepter les cas où un utilisateur est passé d’une adresse IP autorisée à un nouvel emplacement, comme un café en ville. Dans ce cas, si une adresse IPv6 est utilisée et si l’adresse IPv6 n’est pas dans une plage configurée, il se peut que la session de l’utilisateur soit interrompue et redirigée vers Azure AD pour une nouvelle authentification. 

En outre, si vous utilisez des réseaux virtuels Azure, vous recevrez du trafic provenant d’une adresse IPv6. Si le trafic du réseau virtuel est bloqué par une stratégie d’accès conditionnel, vérifiez votre journal des connexions Azure AD. Une fois que vous avez identifié le trafic, vous pouvez obtenir l’adresse IPv6 utilisée et l’exclure de votre stratégie. 

> [!NOTE]
> Si vous souhaitez spécifier une plage CIDR IP pour une seule adresse, appliquez le masque de bits /128. Si vous voyez l'adresse IPv6 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a et souhaitez exclure cette adresse unique en tant que plage, utilisez 2607:fb90:b27a:6f69:f8d5:dea0:fb39:74a/128.

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="when-is-a-location-evaluated"></a>Quand un emplacement est-il évalué ?

Les stratégies d’accès conditionnel sont évaluées lorsque :

- Un utilisateur se connecte initialement à une application web, mobile ou de bureau.
- Une application mobile ou de bureau qui utilise une authentification moderne utilise un jeton d’actualisation pour acquérir un nouveau jeton d’accès. Par défaut, la vérification est effectuée une fois par heure.

Ce qui signifie que pour des applications mobiles et de bureau utilisant l’authentification moderne, un changement d’emplacement sera détecté dans l’heure du changement de l’emplacement réseau. Pour les applications mobiles et de bureau n’utilisant pas l’authentification moderne, la stratégie est appliquée sur chaque requête de jeton. La fréquence de la requête peut varier selon l’application. Il en va de même pour les applications web. La stratégie est appliquée à la connexion initiale et est valide pour toute la durée de la session dans l’application web. En raison des différences de durées de session dans les applications, le temps d’évaluation de la stratégie varie aussi. À chaque fois que l'application demande un nouveau jeton de connexion, la stratégie est appliquée.

Par défaut, Azure AD émet un jeton toutes les heures. Après une sortie du réseau d’entreprise, la stratégie est appliquée dans l’heure qui suit pour les applications utilisant l’authentification moderne.

### <a name="user-ip-address"></a>Adresse IP utilisateur

L’adresse IP utilisée dans l’évaluation de la stratégie correspond à l’IP publique de l’utilisateur. Pour les appareils sur un réseau privé, il ne s’agit pas de l’adresse IP cliente de l’appareil de l’utilisateur sur l’intranet ; il s’agit de l’adresse utilisée par le réseau pour se connecter à l’Internet public.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Chargement et téléchargement en bloc d’emplacements nommés

Lorsque vous créez ou mettez à jour des emplacements nommés, pour effectuer des mises à jour en bloc, vous pouvez charger ou télécharger un fichier CSV contenant les plages d’adresses IP. Un chargement permet de remplacer les plages d’adresses IP de la liste par celles du fichier. Chaque ligne du fichier contient une plage d’adresses IP au format CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies cloud et réseaux privés virtuels

Lorsque vous utilisez un proxy hébergé dans le cloud ou une solution VPN, l’adresse IP Azure AD utilisée pendant l’évaluation d’une stratégie correspond à l’adresse IP du proxy. L’en-tête X-Forwarded-For (XFF) qui contient l’adresse IP publique de l’utilisateur n’est pas utilisé car il n’existe aucune validation provenant d’une source approuvée, il pourrait donc permettre de falsifier une adresse IP.

Lorsqu’un proxy cloud est disponible, une stratégie utilisée pour exiger un appareil joint Azure AD hybride peut être utilisée, ou la revendication de réseau d’entreprise interne à partir d’AD FS.

### <a name="api-support-and-powershell"></a>Prise en charge des API et PowerShell

Une préversion de l’API Graph pour les emplacements nommés est disponible. Pour plus d’informations, consultez [API namedLocation](/graph/api/resources/namedlocation).

## <a name="next-steps"></a>Étapes suivantes

- Pour configurer une stratégie d’accès conditionnel à l’aide de la localisation, consultez l’article [Accès conditionnel : Bloquer l’accès par localisation](howto-conditional-access-policy-location.md).
