---
title: Évaluation de l’accès continu dans Azure AD
description: Réponse plus rapide aux modifications de l’état utilisateur grâce à l’évaluation de l’accès continu dans Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu
ms.custom: has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de2ed185b3a4421a06ac9b3c88df68821b61adf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307988"
---
# <a name="continuous-access-evaluation"></a>Évaluation de l’accès continu

L’expiration et l’actualisation des jetons sont un mécanisme standard du secteur. Lorsqu’une application cliente telle qu’Outlook se connecte à un service comme Exchange Online, les demandes d’API sont autorisées à l’aide de jetons d’accès OAuth 2.0. Par défaut, ces jetons d’accès sont valides pendant une heure. Quand ils expirent, le client est redirigé vers Azure AD pour les actualiser. Cette période d’actualisation offre la possibilité de réévaluer les stratégies d’accès utilisateur. Par exemple, il est possible de choisir de ne pas actualiser le jeton en raison d’une stratégie d’accès conditionnel ou parce que l’utilisateur a été désactivé dans l’annuaire. 

Les clients ont exprimé les préoccupations du décalage entre le changement des conditions pour un utilisateur et le moment où les modifications de stratégie sont appliquées. Azure AD a fait l’expérience de l’approche radicale des durées de vie de jeton réduites, mais il s’est avéré qu’elles peuvent nuire à l’expérience utilisateur et à la fiabilité sans éliminer les risques.

Une réponse en temps utile aux violations de stratégie ou autres problèmes de sécurité nécessite vraiment une « conversation » entre l’émetteur du jeton (Azure AD) et la partie qui se fie à lui (application éclairée). Cette conversation bidirectionnelle nous offre deux capacités importantes. La partie de confiance peut voir quand les propriétés changent, comme l’emplacement réseau, et comment indiquer à l’émetteur du jeton. Et l’émetteur du jeton peut demander à la partie de confiance d’arrêter de respecter les jetons pour un utilisateur donné en raison d’une compromission ou d’une désactivation de compte ou d’autres soucis. Le mécanisme de cette conversation est l’évaluation continue de l’accès. L’objectif est que la réponse soit en quasi-temps réel mais une latence pouvant atteindre 15 minutes peut être observée en raison du temps de propagation de l’événement.

L’implémentation initiale de l’évaluation continue de l’accès se concentre sur Exchange, Teams et SharePoint Online.

Pour préparer vos applications à utiliser l’évaluation continue de l’accès, consultez [Guide pratique pour utiliser les API d’évaluation continue de l’accès dans vos applications](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Principaux avantages

- Démission d’utilisateur ou modification/réinitialisation du mot de passe : La révocation de session utilisateur sera appliquée en quasi-temps réel.
- Changement d’emplacement réseau : les stratégies d’emplacement d’accès conditionnel seront appliquées en quasi-temps réel.
- L’exportation de jetons vers une machine en dehors d’un réseau approuvé peut être évitée avec des stratégies d’emplacement d’accès conditionnel.

## <a name="scenarios"></a>Scénarios 

L’évaluation continue de l’accès se décline en deux scénarios : l’évaluation des événements critiques et l’évaluation de la stratégie d’accès conditionnel.

### <a name="critical-event-evaluation"></a>Évaluation des événements critiques

L’évaluation continue de l’accès est implémentée en permettant à des services tels qu’Exchange Online, SharePoint Online et Microsoft Teams, de s’abonner à des événements critiques dans Azure AD. Ces événements peuvent ensuite être évalués et appliqués en temps quasi réel. L’évaluation des événements critiques ne reposant pas sur des stratégies d’accès conditionnel, elle est disponible dans n’importe quel locataire. Les événements suivants sont actuellement évalués :

- Le compte d’utilisateur est supprimé ou désactivé
- Le mot de passe d’un utilisateur a été modifié ou réinitialisé
- L’authentification multifacteur est activée pour l’utilisateur
- Un administrateur révoque explicitement tous les jetons d’actualisation d’un utilisateur
- Risque utilisateur élevé détecté par Azure AD Identity Protection

Ce processus permet l’émergence du scénario dans lequel des utilisateurs perdent l’accès à des fichiers, à des e-mails, à des éléments du calendrier ou à des tâches SharePoint Online de l’organisation, ainsi qu’à Teams à partir d’applications clientes Microsoft 365 quelques minutes après la survenance d’un événement critique. 

> [!NOTE] 
> Teams et SharePoint Online ne prennent pas encore en charge les événements à risque.

### <a name="conditional-access-policy-evaluation-preview"></a>Évaluation de la stratégie d’accès conditionnel (préversion)

Exchange Online, SharePoint Online, Teams et MS Graph peuvent synchroniser des stratégies d’accès conditionnel clés afin qu’elles puissent être évaluées dans le service proprement dit.

Ce processus permet l’émergence du scénario dans lequel des utilisateurs perdent l’accès à des fichiers, à des e-mails, à des éléments du calendrier ou à des tâches de l’organisation à partir d’applications clientes Microsoft 365 ou de SharePoint Online immédiatement après des changements d’emplacement réseau.

> [!NOTE]
> Les combinaisons de fournisseurs d’applications et de ressources ne sont pas toutes prises en charge. Consultez le tableau ci-dessous. Office fait référence à Word, Excel et PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **Exchange Online** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |

| | Applications web Office | Applications Win32 Office | Office pour iOS | Office pour Android | Office pour Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Non pris en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **Exchange Online** | Non pris en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |

| | Web OneDrive | OneDrive Win32 | IOS OneDrive | Android OneDrive | Mac OneDrive |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Prise en charge | Non pris en charge | Prise en charge | Prise en charge | Non pris en charge |

| | Teams web | Teams Win32 | Teams iOS | Teams Android | Teams Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Teams Service** | Pris en charge | Prise en charge | Prise en charge | Prise en charge | Pris en charge |
| **SharePoint Online** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **Exchange Online** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |

## <a name="client-capabilities"></a>Fonctionnalités du client

### <a name="client-side-claim-challenge"></a>Contestation de revendication côté client

Avant l’évaluation de l’accès continu, les clients essayaient de relire le jeton d’accès à partir de leur cache tant qu’il n’avait pas expiré. Avec l’EAC, nous introduisons un nouveau cas où un fournisseur de ressources peut rejeter un jeton même s’il n’a pas expiré. Pour informer les clients qu’ils doivent contourner leur cache, même si les jetons mis en cache n’ont pas expiré, nous introduisons un mécanisme appelé **contestation de revendication** destiné à indiquer que le jeton a été rejeté et qu’un nouveau jeton d’accès doit être émis par Azure AD. L’EAC requiert une mise à jour du client pour comprendre la constestation de revendication. La dernière version des applications suivantes prend en charge la contestation de revendication :

| | Web | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **Équipes** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **Office** | Non pris en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |
| **OneDrive** | Prise en charge | Prise en charge | Prise en charge | Prise en charge | Prise en charge |

### <a name="token-lifetime"></a>Durée de vie des jetons

Comme le risque et la politique sont évalués en temps réel, les clients qui négocient des sessions conscientes de l'évaluation continue de l'accès ne dépendent plus des politiques statiques de durée de vie des jetons d'accès. Cette modification signifie que la stratégie de durée de vie des jetons configurable n’est pas honorée pour les clients négociant des sessions compatibles avec la reconnaissance de l’EAC.

La durée de vie du jeton est allongée jusqu’à 28 heures dans les sessions d’évaluation continue de l’accès. La révocation est motivée par des événements critiques et l’évaluation de la stratégie. Il ne s’agit pas d’une période arbitraire. Cette modification renforce la stabilité des applications sans compromettre la posture de sécurité. 

Si vous n’utilisez pas de clients basés sur la compatibilité EAC, la durée de vie de votre jeton d’accès par défaut reste 1 heure. La valeur par défaut change uniquement si vous avez configuré votre durée de vie de jeton d’accès avec la fonctionnalité de préversion de la [durée de vie du jeton configurable (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

## <a name="example-flow-diagrams"></a>Exemples de diagrammes de flux

### <a name="user-revocation-event-flow"></a>Flux d’événements de révocation des utilisateurs

![Flux d’événements de révocation des utilisateurs](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client compatible avec l’évaluation continue de l’accès présente à Azure AD des informations d’identification ou un jeton d’actualisation demandant un jeton d’accès pour une certaine ressource.
1. Un jeton d’accès est retourné au client avec d’autres artefacts.
1. Un administrateur [révoque explicitement tous les jetons d’actualisation d’un utilisateur](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Un événement de révocation sera envoyé au fournisseur de ressources à partir d’Azure AD.
1. Un jeton d’accès est présenté au fournisseur de ressources. Le fournisseur de ressources évalue la validité du jeton et vérifie s’il existe un événement de révocation pour l’utilisateur. Le fournisseur de ressources utilise ces informations pour décider d’accorder ou non l’accès à la ressource.
1. Dans ce cas, le fournisseur de ressources refuse l’accès et renvoie une contestation de revendication 401+ au client.
1. Le client compatible avec l’EAC comprend la contestation de revendication 401+. Il contourne les caches et revient à l’étape 1, en renvoyant à Azure AD son jeton d’actualisation avec la contestation de revendication. Azure AD réévalue ensuite toutes les conditions et invite l’utilisateur à se réauthentifier dans ce cas.

### <a name="user-condition-change-flow-preview"></a>Flux de changement de condition utilisateur (préversion)

Dans l’exemple suivant, un administrateur d’accès conditionnel a configuré une stratégie d’accès conditionnel basée sur l’emplacement afin de n’autoriser l’accès qu’à partir de plages d’adresses IP spécifiques :

![Flux d’événements de condition utilisateur](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Un client compatible avec l’évaluation continue de l’accès présente à Azure AD des informations d’identification ou un jeton d’actualisation demandant un jeton d’accès pour une certaine ressource.
1. Azure AD évalue toutes les stratégies d’accès conditionnel pour déterminer si l’utilisateur et le client remplissent les conditions.
1. Un jeton d’accès est retourné au client avec d’autres artefacts.
1. L’utilisateur quitte une plage d’adresses IP autorisées
1. Le client présente un jeton d’accès au fournisseur de ressources en dehors d’une plage d’adresses IP autorisées.
1. Le fournisseur de ressources évalue la validité du jeton et vérifie la synchronisation de la stratégie d’emplacement à partir d’Azure AD.
1. Dans ce cas, le fournisseur de ressources refuse l’accès et renvoie une contestation de revendication 401+ au client. Le client est contesté, car il ne provient pas d’une plage d’adresses IP autorisée.
1. Le client compatible avec l’EAC comprend la contestation de revendication 401+. Il contourne les caches et revient à l’étape 1, en renvoyant à Azure AD son jeton d’actualisation avec la contestation de revendication. Dans ce cas, Azure AD réévalue toutes les conditions et refuse l’accès.

## <a name="enable-or-disable-cae-preview"></a>Activer ou désactiver l’évaluation continue de l’accès (préversion)

Le paramètre d’évaluation continue de l’accès a été déplacé sous le panneau Accès conditionnel. Les nouveaux clients de l’évaluation continue de l’accès pourront accéder à l’évaluation continue de l’accès et l’activer directement lors de la création de stratégies d’accès conditionnel. Toutefois, certains clients existants devront passer par la migration avant de pouvoir commencer à accéder à l’évaluation continue de l’accès par le biais de l’accès conditionnel.

#### <a name="migration"></a>Migration

Les clients qui ont configuré les paramètres d’évaluation continue de l’accès sous Sécurité avant d’avoir à migrer ces paramètres vers une nouvelle stratégie d’accès conditionnel. Utilisez les étapes suivantes pour migrer vos paramètres d’évaluation continue de l’accès vers une stratégie d’accès conditionnel.

:::image type="content" source="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png" alt-text="Vue du portail présentant l’option de migration de l’évaluation continue de l’accès vers une stratégie d’accès conditionnel." lightbox="media/concept-continuous-access-evaluation/migrate-continuous-access-evaluation.png":::

1. Connectez-vous au **portail Azure** en tant qu’administrateur de l’accès conditionnel, administrateur de la sécurité ou administrateur général. 
1.  Accédez à **Azure Active Directory** > **Sécurité** > **Évaluation continue de l’accès (préversion)** . 
1.  L’option vous permettant de **Migrer** votre stratégie sera alors visible. Cette action sera la seule à laquelle vous aurez accès à ce stade.
1. Accédez à **Accès conditionnel** ; vous trouverez une nouvelle stratégie nommée **Stratégie d’accès conditionnel créée à partir des paramètres d’évaluation continue de l’accès** avec vos paramètres configurés. Les administrateurs peuvent choisir de personnaliser cette stratégie ou de créer leur propre stratégie pour la remplacer.

Vous trouverez plus d’informations sur l’évaluation continue de l’accès en tant que contrôle de session dans la section [Personnaliser l’évaluation continue de l’accès](concept-conditional-access-session.md#customize-continuous-access-evaluation).

### <a name="strict-enforcement"></a>Application stricte 

Avec le dernier paramètre d’évaluation continue de l’accès sous Accès conditionnel, l’application stricte est une nouvelle fonctionnalité qui permet une sécurité renforcée basée sur deux facteurs : la variation d’adresse IP et la capacité du client. Cette fonctionnalité peut être activée lors de la personnalisation des options d’évaluation continue de l’accès pour une stratégie donnée. Lorsque vous activez l’application stricte, l’évaluation continue de l’accès révoque l’accès en cas de détection d’instance de [variation d’adresse IP](#ip-address-variation) ou en cas d’absence de [capacité de client](#client-capabilities) d’évaluation continue de l’accès.

> [!NOTE] 
> Vous devez uniquement activer une application stricte après vous être assuré que toutes les applications clientes prennent en charge la capacité de client et que vous avez inclus toutes vos adresses IP vues par Azure AD et les fournisseurs de ressources, comme Exchange online et Azure Resource Manager, dans votre stratégie de localisation sous Accès conditionnel. Dans le cas contraire, vous risquez d’être bloqué.

## <a name="limitations"></a>Limites

### <a name="group-membership-and-policy-update-effective-time"></a>Appartenance aux groupes et temps effectif de mise à jour de stratégie

Les modifications apportées aux stratégies d’accès conditionnel et à l’appartenance aux groupes effectuées par les administrateurs peuvent prendre jusqu’à une journée. Le délai est celui de la réplication entre les Azure AD et les fournisseurs de ressources comme Exchange Online et SharePoint en ligne. Certaines optimisations ont été effectuées pour les mises à jour de stratégie qui réduisent le délai à deux heures. Toutefois, il ne couvre pas encore tous les scénarios.  

Lorsque les modifications de stratégie d’accès conditionnel ou d’appartenance aux groupes doivent être appliquées immédiatement à certains utilisateurs, deux options s’offrent à vous. 

- Exécutez la commande [revoke-azureaduserallrefreshtoken PowerShell command](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) pour révoquer tous les jetons d’actualisation d’un utilisateur spécifié.
- Sélectionnez « révoquer la session » dans la page profil utilisateur du Portail Azure pour révoquer la session de l’utilisateur et vous assurer que les stratégies mises à jour seront appliquées immédiatement.

### <a name="ip-address-variation"></a>Variation d’adresse IP

Votre fournisseur d’identité et vos fournisseurs de ressources peuvent voir des adresses IP différentes. Cette discordance peut se produire pour les raisons suivantes :

- Implémentations du proxy réseau dans votre organisation
- Configurations IPv4/IPv6 incorrectes entre votre fournisseur d’identité et le fournisseur de ressources
 
Exemples :

- Votre fournisseur d’identité voit une adresse IP du client.
- Votre fournisseur de ressources voit une adresse IP du client différente après un passage via un proxy.
- L’adresse IP que voit votre fournisseur d’identité fait partie d’une plage d’adresses IP autorisées dans la stratégie, mais pas l’adresse IP du fournisseur de ressources.

Pour éviter les boucles infinies en raison de ces scénarios, Azure AD émet un jeton EAC d’une heure et n’applique pas la modification de l’emplacement du client. Dans ce cas, la sécurité est améliorée par rapport aux jetons d’une heure traditionnels, car nous évaluons encore les [autres événements](#critical-event-evaluation) à côté des événements de changement d’emplacement de client.

### <a name="supported-location-policies"></a>Stratégies d’emplacement prises en charge

L’EAC a uniquement un aperçu des [emplacements nommés basés sur IP](../conditional-access/location-condition.md#ip-address-ranges). L’EAC n’a pas d’insights sur d’autres paramètres d’emplacement, tels que des [adresses IP approuvées pour l’authentification multifacteur](../authentication/howto-mfa-mfasettings.md#trusted-ips) ou des emplacements basés sur le pays. Quand la provenance de l’utilisateur est une adresse IP approuvée pour l’authentification multifacteur ou un emplacement approuvé, à savoir une adresse IP approuvée pour l’authentification multifacteur ou un emplacement de pays, l’évaluation continue de l’accès n’est pas appliquée après le déplacement de l’utilisateur vers un autre emplacement. Dans ce cas, Azure AD émettra un jeton d’évaluation continue de l’accès d’une heure sans vérification de l’application d’adresse IP instantanée. 

> [!IMPORTANT]
> Quand vous configurez des emplacements pour l’évaluation continue de l’accès, utilisez uniquement la [condition d’emplacement d’accès conditionnel basé sur l’adresse IP](../conditional-access/location-condition.md), et configurez toutes les adresses IP, **y compris IPv4 et IPv6**, que votre fournisseur d’identité et votre fournisseur de ressources peuvent voir. N’utilisez pas les conditions d’emplacement de pays ni la fonctionnalité d’adresses IP approuvées disponibles dans la page des paramètres de service Authentification multifacteur Azure AD.

### <a name="office-and-web-account-manager-settings"></a>Paramètres du gestionnaire de comptes web et Office

| Canal de mise à jour Azure | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Canal d’entreprise semi-annuel | Si la valeur est Activé ou 1, l’EAC n’est pas prise en charge. | Si la valeur est Activé ou 1, l’EAC n’est pas prise en charge. |
| Canal actuel <br> or <br> Canal d’entreprise mensuel | L’EAC est prise en charge quel que soit le paramètre | L’EAC est prise en charge quel que soit le paramètre |

Pour une explication concernant les canaux Office Update, consultez [Vue d’ensemble des canaux de mise à jour pour Microsoft 365 Apps](/deployoffice/overview-update-channels). La recommandation est que les organisations ne désactivent pas le gestionnaire de compte Web (WAM).

### <a name="coauthoring-in-office-apps"></a>Co-création dans les applications Office

Quand plusieurs utilisateurs collaborent simultanément sur un document, l’accès de l’utilisateur au document peut ne pas être immédiatement révoqué par l’évaluation continue de l’accès, en fonction des événements de révocation d’utilisateur ou de changement de stratégie. Dans ce cas, l’utilisateur perd l’accès complètement après : 

- La fermeture du document
- La fermeture de l’application Office
- Après une période de 10 heures

Pour réduire cette durée, un administrateur SharePoint peut réduire la durée de vie maximale des sessions de cocréation pour les documents stockés dans SharePoint Online et OneDrive Entreprise, en [configurant une stratégie d’emplacement réseau dans SharePoint Online](/sharepoint/control-access-based-on-network-location). Une fois cette configuration modifiée, la durée de vie maximale des sessions de cocréation est réduite à 15 minutes, et peut être ajustée davantage à l’aide de la commande SharePoint Online PowerShell « [Set-SPOTenant –IPAddressWACTokenLifetime](/powershell/module/sharepoint-online/set-spotenant) ».

### <a name="enable-after-a-user-is-disabled"></a>Activer après la désactivation d’un utilisateur

Si vous activez un utilisateur juste après la désactivation, il y a une certaine latence avant que le compte soit reconnu comme activé dans les services Microsoft en aval.

- SharePoint Online et Teams ont généralement un délai de 15 minutes. 
- Exchange Online a généralement un délai de 35-40 minutes. 

### <a name="push-notifications"></a>Notifications Push

Une stratégie d’adresse IP n’est pas évaluée avant la libération des notifications push. Ce scénario existe parce que les notifications push sont sortantes et n’ont pas d’adresse IP associée à évaluer. Si un utilisateur clique sur cette notification push, par exemple un e-mail dans Outlook, les stratégies d’adresse IP d’EAC sont toujours appliquées pour que l’e-mail puisse s’afficher. Les notifications push affichent un aperçu des messages, qui n’est pas protégé par une stratégie d’adresse IP. Toutes les autres vérifications d’EAC sont effectuées avant l’envoi de la notification push. Si l’accès est supprimé pour un utilisateur ou un appareil, la mise en œuvre se produit au cours de la période documentée. 

## <a name="faqs"></a>FAQ

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Comment l’EAC fonctionnera-t-elle avec la fréquence de connexion ?

La fréquence de connexion sera respectée avec ou sans EAC.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser des API dotées d’Évaluation continue de l’accès dans vos applications](../develop/app-resilience-continuous-access-evaluation.md)
- [Contestations liées aux revendications, demandes de revendications, et fonctionnalités clientes](../develop/claims-challenge.md)
- [Accès conditionnel : session](concept-conditional-access-session.md)
- [Résoudre les problèmes rencontrés avec la fonctionnalité Évaluation continue de l’accès](howto-continuous-access-evaluation-troubleshoot.md)
