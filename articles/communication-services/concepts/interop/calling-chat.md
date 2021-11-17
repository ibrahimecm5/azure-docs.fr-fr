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
ms.openlocfilehash: 10435eba4127fbb58d939bfac0710aa90e2c7b32
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846251"
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

Pour démarrer un appel ou une conversation avec un utilisateur Teams, vous avez besoin de l’ID d’objet Azure Active Directory (Azure AD) de l’utilisateur. Vous pouvez l’obtenir avec l’[API Microsoft Graph](/graph/api/resources/users), ou dans votre annuaire local si vous utilisez [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (ou un autre mécanisme) pour effectuer une synchronisation entre votre annuaire local et Azure AD.

## <a name="calling"></a>Appel
Avec le SDK d’appel, un utilisateur ou un point de terminaison Communication Services peut démarrer un appel un à un avec des utilisateurs Teams, identifiés par leur ID d’objet Azure Active Directory (Azure AD). Vous pouvez facilement modifier une application existante qui appelle d’autres utilisateurs Communication Services pour qu’elle appelle un utilisateur Teams à la place.
 
[Gérer les appels - Guide pratique d’Azure Communication Services | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

Appel d’un autre point de terminaison Communication Services à l’aide de [communicationUserId](/javascript/api/@azure/communication-common/communicationuseridentifier?view=azure-node-latest#communicationUserId) :
```js
const acsCallee = { communicationUserId: '<ACS User ID>' }
const call = callAgent.startCall([acsCallee]);
```

Appel d’un utilisateur Teams à l’aide de [microsoftTeamsUserId](/javascript/api/@azure/communication-common/microsoftteamsuseridentifier?view=azure-node-latest#microsoftTeamsUserId) :
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**Limitations et problèmes connus**
- Les utilisateurs Teams doivent être en mode « TeamsOnly ». Les utilisateurs Skype Entreprise ne peuvent pas recevoir d’appels un à un d’utilisateurs Communication Services.
- L’escalade vers un appel de groupe n’est pas prise en charge.
- L’enregistrement des appels Communication Services n’est pas disponible pour les appels un à un.
- Les fonctionnalités avancées de routage des appels, comme le transfert d’appels, la répartition des appels entrants, la sonnerie simultanée et la messagerie vocale, ne sont pas prises en charge.
- Les utilisateurs Teams ne peuvent pas définir les utilisateurs Communication Services comme cibles de transfert.
- Un certain nombre de fonctionnalités du client Teams ne fonctionnent pas comme prévu lors des appels 1:1 avec les utilisateurs de Communication Services.
- Les [appareils pour Teams](/MicrosoftTeams/devices/teams-ip-phones) et [téléphones IP Skype](/skypeforbusiness/certification/devices-ip-phones) tiers ne sont pas pris en charge.

## <a name="chat"></a>Conversation
Avec le SDK de conversation, les utilisateurs ou points de terminaison Communication Services peuvent avoir des conversations de groupe avec des utilisateurs Teams, identifiés par leur ID d’objet Azure Active Directory (AAD). Vous pouvez facilement modifier une application existante qui crée des conversations avec d’autres utilisateurs de Communication Services pour qu’elle crée des conversations avec les utilisateurs Teams à la place. Voici un exemple d’utilisation du kit de développement logiciel (SDK) Chat pour ajouter des utilisateurs Teams en tant que participants. Pour savoir comment utiliser le kit de développement logiciel (SDK) Chat pour envoyer un message, gérer les participants, etc., consultez notre [guide de démarrage rapide](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript).

Création d’une conversation avec un utilisateur Teams :
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<Teams User AAD Object ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

Pour faciliter le test, nous avons publié un exemple d’application [ici](https://github.com/Azure-Samples/communication-services-web-chat-hero/tree/teams-interop-chat-adhoc). Pour commencer, mettez à jour l’application avec votre ressource Communication Services et votre locataire Teams activé pour l’interopérabilité. 

**Limitations et problèmes connus** </br>
En préversion privée, un utilisateur de Communication Services peut effectuer diverses actions à l’aide du kit de développement logiciel (SDK) Chat Communication Services, y compris l’envoi et la réception de messages texte simples et riches, d’indicateurs de frappe, d’accusés de lecture, de notifications en temps réel, etc. La plupart des fonctionnalités de conversation Teams ne sont toutefois pas prises en charge. Voici quelques comportements clés et problèmes connus :
-   Les conversations ne peuvent être lancées que par les utilisateurs de Communication Services. 
-   Les utilisateurs de Communication Services ne peuvent pas envoyer ou recevoir des GIF, des images ou des fichiers. Des liens vers des fichiers et des images peuvent être partagés.
-   Les utilisateurs de Communication Services peuvent supprimer la conversation. Cela permet de supprimer l’utilisateur de Teams du thread de conversation et masque l’historique des messages du client Teams.
- Problème connu : les utilisateurs de Communication Services ne s’affichent pas correctement dans la liste des participants. Ils sont actuellement affichés comme externes, mais leur carte de visite peut être incohérente. 
- Problème connu : une conversation ne peut pas être convertie en appel à partir de l’application Teams. 
- Problème connu : la modification des messages par l’utilisateur de Teams n’est pas prise en charge. 

## <a name="privacy"></a>Confidentialité
L’interopérabilité entre Azure Communication Services et Microsoft Teams permet à vos applications et utilisateurs de participer à des appels, réunions et conversations Teams. Il vous incombe de vous assurer que les utilisateurs de votre application sont avertis quand l’enregistrement ou la transcription sont activés dans le cadre d’un appel ou d’une réunion Teams.

Microsoft vous indique par le biais de l’API Azure Communication Services que l’enregistrement ou la transcription a commencé, et vous devez communiquer ce fait en temps réel à vos utilisateurs dans l’interface utilisateur de votre application. Vous acceptez d’indemniser Microsoft pour tous les coûts et dommages résultant de votre non-respect de cette obligation.
