---
title: Traiter les messages volumineux dans les flux de travail à l’aide de la segmentation
description: Gérez les messages volumineux à l’aide de la segmentation dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.topic: how-to
ms.date: 12/18/2020
ms.openlocfilehash: bcbad7c9a71ae5045f24ee25b8de409ece544c0f
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179162"
---
# <a name="handle-large-messages-in-workflows-using-chunking-in-azure-logic-apps"></a>Gérer les messages volumineux à l’aide de la segmentation dans Azure Logic Apps

Azure Logic Apps a fixé différentes limites maximales à la taille du contenu des messages que les déclencheurs et les actions peuvent traiter dans les flux de travail d’application logique, en fonction du type de ressource d’application logique et de l’environnement dans lequel le flux de travail d’application logique s’exécute. Ces limites permettent de réduire toute surcharge résultant du stockage et du traitement de [messages volumineux](#what-is-large-message). Pour plus d’informations sur les limites de taille des messages, consultez [Limites des messages dans Azure Logic Apps](logic-apps-limits-and-config.md#messages).

Si vous utilisez des actions HTTP intégrées ou des actions spécifiques de connecteur managé et que vous avez besoin qu’Azure Logic Apps travaille avec des messages dont la taille dépasse les limites par défaut, vous pouvez activer la *segmentation*, qui fractionne un message volumineux en messages plus petits. De cette façon, vous pouvez toujours transférer des fichiers volumineux dans des conditions spécifiques. En fait, lorsque vous utilisez ces actions HTTP intégrées ou des actions spécifiques de connecteur managé, la segmentation est la seule façon dont Azure Logic Apps peut consommer des messages volumineux. Cette exigence signifie que l’échange sous-jacent de messages HTTP entre Azure Logic Apps et d’autres services doit utiliser la segmentation ou que les connexions créées par les connecteurs managés que vous souhaitez utiliser doivent également prendre en charge la segmentation.

> [!NOTE]
> Azure Logic Apps ne prend pas en charge la segmentation sur les déclencheurs en raison de l’augmentation de la surcharge due à l’échange de plusieurs messages.
> En outre, Azure Logic Apps implémente la segmentation des actions HTTP à l’aide de son propre protocole, comme décrit dans cet article. Ainsi, même si votre site web ou service web prend en charge la segmentation, il ne fonctionnera pas avec la segmentation des actions HTTP. Pour utiliser la segmentation des actions HTTP avec votre site web ou service web, vous devez implémenter le même protocole que celui utilisé par Azure Logic Apps. Dans le cas contraire, n’activez pas la segmentation des actions HTTP. 

Cet article fournit une vue d’ensemble du fonctionnement de la segmentation dans Azure Logic Apps et explique comment configurer la segmentation des actions prises en charge.

<a name="what-is-large-message"></a>

## <a name="what-makes-messages-large"></a>Qu’est-ce qui rend les messages « volumineux » ?

Les messages sont considérés comme « volumineux » selon le service qui les gère. La taille maximale réelle des messages volumineux varie selon les connecteurs et Logic Apps. Logic Apps et les différents connecteurs sont incapables de gérer directement les messages volumineux, qui doivent donc être segmentés. Pour connaître la taille maximale de message pour Logic Apps, consultez l’article [Limites et configuration de Logic Apps](../logic-apps/logic-apps-limits-and-config.md).
Pour connaître la taille maximale de message pour les différents connecteurs, consultez les [détails techniques spécifiques à chaque connecteur](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Gestion des messages segmentés pour Logic Apps

Logic Apps ne peut pas utiliser directement les sorties provenant de messages segmentés dont la taille dépasse la limite autorisée. Seules les actions qui prennent en charge la segmentation peuvent accéder au contenu du message dans ces sorties. Par conséquent, une action qui gère les messages volumineux doit répondre à *l’un* de ces critères :

* Prendre en charge la segmentation de manière native lorsque cette action appartient à un connecteur. 
* Avoir la prise en charge de la segmentation activée dans la configuration d’exécution de cette action. 

Dans le cas contraire, vous obtenez une erreur d’exécution lorsque vous essayez d’accéder à un contenu volumineux. Pour activer la segmentation, consultez l’article [Set up chunking support](#set-up-chunking) (Configurer la prise en charge de la segmentation).

### <a name="chunked-message-handling-for-connectors"></a>Gestion des messages segmentés pour les connecteurs

Les services qui communiquent avec Logic Apps peuvent avoir leurs propres limites de taille de message. Ces limites sont souvent inférieures à la limite de Logic Apps. Par exemple, en supposant qu’un connecteur prend en charge la segmentation, celui-ci peut considérer un message de 30 Mo comme volumineux, alors que Logic Apps ne le considérera pas comme tel. Pour respecter la limite de ce connecteur, Logic Apps fractionne tout message supérieur à 30 Mo en segments plus petits.

Pour les connecteurs qui prennent en charge la segmentation, le protocole de segmentation sous-jacent n’est pas visible par les utilisateurs finaux. Toutefois, tous les connecteurs ne prennent pas en charge la segmentation. Ces connecteurs génèrent alors des erreurs d’exécution lorsque les messages entrants dépassent la taille maximale autorisée pour ces connecteurs.

Pour les actions qui prennent en charge et sont activées pour la segmentation, vous ne pouvez pas utiliser les corps, les variables et les expressions du déclencheur, comme `@triggerBody()?['Content']`, car l’utilisation de l’une de ces entrées empêche l’opération de segmentation de se produire. Au lieu de cela, utilisez l’action [**Composer**](../logic-apps/logic-apps-perform-data-operations.md#compose-action). Plus précisément, vous devez créer un champ `body` à l’aide de l’action **Composer** pour stocker la sortie des données à partir du corps du déclencheur, de la variable, de l’expression, etc., par exemple :

```json
"Compose": {
    "inputs": {
        "body": "@variables('myVar1')"
    },
    "runAfter": {
        "Until": [
            "Succeeded"
        ]
    },
    "type": "Compose"
},
```
Ensuite, pour référencer les données, dans l’action de segmentation, utilisez `@body('Compose')`.

```json
"Create_file": {
    "inputs": {
        "body": "@body('Compose')",
        "headers": {
            "ReadFileMetadataFromServer": true
        },
        "host": {
            "connection": {
                "name": "@parameters('$connections')['sftpwithssh_1']['connectionId']"
            }
        },
        "method": "post",
        "path": "/datasets/default/files",
        "queries": {
            "folderPath": "/c:/test1/test1sub",
            "name": "tt.txt",
            "queryParametersSingleEncoded": true
        }
    },
    "runAfter": {
        "Compose": [
            "Succeeded"
        ]
    },
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "Chunked"
        }
    },
    "type": "ApiConnection"
},
```

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurer la segmentation via HTTP

Dans les scénarios HTTP génériques, vous pouvez fractionner les téléchargements et chargements de contenu volumineux via HTTP, afin que votre application logique et un point de terminaison puissent échanger des messages volumineux. Toutefois, vous devez segmenter ces messages conformément à la limite autorisée par Logic Apps. 

Si un point de terminaison prend en charge la segmentation pour les téléchargements ou les chargements, les actions HTTP dans votre application logique segmenteront automatiquement les messages volumineux. Dans le cas contraire, vous devez configurer la prise en charge de la segmentation sur le point de terminaison. Si vous ne possédez ou ne contrôlez pas le point de terminaison ou le connecteur, vous ne serez peut-être pas en mesure de configurer la segmentation.

En outre, si une action HTTP ne prend pas encore en charge la segmentation, vous devez également configurer la segmentation dans la propriété `runTimeConfiguration` de l’action. Vous pouvez définir cette propriété à l’intérieur de l’action, soit directement dans l’éditeur en mode Code comme décrit plus loin, soit dans le Concepteur Logic Apps comme décrit ci-après :

1. Dans le coin supérieur droit de l’action HTTP, sélectionnez le bouton représentant des points de suspension ( **...** ), puis **Paramètres**.

   ![Dans l’action, ouvrez le menu Paramètres](./media/logic-apps-handle-large-messages/http-settings.png)

2. Sous **Transfert de contenu**, définissez **Autoriser la segmentation** sur **Activé**.

   ![Activer la segmentation](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Pour poursuivre la configuration de la segmentation pour les téléchargements ou les chargements, consultez les sections suivantes.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Télécharger du contenu sous forme de segments

De nombreux points de terminaison envoient automatiquement les messages volumineux sous forme de segments lorsque ces derniers sont téléchargés via une requête HTTP GET. Pour télécharger des messages segmentés depuis un point de terminaison via HTTP, le point de terminaison doit prendre en charge les requêtes de contenu partiel, ou *téléchargements segmentés*. Lorsque votre application logique envoie une requête HTTP GET à un point de terminaison pour télécharger du contenu et que le point de terminaison répond avec un code d’état « 206 », la réponse contient le contenu segmenté. Logic Apps ne peut pas déterminer si un point de terminaison prend en charge les requêtes partielles. Toutefois, lorsque votre application logique reçoit la première réponse « 206 », votre application logique envoie automatiquement plusieurs requêtes pour télécharger l’ensemble du contenu.

Pour vérifier si un point de terminaison peut prendre en charge le contenu partiel, envoyez une requête HEAD. Cette requête vous permet de savoir si la réponse contient l’en-tête `Accept-Ranges`. De cette façon, si le point de terminaison prend en charge les téléchargements segmentés, mais n’envoie pas de contenu segmenté, vous pouvez *suggérer* cette option en définissant l’en-tête `Range` dans votre requête HTTP GET. 

Ces étapes décrivent le processus détaillé utilisé par Logic Apps pour télécharger le contenu segmenté d’un point de terminaison vers votre application logique :

1. Votre application logique envoie une requête HTTP GET au point de terminaison.

   L’en-tête de la requête peut éventuellement inclure un champ `Range` qui définit une plage d’octets pour la requête de segments de contenu.

2. Le point de terminaison renvoie le code d’état « 206 » et un corps de message HTTP.

    Les informations détaillées sur le contenu de ce segment s’affichent dans l’en-tête `Content-Range` de la réponse, notamment des informations qui permettent à Logic Apps d’identifier le début et la fin du segment, ainsi que la taille totale du contenu avant segmentation.

3. Votre application logique envoie automatiquement les requêtes HTTP GET suivantes.

    Votre application logique envoie des requêtes GET jusqu’à ce que l’ensemble du contenu soit récupéré.

Par exemple, cette définition d’action affiche une requête HTTP GET qui définit l’en-tête `Range`. L’en-tête *suggère* au point de terminaison de répondre en envoyant du contenu segmenté :

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

La requête GET définit l’en-tête « Range » sur « bytes=0-1023 », qui correspond à la plage d’octets. Si le point de terminaison prend en charge les requêtes de contenu partiel, le point de terminaison répond en envoyant un segment de contenu dans la plage demandée. Le format exact du champ d’en-tête « Range » peut varier selon les points de terminaison.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Charger du contenu sous forme de segments

Pour charger du contenu segmenté à partir d’une action HTTP, la prise en charge de la segmentation doit être activée dans la propriété `runtimeConfiguration` de l’action. Ce paramètre permet à l’action de démarrer le protocole de segmentation. Votre logique application peut ensuite envoyer un message POST ou PUT initial au point de terminaison cible. Une fois que le point de terminaison répond en envoyant une suggestion de taille de segment, votre application logique poursuit le processus en envoyant des requêtes HTTP PATCH qui contiennent les segments de contenu.

Ces étapes décrivent le processus détaillé utilisé par Logic Apps pour charger le contenu segmenté de votre application logique vers un point de terminaison :

1. Votre application logique envoie une requête HTTP POST ou PUT initiale avec un corps de message vide. L’en-tête de la requête inclut les informations sur le contenu que votre application logique souhaite envoyer sous forme de segments :

   | Champ d’en-tête de la requête Logic Apps | Valeur | Type | Description |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | segmenté | String | Indique que le contenu est chargé sous forme de segments |
   | **x-ms-content-length** | <*content-length*> | Integer | La taille, en octets, de l’intégralité du contenu avant segmentation |
   ||||

2. Le point de terminaison répond avec le code d’état de réussite « 200 » et ces informations facultatives :

   | Champ d’en-tête de réponse de point de terminaison | Type | Obligatoire | Description |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Non | La taille de segment suggérée en octets |
   | **Lieu** | String | Oui | L’adresse URL vers laquelle envoyer les messages HTTP PATCH |
   ||||

3. Votre application logique crée et envoie des messages HTTP PATCH de suivi, chacun contenant les informations suivantes :

   * Un segment de contenu correspondant à la taille **x-ms-chunk-size** ou à une taille calculée en interne, jusqu’à ce que tout le contenu correspondant à la valeur **x-ms-content-length** soit chargé de manière séquentielle

   * Ces informations d’en-tête relatives au segment de contenu envoyé dans chaque message PATCH :

     | Champ d’en-tête de la requête Logic Apps | Valeur | Type | Description |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | La plage d’octets pour le segment de contenu actuel, y compris la valeur de début, la valeur de fin et la taille totale du contenu, par exemple : « bytes=0-1023/10100 » |
     | **Content-Type** | <*content-type*> | String | Le type de contenu segmenté |
     | **Content-Length** | <*content-length*> | String | La longueur, en octets, du segment actuel |
     |||||

4. Après chaque requête PATCH, le point de terminaison confirme la réception de chaque segment en renvoyant le code d’état « 200 » et les en-têtes de réponse suivants :

   | Champ d’en-tête de réponse de point de terminaison | Type | Obligatoire | Description |
   |--------------------------------|------|----------|-------------|
   | **Plage** | String | Oui | La plage d’octets pour le contenu qui a été reçu par le point de terminaison, par exemple: « octets = 0-1023 » |   
   | **x-ms-chunk-size** | Integer | Non | La taille de segment suggérée en octets |
   ||||

Par exemple, cette définition d’action affiche une requête HTTP POST pour le chargement de contenu segmenté vers un point de terminaison. Dans la propriété `runTimeConfiguration` de l’action, la propriété `contentTransfer` définit `transferMode` sur `chunked` :

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
