---
title: 'Démarrage rapide : utiliser cURL et REST pour gérer la base de connaissances : réponses aux questions personnalisées'
description: Ce guide de démarrage rapide vous montre comment créer, publier et interroger votre base de connaissances à l’aide des API REST.
ms.date: 11/02/2021
ms.topic: include
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2768081da14be2c481093d54c833f1cde45e36a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029015"
---
## <a name="prerequisites"></a>Prérequis

* Version actuelle de [cURL](https://curl.haxx.se/). Plusieurs commutateurs de ligne de commande sont utilisés dans les guides de démarrage rapide, qui sont indiqués dans la [documentation cURL](https://curl.haxx.se/docs/manpage.html).
* Les questions et réponses personnalisées nécessitent une [ressource Langage](../../../qnamaker/how-to/set-up-qnamaker-service-azure.md?tabs=v2#create-a-new-qna-maker-service) avec la fonctionnalité de réponses aux questions personnalisées activée pour générer une clé d’API et un point de terminaison. Le **nom** que vous avez choisi lors de la création de votre ressource est utilisé en tant que sous-domaine de votre point de terminaison. Pour récupérer la clé et le nom de votre ressource, sélectionnez **Démarrage rapide** pour votre ressource dans le portail Azure. Le nom de la ressource constitue le premier sous-domaine de l’URL du point de terminaison : <!--TODO: Change link-->

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2`

> [!CAUTION]
> Les exemples BASH suivants utilisent le caractère de continuation de ligne `\`. Si votre console ou terminal utilise un caractère de continuation de ligne différent, utilisez ce caractère.

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Pour créer une base de connaissances avec les API REST et cURL, vous devez disposer des informations suivantes :

|Information|Configuration cURL|Objectif|
|--|--|--|
|Nom de la ressource de langue (fonctionnalité de réponses aux questions personnalisées activée)|URL|Utilisé pour construire l’URL|
|Clé de ressource de langue|Param `-h` pour l’en-tête `Ocp-Apim-Subscription-Key`|S’authentifier auprès du service de langue|
|JSON décrivant la base de connaissances|Param `-d`|[Exemples](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Taille du JSON en octets|Param `-h` pour l’en-tête `Content-Size`||

La commande cURL est exécutée à partir d’un interpréteur de commandes BASH. Modifiez cette commande avec vos propres informations relatives au nom de ressource, à la clé de ressource ainsi qu’aux valeurs JSON et à la taille de JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

La réponse cURL de questions-réponses comprend `operationId`, qui est requis pour [obtenir l’état de l’opération](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Obtenir l’état de l’opération

Quand vous créez une base de connaissances, comme l’opération est asynchrone, la réponse contient des informations pour déterminer l’état.

|Information|Configuration cURL|Objectif|
|--|--|--|
|Nom de la ressource de langue (fonctionnalité de réponses aux questions personnalisées activée)|URL|Utilisé pour construire l’URL|
|ID d’opération|Itinéraire d’URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Clé de ressource de langue|Param `-h` pour l’en-tête `Ocp-Apim-Subscription-Key`|S’authentifier auprès du service de langue|

La commande cURL est exécutée à partir d’un interpréteur de commandes BASH. Modifiez cette commande avec vos propres informations relatives au nom de ressource, à la clé de ressource et à l’ID d’opération.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

La réponse cURL comprend l’état. Si l’état de l’opération est Réussi, `resourceLocation` contient l’ID de la base de connaissances.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publier une base de connaissances

Avant d’interroger la base de connaissances, vous devez publier la base de connaissances.

|Information|Configuration cURL|Objectif|
|--|--|--|
|Nom de la ressource de langue (fonctionnalité de réponses aux questions personnalisées activée)|URL|Utilisé pour construire l’URL|
|Clé de ressource de langue|Param `-h` pour l’en-tête `Ocp-Apim-Subscription-Key`|S’authentifier auprès du service de langue|
|ID de la base de connaissances|Itinéraire d’URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

La commande cURL est exécutée à partir d’un interpréteur de commandes BASH. Modifiez cette commande avec vos propres informations relatives au nom de ressource, à la clé de ressource et à l’ID de la base de connaissances.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

L’état de la réponse est 204 sans résultats. Utilisez le paramètre de ligne de commande `-v` pour afficher la sortie détaillée de la commande cURL. Cela inclut l’état HTTP.

## <a name="query-for-answer-from-published-knowledge-base"></a>Requête relative à une réponse de la base de connaissances publiée

|Information|Configuration cURL|Objectif|
|--|--|--|
|Nom de la ressource de langue (fonctionnalité de réponses aux questions personnalisées activée)|URL|Utilisé pour construire l’URL|
|Clé de ressource de langue|Param `-h` pour l’en-tête `Ocp-Apim-Subscription-Key`|S’authentifier auprès du service de langue|
|ID de la base de connaissances|Itinéraire d’URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON décrivant la requête|Param `-d`|[Paramètres du corps de la demande](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) et [exemples](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Taille du JSON en octets|Param `-h` pour l’en-tête `Content-Size`||

La commande cURL est exécutée à partir d’un interpréteur de commandes BASH. Modifiez cette commande avec vos propres informations relatives au nom de ressource, à la clé de ressource et à l’ID de la base de connaissances.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Une réponse correcte comprend la meilleure réponse avec d’autres informations dont une application cliente, telle qu’un chatbot, a besoin afin d’afficher une réponse pour l’utilisateur.

## <a name="delete-knowledge-base"></a>Supprimer une base de connaissances

Quand vous avez fini d’utiliser la base de connaissances, supprimez-la.

|Information|Configuration cURL|Objectif|
|--|--|--|
|Nom de la ressource de langue (fonctionnalité de réponses aux questions personnalisées activée)|URL|Utilisé pour construire l’URL|
|Clé de ressource de langue|Param `-h` pour l’en-tête `Ocp-Apim-Subscription-Key`|S’authentifier auprès du service de langue|
|ID de la base de connaissances|Itinéraire d’URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

La commande cURL est exécutée à partir d’un interpréteur de commandes BASH. Modifiez cette commande avec vos propres informations relatives au nom de ressource, à la clé de ressource et à l’ID de la base de connaissances.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

L’état de la réponse est 204 sans résultats. Utilisez le paramètre de ligne de commande `-v` pour afficher la sortie détaillée de la commande cURL. Cela inclut l’état HTTP.

## <a name="additional-resources"></a>Ressources supplémentaires

* Documentation de référence sur la [création](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)
* Documentation de référence sur le [runtime](/rest/api/cognitiveservices/qnamaker4.0/runtime)
* [Exemples de scripts BASH utilisant cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
