---
title: Comment configurer un gestionnaire d’événements
description: Aide relative aux concepts de gestionnaire d’événements et introduction à l’intégration lors du développement avec le service Azure Web PubSub.
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 10/31/2021
ms.openlocfilehash: 45ad4d9d6d441584a512f5666245aed4020778cb
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576393"
---
# <a name="event-handler-in-azure-web-pubsub-service"></a>Gestionnaire d’événements dans le service Azure Web PubSub

Le gestionnaire d’événements gère les événements clients entrants. Les gestionnaires d’événements sont enregistrés et configurés dans le service via le portail ou Azure CLI au préalable, de sorte que lorsqu’un événement client est déclenché, le service peut déterminer si l’événement est supposé être géré ou non. Nous prenons maintenant en charge le gestionnaire d’événements côté serveur, qui expose un point de terminaison accessible au public pour que le service puisse l’appeler lorsque l’événement est déclenché. En d’autres termes, il agit comme un **webhook**. 

Le service fournit des événements client au webhook en amont à l’aide du [protocole HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

Pour chaque événement, il formule une requête HTTP POST au service en amont enregistré et attend une réponse HTTP. 

L’envoi des données du service au serveur est toujours au format `binary` CloudEvents.

:::image type="content" source="media/howto-develop-eventhandler/event-trigger.png" alt-text="Capture d’écran du déclencheur d’événements du service Web PubSub.":::

## <a name="upstream-and-validation"></a>Serveur en amont et validation

Lors de la configuration du point de terminaison webhook, l’URL peut utiliser le paramètre `{event}` pour définir un modèle d’URL. Le service calcule de manière dynamique la valeur de l’URL du webhook lorsque la demande du client arrive. Par exemple, lorsqu’une demande `/client/hubs/chat` arrive, avec un modèle d'URL de gestionnaire d’événements configuré `http://host.com/api/{event}` pour le hub `chat`, lorsque le client se connecte, il publie d’abord vers cette URL : `http://host.com/api/connect`. Cela peut être utile lorsqu’un client WebSocket PubSub envoie des événements personnalisés, et le gestionnaire d’événements aide à distribuer différents événements vers différents serveurs en amont. Notez que le paramètre `{event}` n’est pas autorisé dans le nom de domaine de l’URL.

Quand vous configurez le gestionnaire d’événements en amont par le biais du portail Azure ou de l’interface CLI, le service suit la [protection contre les abus CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) pour valider le webhook en amont. Chaque URL inscrite de webhook en amont sera validée par ce mécanisme. L’en-tête de demande `WebHook-Request-Origin` est défini sur le nom de domaine de service `xxx.webpubsub.azure.com` et il s’attend à ce que la réponse ayant l’en-tête `WebHook-Allowed-Origin` contienne ce nom de domaine ou `*`.

Lors de la validation, le paramètre `{event}` est résolu en `validate`. Par exemple, lorsqu’il essaie de définir l’URL sur `http://host.com/api/{event}`, le service essaie de poser des **OPTIONS** sur une demande vers `http://host.com/api/validate` et c’est seulement lorsque la réponse est valide que la configuration peut être définie avec succès.

À l’heure actuelle, [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) et [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) ne sont pas pris en charge.

## <a name="authentication-between-service-and-webhook"></a>Authentification entre le service et le webhook

- Mode anonyme
- Authentification simple avec `?code=<code>` est fournie par l’URL de webhook configurée comme paramètre de requête.
- Authentification AAD. 
   - Ajoutez une clé secrète client dans les [inscriptions d’applications] de AAD et fournissez la [clé secrète client] à Azure Web PubSub via le portail/l’interface CLI.
   - Fournissez l’[identité](../app-service/overview-managed-identity.md?tabs=dotnet) à Azure Web PubSub via le portail/l’interface CLI.

## <a name="configure-event-handler"></a>Configurer un gestionnaire d’événements

### <a name="configure-through-azure-portal"></a>Configurer via Portail Azure

Recherchez votre service Azure Web PubSub à partir de **Portail Azure**. Accédez à **Paramètres** et entrez votre nom de hub. Cliquez ensuite sur **Ajouter** pour configurer l’URL du webhook côté serveur. N’oubliez pas de cliquer sur **Enregistrer** lorsque vous avez terminé.

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="Capture d’écran de la définition du gestionnaire d’événements.":::

### <a name="configure-through-azure-cli"></a>Configurer via Azure CLI

Utilisez les commandes de groupe [**az webpubsub hub**](/cli/azure/webpubsub/hub) d’Azure CLI pour configurer les paramètres du gestionnaire d’événements.

Commandes | Description
--|--
create | Crée les paramètres de hub pour le service WebPubSub.
supprimer | Supprime les paramètres de hub pour le service WebPubSub.
list   | Répertorie tous les paramètres de hub pour le service WebPubSub.
show   | Affiche les paramètres de hub pour le service WebPubSub.
update | Met à jour les paramètres de hub pour le service WebPubSub.

Voici un exemple de création de deux URL de webhook pour le hub `MyHub` de la ressource `MyWebPubSub`.

```azurecli-interactive
az webpubsub hub create -n "MyWebPubSub" -g "MyResourceGroup" --hub-name "MyHub" --event-handler url-template="http://host.com" user-event-pattern="*" --event-handler url-template="http://host2.com" system-event="connected" system-event="disconnected" auth-type="ManagedIdentity" auth-resource="uri://myUri"
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [next step](includes/include-next-step.md)]