---
title: 'Démarrage rapide : bibliothèque de client de réponses aux questions personnalisées pour Python'
description: Ce guide de démarrage rapide montre comment commencer à utiliser la bibliothèque de client QnA Maker pour Python.
ms.topic: include
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cdca2bce7a2731d4a819bd9e24d8aba5ab61b782
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029024"
---
Utilisez la bibliothèque de client de réponses aux questions personnalisées pour Python afin de :

* Créer une base de connaissances
* Mettre à jour une base de connaissances
* Publier une base de connaissances
* Attendre l’exécution d’une tâche de longue durée
* Télécharger une base de connaissances
* Obtenir une réponse d’une base de connaissances
* Supprimer une base de connaissances

[Documentation de référence](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Exemples Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* * Les questions et réponses personnalisées nécessitent une [ressource Langage](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) avec la fonctionnalité de réponses aux questions personnalisées activée pour générer une clé d’API et un point de terminaison.
    * Après le déploiement de votre ressource de langage, sélectionnez **Accéder à la ressource**. Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API QnA Maker. Collez votre clé et votre point de terminaison dans le code ci-dessous, plus loin dans le guide de démarrage rapide.

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un fichier Python nommé `quickstart-file.py` et importez les bibliothèques suivantes.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource.

<!-- TODO: Replace Link

- We use subscription key and authoring key interchangeably. For more details on authoring key, follow [Keys](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

-->

- Le format de la valeur de QNA_MAKER_ENDPOINT est `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Accédez au portail Azure, puis recherchez la ressource linguistique que vous avez créée à l’étape des prérequis. Sélectionnez la page **Clés et point de terminaison**, sous **Gestion des ressources** pour trouver la clé de création (abonnement) et le point de terminaison.

    > [!div class="mx-imgBorder"]
    > ![Point de terminaison de création de questions-réponses personnalisées](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)
 
- Pour la production, envisagez d’utiliser une méthode de stockage et d’accès sécurisée pour vos informations d’identification. Par exemple, [Azure Key Vault](../../../../key-vault/general/overview.md) fournit un stockage de clé sécurisé.

    [!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Modèles objet

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker) utilise le modèle objet suivant :
* **[QnAMakerClient](#qnamakerclient-object-model)** est l’objet utilisé pour créer, gérer, publier, télécharger et interroger la base de connaissances.

[!INCLUDE [Get KBinformation](../../../qnamaker/includes/quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modèle objet QnAMakerClient

Le client QnA Maker de création est un objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient) qui s’authentifie auprès d’Azure à l’aide de Microsoft.Rest.ServiceClientCredentials, qui contient votre clé.

Une fois le client créé, utilisez la propriété [Knowledge base](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations) pour créer, gérer et publier votre base de connaissances.

Gérez votre base de connaissances en envoyant un objet JSON. Pour les opérations immédiates, une méthode retourne généralement un objet JSON indiquant l’état. Pour les opérations de longue durée, la réponse est l’ID d’opération. Appelez la méthode [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#get-details-kb-id--custom-headers-none--raw-false----operation-config-) avec l’ID d’opération pour déterminer l’[état de la requête](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>Modèle objet QnAMakerRuntimeClient

La réponse à une question personnalisée ne nécessite pas l’utilisation de l’objet QnAMakerRuntimeClient. Vous appelez plutôt [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) directement sur l’objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient).

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Authentifier le client pour la création de la base de connaissances

Instanciez un client avec votre point de terminaison et la clé. Créez un objet CognitiveServicesCredentials avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient).

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Créer une base de connaissances

Utilisez l’objet client pour obtenir un objet [knowledge base operations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations).

Une base de connaissances stocke des paires de questions et réponses pour l’objet [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) à partir de trois sources :

* Pour le **contenu éditorial**, utilisez l'objet [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto).
    * Pour utiliser les métadonnées et les invites de suivi, utilisez le contexte éditorial, car cette donnée est ajoutée au niveau de chaque paire question/réponse.
* Pour les **fichiers**, utilisez l'objet [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto). FileDTO contient le nom de fichier et l’URL publique d’accès au fichier.
* Pour les **URL**, utilisez une liste de chaînes pour représenter les URL disponibles publiquement.

Appelez la méthode [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#create-create-kb-payload--custom-headers-none--raw-false----operation-config-), puis passer l’ID d’opération retourné à la méthode [Operations.getDetails](#get-status-of-an-operation) pour interroger l’état.

La dernière ligne du code suivant retourne l’ID de la base de connaissances à partir de la réponse de MonitorOperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

Veillez à inclure la fonction [`_monitor_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de créer une base de connaissances.

## <a name="update-a-knowledge-base"></a>Mettre à jour une base de connaissances

Vous pouvez mettre à jour une base de connaissances en passant l’ID de celle-ci et un [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) contenant des objets DTO [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) et [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) à la méthode [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations). Utilisez la méthode [Operation.getDetail](#get-status-of-an-operation) pour déterminer si la mise à jour a réussi.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

Veillez à inclure la fonction [`_monitor_operation`](#get-status-of-an-operation), référencée dans le code ci-dessus, afin de mettre à jour une base de connaissances.

## <a name="download-a-knowledge-base"></a>Télécharger une base de connaissances

Utilisez la méthode [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations) pour télécharger la base de données sous la forme d’une liste de [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Ceci n'est _pas_ équivalent à l’exportation à partir de la page **Paramètres** du portail QnA Maker, car le résultat de cette méthode n’est pas un fichier TSV.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

Publiez la base de connaissances à l’aide de la méthode [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#publish-kb-id--custom-headers-none--raw-false----operation-config-). Celle-ci prend le modèle actuel enregistré et entraîné, référencé par l’ID de base de connaissances, et le publie sur un point de terminaison.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

## <a name="query-a-knowledge-base"></a>Interroger une base de connaissances

### <a name="generate-an-answer-from-the-knowledge-base"></a>Générer une réponse à partir de la base de connaissances

Générez une réponse à partir d’une base de connaissances publiée en utilisant la méthode [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-). Cette méthode accepte l’ID de la base de connaissances et [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto). Accédez à des propriétés supplémentaires de QueryDTO comme Top et Context, que vous pouvez utiliser dans votre chat bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

<!-- TODO: Replace Link
This is a simple example of querying the knowledge base. To understand advanced querying scenarios, review [other query examples](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).
-->

## <a name="delete-a-knowledge-base"></a>Supprimer une base de connaissances

Supprimez la base de connaissances à l’aide de la méthode [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations#delete-kb-id--custom-headers-none--raw-false----operation-config-) avec comme paramètre l’ID de la base de connaissances.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

## <a name="get-status-of-an-operation"></a>Obtenir l’état d’une opération

Certaines méthodes, telles que Create et Update, peuvent prendre tellement de temps qu’au lieu d’attendre que le processus se termine, une [opération](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)) est retournée. Utilisez l’ID de l’opération pour interroger (avec une logique de nouvelle tentative) afin de déterminer l’état de la méthode d’origine.

L’appel _setTimeout_ dans le bloc de code suivant est utilisé pour simuler le code asynchrone. Remplacez cela par la logique de nouvelle tentative.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande python de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).
