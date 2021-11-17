---
title: Participer à une réunion Teams
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les SDK Azure Communication Services pour se joindre à une réunion Teams.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 0b2469def75cc54efc9efced2c25587b466f4cf0
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063624"
---
# <a name="join-a-teams-meeting"></a>Se joindre à une réunion Teams

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

Les SDK Azure Communication Services peuvent permettre à vos utilisateurs de se joindre à des réunions Microsoft Teams normales. Voici comment faire !

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).
- Facultatif : Suivez le guide de démarrage rapide pour [ajouter l’appel vocal à votre application](../../quickstarts/voice-video-calling/getting-started-with-calling.md).

> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Pour participer à une réunion Teams, utilisez la méthode `join` et transmettez un lien de réunion ou les coordonnées d’une réunion.

Participer avec un lien de réunion :

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Participer avec des coordonnées de réunion :

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

Participer à l’aide d’un ID de réunion (actuellement en préversion limitée) :

```js
const locator = { meetingId: '<MEETING_ID>'}
const call = callAgent.join(locator);
```

## <a name="next-steps"></a>Étapes suivantes
- [Découvrir comment gérer les appels](./manage-calls.md)
- [Découvrir comment gérer la vidéo](./manage-video.md)
- [Découvrir comment transférer des appels](./transfer-calls.md)
