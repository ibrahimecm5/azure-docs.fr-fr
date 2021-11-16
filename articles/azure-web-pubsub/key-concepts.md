---
title: Concepts de base Azure Web PubSub relatifs aux hubs, aux groupes et aux connexions
description: Mieux comprendre les termes utilisés dans Azure Web PubSub.
author: lianwei
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: dc05e81228149fbeae647af81f97bc1e3a320c1f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997701"
---
# <a name="azure-web-pubsub-basic-concepts"></a>Concepts de base d’Azure Web PubSub

Le service Azure Web PubSub vous aide à générer des applications web de messagerie en temps réel. Les clients se connectent au service à l’aide du [protocole WebSocket standard](https://datatracker.ietf.org/doc/html/rfc6455) et le service expose des [API REST](/rest/api/webpubsub) et des kits de développement logiciel (SDK) qui vous permettent de gérer ces clients.

## <a name="terms"></a>Termes

Voici quelques termes importants utilisés par le service :

[!INCLUDE [Terms](includes/terms.md)]

## <a name="workflow"></a>Workflow

Un flux de travail classique utilisant le service est illustré ci-dessous :

![Diagramme montrant le workflow Web PubSub Service.](./media/concept-service-internals/workflow.png)

Comme illustré par le graphique de workflow ci-dessus :

1. Un *client* se connecte au point de terminaison `/client` du service à l’aide du transport WebSocket. Le service transfère chaque trame WebSocket vers le serveur en amont configuré. La connexion WebSocket peut se connecter à n’importe quel sous-protocole personnalisé à gérer par le serveur ou se connecter au sous-protocole pris en charge par le service, ce qui permet aux clients `json.webpubsub.azure.v1` d’effectuer des actions Pub/Sub directement. Des informations détaillées sont disponibles dans la section [Protocole client](concept-service-internals.md#client_protocol).

2. Le service appelle le serveur à l'aide du **protocole HTTP CloudEvents** sur différents événements clients. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) est une définition standardisée et indépendante du protocole de la description de la structure et des métadonnées des événements hébergés par la Cloud Native Computing Foundation (CNCF). Des informations détaillées sont disponibles dans la section [Protocole serveur](concept-service-internals.md#server_protocol).

3. Le serveur peut appeler le service à l’aide de l’API REST pour envoyer des messages aux clients ou pour gérer les clients connectés. Des informations détaillées sont disponibles dans la section [Protocole serveur](concept-service-internals.md#server_protocol).