---
title: Interopérabilité des appels et des conversations dans Teams
titleSuffix: An Azure Communication Services concept document
description: Interopérabilité des appels et des conversations dans Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 328bfff2366efb416cbd41452a821256f675d0e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078690"
---
# <a name="teams-interoperability-calling-and-chat"></a>Interopérabilité Teams : appels et conversations

> [!IMPORTANT]
> L’interopérabilité des appels et des conversations est en préversion privée et limitée à un nombre restreint d’utilisateurs précoces d’Azure Communication Services. Vous pouvez [envoyer ce formulaire pour demander à participer à la préversion](https://forms.office.com/r/F3WLqPjw0D) et nous examinerons votre ou vos scénarios afin d’évaluer votre participation à la préversion.
>
> Les API et les SDK en préversion privée sont fournis sans contrat de niveau de service et ne sont pas appropriés pour les charges de travail de production. Utilisez-les seulement avec des utilisateurs de test et des données de test. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Pour obtenir de l’aide, poser des questions, envoyer des commentaires ou signaler des problèmes, utilisez le [Canal de conversation et d’appel ad hoc de l’interopérabilité Teams](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47). Vous devez être membre de l’équipe TAP d’Azure Communication Services.

Dans le cadre de cette préversion, les SDK Azure Communication Services peuvent être utilisés pour créer des applications qui permettent aux utilisateurs BYOI (bring your own identity) de démarrer des appels un à un ou des conversations un à plusieurs avec des utilisateurs Teams. Les [prix ACS standard](https://azure.microsoft.com/pricing/details/communication-services/) s’appliquent à ces utilisateurs, mais aucuns frais supplémentaires ne sont facturés pour la capacité d’interopérabilité elle-même.



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Activation de l’interopérabilité des appels et des conversations dans votre locataire Teams
Pour activer les appels et les conversations entre vos utilisateurs Communication Services et votre locataire Teams, utilisez la nouvelle applet de commande Teams PowerShell [Set-CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration). Cette applet de commande est uniquement disponible pour les participants de la préversion privée. 

Les applications personnalisées créées avec Azure Communication Services pour se connecter à Teams et communiquer avec les utilisateurs Teams peuvent être utilisées par des utilisateurs finaux ou par des bots, et il n’y a aucune différence dans la façon dont elles sont présentées aux utilisateurs Teams, sauf si le développeur de l’application l’indique explicitement.

Pour démarrer un appel ou une conversation avec un utilisateur Teams, vous avez besoin de l’ID d’objet Azure Active Directory (AAD) de l’utilisateur. Vous pouvez l’obtenir avec l’[API Microsoft Graph](/graph/api/resources/users), ou dans votre annuaire local si vous utilisez [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (ou un autre mécanisme) pour synchroniser votre annuaire local et AAD.

## <a name="calling"></a>Appel
Avec le SDK d’appel, un utilisateur ou un point de terminaison Communication Services peut démarrer un appel un à un avec des utilisateurs Teams, identifiés par leur ID d’objet Azure Active Directory (AAD). Vous pouvez facilement modifier une application existante qui appelle d’autres utilisateurs Communication Services pour qu’elle appelle un utilisateur Teams à la place.
 
[Gérer les appels - Guide pratique d’Azure Communication Services | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

Appel d’un autre utilisateur ACS :
```js
const acsCallee = { communicationUserId: <'ACS_USER_ID>' }
const call = callAgent.startCall([acsCallee]);
```

Appel d’un utilisateur Teams :
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**Limitations et problèmes connus**
- Les utilisateurs Teams doivent être en mode « TeamsOnly ». Les utilisateurs Skype Entreprise ne peuvent pas recevoir d’appels un à un d’utilisateurs Communication Services.
- L’escalade vers un appel de groupe n’est pas prise en charge.
- Les utilisateurs Communication Services ne s’affichent pas correctement dans l’historique des appels
- L’enregistrement des appels Communication Services n’est pas disponible pour les appels un à un.
- Les fonctionnalités avancées de routage des appels, comme le transfert d’appels, la répartition des appels entrants, la sonnerie simultanée et la messagerie vocale, ne sont pas prises en charge.
- Les utilisateurs Teams ne peuvent pas définir les utilisateurs Communication Services comme cibles de transfert.
- La branche LyncIpPhone n’est pas prise en charge.

## <a name="chat"></a>Conversation
Avec le SDK de conversation, les utilisateurs ou les points de terminaison Communication Services peuvent démarrer une conversation un à plusieurs avec des utilisateurs Teams, identifiés par leur ID d’objet Azure Active Directory (AAD). Vous pouvez facilement modifier une application existante qui crée des conversations avec d’autres utilisateurs Communication Services pour qu’elle crée des conversations avec les utilisateurs Teams à la place :
                                            
[Démarrage rapide : Ajouter un système de conversation à votre application](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)

Création d’une conversation avec un utilisateur Teams :
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<TEAMS_USER_ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

**Fonctionnalités prises en charge**
-   Envoyer/recevoir des messages (type : texte, texte enrichi, émoticônes) 
-   L’utilisateur Communication Services peut modifier les messages envoyés
-   Supprimer les messages envoyés
-   Recevoir des notifications en temps réel (événements liés aux conversations et aux messages pris actuellement en charge par ACS)
-   Envoyer et recevoir des indicateurs de saisie
-   Envoyer et recevoir des accusés de lecture
-   Ajouter un participant et partager l’historique des messages : l’utilisateur Teams peut ajouter seulement des utilisateurs Teams. L’utilisateur Communication Services peut ajouter des utilisateurs Teams et Communication Services.
-   Supprimer un participant existant de la conversation
-   Quitter la conversation
-   Mettre à jour le sujet de conversation
-   L’utilisateur Communication Services peut supprimer la conversation.


**Limitations et problèmes connus**
- La modification de messages par l’utilisateur Teams échoue.
- La suppression d’une conversation par l’utilisateur Communication Services supprime l’historique des messages pour l’utilisateur Teams et retire l’utilisateur Teams de la conversation.
- L’interface utilisateur du client Teams pour les utilisateurs externes est incohérente.
- Dans le client Teams, un appel ne peut pas être lancé avec les participants de la conversation


## <a name="privacy"></a>Confidentialité
L’interopérabilité entre Azure Communication Services et Microsoft Teams permet à vos applications et utilisateurs de participer à des appels, réunions et conversations Teams. Il vous incombe de vous assurer que les utilisateurs de votre application sont avertis quand l’enregistrement ou la transcription sont activés dans le cadre d’un appel ou d’une réunion Teams.

Microsoft vous indique par le biais de l’API Azure Communication Services que l’enregistrement ou la transcription a commencé, et vous devez communiquer ce fait en temps réel à vos utilisateurs dans l’interface utilisateur de votre application. Vous acceptez d’indemniser Microsoft pour tous les coûts et dommages résultant de votre non-respect de cette obligation.
