---
title: Comment utiliser l’outil de trace dynamique pour le service Azure SignalR
description: Découvrez comment utiliser l’outil de trace dynamique pour le service Azure SignalR
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 6fecb2f492a71781706e19a407a87cb1af7dc006
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470259"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Comment utiliser l’outil de trace dynamique pour le service Azure SignalR

L’outil de trace dynamique est une application web unique pour la capture et l’affichage des traces dynamiques dans le service Azure SignalR. Les traces dynamiques peuvent être collectées en temps réel sans aucune dépendance vis-à-vis d’autres services.
Vous pouvez activer et désactiver la fonctionnalité de trace dynamique en un seul clic. Vous pouvez également choisir n’importe quelle catégorie de journal qui vous intéresse.

> [!NOTE]
> Notez que les traces dynamiques sont comptabilisées comme messages sortants.

## <a name="launch-the-live-trace-tool"></a>Lancer l’outil de suivi dynamique

1. Accédez au portail Azure.
2. Activez la case à cocher **Activer la trace dynamique**.
3. Cliquez sur le bouton **Enregistrer** dans la barre d’outils et attendez que les modifications prennent effet.
4. Dans la page **Paramètres de diagnostic** de votre instance du service Azure Web PubSub, sélectionnez **Ouvrir l’outil de suivi dynamique**. 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="Capture d’écran montrant comment lancer l’outil de trace dynamique.":::

## <a name="capture-live-traces"></a>Capturer des traces dynamiques

L’outil de trace dynamique offre certaines fonctionnalités fondamentales vous permettant de capturer les traces dynamiques à des fins de dépannage.

* **Capturer** : Commencez à capturer les traces dynamiques en temps réel de l’instance Azure Web PubSub à l’aide de l’outil de trace dynamique.
* **Effacer** : Effacez les traces dynamiques en temps réel capturées.
* **Exporter** : Exportez les traces dynamiques dans un fichier. Le format de fichier actuellement pris en charge est CSV.
* **Filtre de journal** : L’outil de trace dynamique vous permet de filtrer les traces dynamiques en temps réel capturées à l’aide d’un mot clé spécifique. Le séparateur commun (par exemple, espace, virgule, point-virgule, etc.) sera traité comme faisant partie du mot-clé. 
* **État** : L’état indique si l’outil de suivi dynamique est connecté ou déconnecté de l’instance spécifique.

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="Capture d’écran montrant la capture de traces dynamiques avec l’outil de trace dynamique.":::

Les traces dynamiques en temps réel capturées par l’outil de trace dynamique contiennent des informations détaillées pour la résolution des problèmes. 

| Name | Description |
| ------------ |  ------------------------ | 
| Temps | Heure de l’événement de journal |
| Niveau du journal | Niveau d’événement du journal (trace/débogage/informations/avertissement/erreur) |
| Nom de l'événement | Nom d’opération de l’événement |
| Message | Message détaillé de l’événement de journal |
| Exception | Exception d’exécution du service Azure Web PubSub |
| Hub | Nom du hub défini par l’utilisateur |
| ID de connexion | Identité de la connexion |
| Type de la connexion | Type de la connexion. Les valeurs autorisées sont `Server` (connexions entre le serveur et le service) et `Client` (connexions entre le client et le service)|
| ID d'utilisateur | Identité de l’utilisateur |
| IP | Adresse IP du client |
| Server Sticky | Mode de routage du client. Les valeurs autorisées sont `Disabled`, `Preferred` et `Required`. Pour plus d’informations, consultez [ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode). |
| Transport | Transport que le client peut utiliser pour envoyer des requêtes HTTP. Les valeurs autorisées sont `WebSockets`, `ServerSentEvents` et `LongPolling`. Pour plus d’informations, consultez [HttpTransportType](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype). |
| ID de suivi des messages | Identificateur unique d’un message |
| Modèle de routage | Modèle de routage de l’API |
| Méthode HTTP | Méthode HTTP (POST/GET/PUT/DELETE) |
| URL | Localisateur de ressource uniforme |
| ID de trace | Identificateur unique pour représenter une requête |
| Code d’état | Code de réponse HTTP |
| Duration | Durée entre la réception de la requête et son traitement |
| En-têtes | Informations supplémentaires passées par le client et le serveur à l’aide d’une requête ou d’une réponse HTTP |
| ID d’invocation | Identificateur unique pour représenter une invocation (disponible uniquement pour ASP.NET SignalR) |
| Type de message | Type du message (BroadcastDataMessage/JoinGroupMessage/LeaveGroupMessage/...) |

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a permis d’apprendre à utiliser l’outil de trace dynamique. Vous pouvez également apprendre à traiter les problèmes courants :
* Guides de dépannage : Consultez notre [guide de dépannage](./signalr-howto-troubleshoot-guide.md) pour savoir comment résoudre les problèmes courants liés aux traces dynamiques.
* Méthodes de résolution des problèmes : Consultez notre [présentation de méthodes de résolution des problèmes](./signalr-howto-troubleshoot-method.md), qui simplifient l’auto-diagnostic pour trouver la cause racine directement ou réduire le problème.
