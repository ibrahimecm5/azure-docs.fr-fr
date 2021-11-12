---
title: Comment résoudre des problèmes à l’aide des journaux de ressources du service Azure Web PubSub
description: Découvrez comment obtenir les journaux de ressources et les utiliser pour résoudre des problèmes.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: e8bf85166b0557f30909d8109ecdd36782a4adc1
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995100"
---
# <a name="how-to-troubleshoot-with-resource-logs"></a>Comment résoudre les problèmes liés aux journaux de ressources

Ce guide pratique vous présente les options permettant d’obtenir les journaux de ressource et la façon de les utiliser pour résoudre des problèmes.

## <a name="whats-the-resource-logs"></a>Que sont les journaux de ressources ?

Les journaux de ressources fournissent une vue plus détaillée des informations de connectivité, de messagerie et de requête HTTP à l’instance du service Azure Web PubSub. Ils peuvent être utilisés pour l’identification des problèmes, le suivi des connexions, le suivi des messages, le traçage des requêtes HTTP et l’analyse.

Il existe trois types de journaux : le journal de connectivité, le journal de messagerie et les journaux de requête HTTP.

### <a name="connectivity-logs"></a>Journaux de connectivité

Les journaux de connectivité fournissent des informations détaillées sur les connexions au hub Azure Web PubSub. Par exemple, des informations de base (identifiant utilisateur, ID de connexion, etc.) et des informations sur l’événement (connexion, déconnexion, abandon, etc.). C’est la raison pour laquelle le journal de connectivité est utile pour résoudre les problèmes liés à la connexion.

### <a name="messaging-logs"></a>Journaux de messagerie

Les journaux de messagerie fournissent des informations de suivi pour les messages du hub Azure Web PubSub reçus et envoyés via le service Azure Web PubSub. Par exemple, l’ID de suivi et le type de message du message. En général, le message est enregistré lorsqu’il arrive au service ou en part. Les journaux de messagerie sont donc utiles pour résoudre les problèmes liés aux messages.

### <a name="http-request-logs"></a>Journaux de requête HTTP

Les journaux de requête HTTP fournissent des informations de suivi pour les requêtes HTTP au service Azure Web PubSub. Par exemple, la méthode HTTP et le code d’état. En général, la requête HTTP est enregistrée lorsqu’elle arrive au service ou en part. Les journaux de requête HTTP sont donc utiles pour résoudre les problèmes liés aux requêtes.

## <a name="capture-resource-logs-with-live-trace-tool"></a>Capturer les journaux de ressources avec l’outil Live Trace 

L’outil de suivi dynamique du service Azure Web PubSub permet de collecter des journaux de ressources en temps réel, ce qui est utile pour effectuer un suivi de l’environnement de développement du client. L’outil Live Trace peut capturer les journaux de connectivité, les journaux de messagerie et les journaux de requête HTTP.

> [!NOTE]
> Les journaux de ressources en temps réel capturés par l’outil de suivi dynamique sont facturés comme des messages (trafic sortant).

> [!NOTE]
> L’instance du service Azure Web PubSub créée au niveau de service gratuit a une limite quotidienne de messages (trafic sortant).

### <a name="launch-the-live-trace-tool"></a>Lancer l’outil de suivi dynamique

1. Accédez au portail Azure. 
2. Sur la page **Paramètres de Live Trace** de votre instance de service Azure Web PubSub, activez la case à cocher **Activer Live Trace** si elle est désactivée.
3. Cochez toute catégorie de journal dont vous avez besoin.
4. Cliquez sur le bouton **Enregistrer** et attendez que les paramètres prennent effet.
5. Cliquez sur *Ouvrir l’outil Live Trace*

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="Capture d’écran montrant comment lancer l’outil de trace dynamique.":::

### <a name="capture-the-resource-logs"></a>Capturer les journaux de ressources

L’outil de suivi dynamique offre certaines fonctionnalités fondamentales pour vous aider à capturer les journaux de ressources à des fins de dépannage.

* **Capturer** : Commencez à capturer les journaux de ressources en temps réel de l’instance Azure Web PubSub à l’aide de l’outil de suivi dynamique.
* **Effacer** : Effacez les journaux de ressources en temps réel capturés.
* **Filtre de journal** : L’outil de suivi dynamique vous permet de filtrer les journaux de ressources en temps réel capturés à l’aide d’un mot clé spécifique. Le séparateur commun (par exemple, espace, virgule, point-virgule, etc.) sera traité comme faisant partie du mot-clé. 
* **État** : L’état indique si l’outil de suivi dynamique est connecté ou déconnecté de l’instance spécifique.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="Capture d’écran de la capture des journaux de ressources avec l’outil de traçage dynamique.":::

Les journaux de ressources en temps réel capturés par l’outil de suivi dynamique contiennent des informations détaillées pour la résolution des problèmes. 

| Nom | Description |
| ------------ |  ------------------------ | 
| Temps | Heure de l’événement de journal |
| Niveau du journal | Niveau d’événement du journal (trace/débogage/informations/avertissement/erreur) |
| Nom de l'événement | Nom d’opération de l’événement |
| Message | Message détaillé de l’événement de journal |
| Exception | Exception d’exécution du service Azure Web PubSub |
| Hub | Nom du hub défini par l’utilisateur |
| ID de connexion | Identité de la connexion |
| ID d'utilisateur | Identité de l’utilisateur |
| IP | Adresse IP du client |
| Modèle de routage | Modèle de routage de l’API |
| Méthode HTTP | Méthode HTTP (POST/GET/PUT/DELETE) |
| URL | L’URL |
| ID de trace | Identificateur unique de l’invocation |
| Code d’état | Code de réponse HTTP |
| Duration | La durée entre la réception de la requête et son traitement |
| En-têtes | Informations supplémentaires passées par le client et le serveur à l’aide d’une requête ou d’une réponse HTTP |

Une fois que le service Azure Web PubSub sera en disponibilité générale, l’outil de suivi dynamique permettra également d’exporter les journaux dans un format spécifique et de les partager avec d’autres personnes à des fins de dépannage. 

## <a name="capture-resource-logs-with-azure-monitor"></a>Capturer les journaux de ressources avec Azure Monitor

### <a name="how-to-enable-resource-logs"></a>Comment activer les journaux de ressources

Actuellement, Azure Web PubSub prend en charge l’intégration avec [Stockage Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

1. Accédez au portail Azure.
2. Sur la page **Paramètres de diagnostic** de votre instance de service Azure Web PubSub, cliquez sur le lien **+ Ajouter un paramètre de diagnostic**.
3. Dans **Nom du paramètre de diagnostic**, entrez le nom du paramètre.
4. Dans **Détails de la catégorie**, sélectionnez toute catégorie de journal dont vous avez besoin.
5. Dans **Détails de la destination**, cochez **Archiver dans un compte de stockage**.
6. Cliquez sur le bouton **Enregistrer** pour enregistrer le paramètre de diagnostic.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="Capture d’écran de l’affichage des paramètres de diagnostic et de la création d’un nouveau":::

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="Capture d’écran de la configuration des détails des paramètres de diagnostic":::

> [!NOTE]
> Le compte de stockage doit être dans la même région que le service Azure Web PubSub.

### <a name="archive-to-a-storage-account"></a>Archiver dans un compte de stockage

Les journaux sont stockés dans le compte de stockage configuré dans le volet **Paramètres de diagnostic**. Un conteneur nommé `insights-logs-<CATEGORY_NAME>` est automatiquement créé pour stocker les journaux de ressources. Dans le conteneur, les journaux sont stockés dans le fichier `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. Fondamentalement, le chemin d’accès est combiné par `resource ID` et `Date Time`. Les fichiers journaux sont fractionnés par `hour`. Par conséquent, les minutes sont toujours `m=00`.

Tous les journaux d’activité sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne au format décrit dans les sections suivantes.

Les chaînes JSON du journal d’archivage incluent les éléments répertoriés dans les tableaux suivants :

**Format**

Nom | Description
------- | -------
time | Heure de l’événement de journal
level | Niveau de l’événement de journal
resourceId | ID de ressource de votre Azure SignalR Service
location | Emplacement de votre Azure SignalR Service
catégorie | Catégorie de l’événement de journal
operationName | Nom d’opération de l’événement
callerIpAddress | Adresse IP de votre serveur/client
properties | Propriétés détaillées relatives à cet événement de journal. Pour plus d’informations, voir le tableau des propriétés ci-dessous

**Tableau des propriétés**

Nom | Description
------- | -------
collection | Collection de l’événement de journal. Les valeurs autorisées sont : `Connection`, `Authorization` et `Throttling`
connectionId | Identité de la connexion
userId | Identité de l’utilisateur
message | Message détaillé de l’événement de journal
hub | Nom du hub défini par l’utilisateur |
routeTemplate | Modèle de routage de l’API |
httpMethod | Méthode HTTP (POST/GET/PUT/DELETE) |
url | L’URL |
traceId | Identificateur unique de l’invocation |
statusCode | Code de réponse HTTP |
duration | La durée entre la réception de la requête et son traitement |
headers | Informations supplémentaires passées par le client et le serveur à l’aide d’une requête ou d’une réponse HTTP |

Le code suivant est un exemple de chaîne JSON de journal d’archivage :

```json
{
  "properties": {
    "message": "Connection started",
    "collection": "Connection",
    "connectionId": "LW61bMG2VQLIMYIVBMmyXgb3c418200",
    "userId": null
  },
  "operationName": "ConnectionStarted",
  "category": "ConnectivityLogs",
  "level": "Informational",
  "callerIpAddress": "167.220.255.79",
  "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYGROUP/PROVIDERS/MICROSOFT.SIGNALRSERVICE/WEBPUBSUB/MYWEBPUBSUB",
  "time": "2021-09-17T05:25:05Z",
  "location": "westus"
}
```

## <a name="troubleshoot-with-the-resource-logs"></a>Résoudre les problèmes avec les journaux de ressources

En cas de problème de croissance ou de perte inattendues de la connexion, vous pouvez tirer parti des journaux de ressources pour résoudre les problèmes. Les problèmes courants sont souvent liés à des modifications inattendues de la quantité des connexions, où les connexions atteignent les limites de connexion et entraînent un échec d’autorisation.

### <a name="unexpected-connection-number-changes"></a>Modifications inattendues du nombre de connexions

#### <a name="unexpected-connection-dropping"></a>Abandon de connexion inattendu

Si une connexion est perdue, les journaux de ressources enregistrent cet événement de déconnexion avec `ConnectionAborted` ou `ConnectionEnded` dans `operationName`.

La différence entre `ConnectionAborted` et `ConnectionEnded` est que `ConnectionEnded` est une déconnexion attendue déclenchée par le côté client ou serveur. Tandis que `ConnectionAborted` est généralement un événement d’abandon de connexion inattendu dont le motif est fourni dans `message`.

Les motifs d’abandon sont répertoriés dans le tableau suivant :

| Motif | Description |
| ------- | ------- |
| Le nombre de connexions atteint la limite | Le nombre de connexions atteint la limite de votre niveau tarifaire actuel. Envisagez une montée en puissance de l’unité de service
| Rechargement du service, reconnexion | Le service Azure Web PubSub se recharge. Vous devez implémenter votre propre mécanisme de reconnexion ou vous reconnecter manuellement au service Azure Web PubSub |
| Erreur temporaire de serveur interne | Une erreur temporaire se produit dans le service Azure Web PubSub, qui doit être récupérée automatiquement

#### <a name="unexpected-connection-growing"></a>Croissance inattendue des connexions

Pour résoudre les problèmes de croissance inattendue des connexions, la première chose à faire est de filtrer les connexions supplémentaires. Vous pouvez ajouter un ID d’utilisateur de test unique à votre connexion de client de test. Ensuite, vérifiez les journaux de ressources. Si vous constatez que plusieurs connexions de client ont le même identifiant utilisateur de test ou la même adresse IP, il est probable que le côté client crée et établisse plus de connexions que prévu. Vérifiez votre côté client.

### <a name="authorization-failure"></a>Échec de l’autorisation

Si l’erreur 401 Non autorisé est retournée pour des demandes de clients, vérifiez vos journaux de ressources. Si vous rencontrez l’erreur `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, cela signifie qu’aucune des audiences dans votre jeton d’accès n’est valide. Essayez d’utiliser les audiences valides suggérées dans le journal.

### <a name="throttling"></a>Limitation

Si vous constatez que vous ne pouvez pas établir de connexions client au service Azure Web PubSub, vérifiez vos journaux de ressources. Si vous rencontrez `Connection count reaches limit` dans le journal de ressources, cela signifie que vous établissez un trop grand nombre de connexions au service Azure Web PubSub, et que la limite du nombre de connexions est atteinte. Envisagez d’effectuer un scale-up de votre instance du service Azure Web PubSub. Si vous rencontrez `Message count reaches limit` dans le journal de ressources, cela signifie que vous utilisez le niveau gratuit et épuisez le quota de messages. Si vous souhaitez envoyer davantage de messages, envisagez de mettre à niveau votre instance du service Azure Web PubSub au niveau standard pour envoyer des messages supplémentaires. Pour plus d’informations, voir [Tarification du service Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/).
