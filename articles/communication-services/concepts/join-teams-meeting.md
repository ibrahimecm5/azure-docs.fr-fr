---
title: Interopérabilité des réunions Teams
titleSuffix: An Azure Communication Services concept document
description: Participer aux réunions Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 74a39ff953c1ef8e549dbace0c7fc495976fae2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325521"
---
# <a name="join-a-teams-meeting"></a>Participer à une réunion Teams

> [!IMPORTANT]
> L’interopérabilité BYOI est en préversion publique et disponible pour toutes les applications Communication Services et toutes les organisations Teams.
>
> Les API et kits de développement logiciel (SDK) en préversion sont fournis sans contrat de niveau de service, et déconseillés pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Communication Services peut être utilisé pour créer des applications qui permettent aux utilisateurs de rejoindre des réunions Teams et d’y participer. La [tarification standard d’ACS](https://azure.microsoft.com/pricing/details/communication-services/) s’applique à ces utilisateurs, mais aucuns frais supplémentaires ne sont facturés pour la capacité d’interopérabilité elle-même. Grâce au modèle BYOI (bring your own identity), vous contrôlez l’authentification des utilisateurs, et les utilisateurs de vos applications n’ont pas besoin de licences Teams pour participer à des réunions Teams. C’est l’idéal pour les solutions d’entreprise à consommateur qui permettent aux utilisateurs Teams sous licence (par exemple, les prestataires de soins de santé ou les conseillers financiers) et aux utilisateurs externes (par exemple, les patients ou les clients) utilisant une application personnalisée de participer à une expérience de consultation virtuelle.

Il est également possible d’utiliser les identités Microsoft 365 Teams avec les Kits de développement logiciel (SDK) Azure Communication Services. Des informations supplémentaires sont disponibles [ici](./teams-interop.md).

Actuellement, il n’est pas possible pour un utilisateur Teams de rejoindre un appel qui a été initié en utilisant le Kit de développement logiciel (SDK) Appel d’Azure Communication Services.

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Activation de la participation anonyme aux réunions dans votre locataire Teams

Lorsqu’un utilisateur BYOI se joint à une réunion Teams, il est traité comme un utilisateur externe anonyme, tout comme les utilisateurs qui se joignent anonymement à une réunion Teams en utilisant l’application web Teams. La possibilité pour les utilisateurs BYOI de rejoindre des réunions Teams en tant qu’utilisateurs anonymes est contrôlée par la configuration « autoriser l’accès anonyme aux réunions » qui contrôle également l’accès anonyme aux réunions Teams. Ce paramètre peut être mis à jour dans le [Centre d’administration Teams](https://admin.teams.microsoft.com/meetings/settings) ou avec l’applet de commande PowerShell Teams [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration).  

Les applications personnalisées créées avec Azure Communication Services pour se connecter à Teams et communiquer avec les utilisateurs de Teams peuvent être utilisées par des utilisateurs finaux ou par des bots, et il n’y a aucune différence dans la façon dont elles apparaissent aux utilisateurs de Teams, sauf si le développeur de l’application l’indique explicitement dans le cadre de la communication. Votre application personnalisée doit envisager l’authentification des utilisateurs et d’autres mesures de sécurité pour protéger les réunions Teams. Pensez à ce que cela implique en terme de sécurité si vous autorisez les utilisateurs anonymes à rejoindre des réunions et utilisez le [Guide sur la sécurité de Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) pour configurer les fonctionnalités disponibles pour les utilisateurs anonymes.

## <a name="meeting-experience"></a>Expérience de réunion

Comme avec l’accès anonyme aux réunions Teams, votre application doit disposer du lien de la réunion pour y accéder, qui peut être récupéré via l’API Graph ou à partir du calendrier dans Microsoft Teams. Le nom des utilisateurs BYOI affiché dans Teams est configurable par l’intermédiaire du Kit de développement logiciel (SDK) Appel de Communication Services, et ces utilisateurs sont étiquetés comme « externes » pour indiquer aux utilisateurs de Teams qu’ils n’ont pas été authentifiés à l’aide d’Azure Active Directory. Lorsque le premier utilisateur ACS se joint à une réunion Teams, le client Teams affiche un message indiquant que certaines fonctionnalités peuvent ne pas être disponibles parce que l’un des participants utilise un client personnalisé.

Lors d’une réunion, les utilisateurs de Communication Services pourront utiliser les principales fonctionnalités audio, vidéo, de partage d’écran et de conversation via les Kits de développement logiciel (SDK) d’Azure Communication Services. Lorsqu’un utilisateur de Communication Services quitte la réunion ou que la réunion se termine, il ne peut plus envoyer ni recevoir de nouveaux messages de conversation, mais il a accès aux messages envoyés et reçus pendant la réunion. Les utilisateurs anonymes de Communication Services ne peuvent pas ajouter/retirer d’autres participants à la réunion ni ne peuvent démarrer l’enregistrement ou la transcription de la réunion.

Vous trouverez des informations supplémentaires sur les flux requis pour la participation à des réunions Teams sur la [page architecture du client et du serveur](client-and-server-architecture.md). L'[exemple d'appel de groupe Hero](../samples/calling-hero-sample.md) fournit un code d'exemple pour rejoindre une réunion Teams à partir d'une application Web.

## <a name="privacy"></a>Confidentialité
L’interopérabilité entre Azure Communication Services et Microsoft Teams permet à vos applications et utilisateurs de participer à des appels, réunions et conversations Teams. Il vous incombe de vous assurer que les utilisateurs de votre application sont avertis quand l’enregistrement ou la transcription sont activés dans le cadre d’un appel ou d’une réunion Teams.

Microsoft vous indique par le biais de l’API Azure Communication Services que l’enregistrement ou la transcription a commencé et vous devez communiquer ce fait, en temps réel, à vos utilisateurs dans l’interface utilisateur de votre application. Vous acceptez d’indemniser Microsoft pour tous les coûts et dommages résultant de votre non-respect de cette obligation.

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

- Un utilisateur BYOI peut se joindre à une réunion Teams planifiée pour un canal Teams et utiliser l’audio et la vidéo, mais il ne pourra pas envoyer ni recevoir de messages de conversation, car il n’est pas membre du canal.
- Lorsque vous utilisez Microsoft Graph pour [dresser la liste des participants à une réunion Teams](/graph/api/call-list-participants), les détails concernant les utilisateurs de Communication Services ne sont actuellement pas inclus.
- Les présentations PowerPoint ne sont pas rendues pour les utilisateurs Communication Services.
- Les réunions Teams prennent en charge jusqu’à 1 000 participants, mais le Kit de développement logiciel (SDK) Appel d’Azure Communication Services ne prend actuellement en charge que 350 participants.
- Avec [Cloud Video Interop pour Microsoft Teams](/microsoftteams/cloud-video-interop), certains appareils ont rencontré des problèmes lorsqu’un utilisateur de Communication Services partage son écran.
- Les fonctionnalités telles que Lever la main, le mode ensemble et les salles de pause ne sont disponibles que pour les utilisateurs de Teams.
- Le Kit de développement logiciel (SDK) Appel ne prend actuellement pas en charge les sous-titres pour les réunions Teams.
- Les utilisateurs de Communication Services ne peuvent pas participer aux [événements en direct Teams](/microsoftteams/teams-live-events/what-are-teams-live-events).
- Les [événements du gestionnaire d’activités Teams](/microsoftteams/platform/bots/bot-basics?tabs=csharp) pour les bots ne se déclenchent pas lorsque les utilisateurs de Communication Services rejoignent une réunion Teams.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique : Participer à une réunion Teams](../how-tos/calling-sdk/teams-interoperability.md)
- [Démarrage rapide : joindre une application appelante BYOI à une réunion Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [Démarrage rapide : joindre une application de conversation BYOI à une réunion Teams](../quickstarts/chat/meeting-interop.md)
