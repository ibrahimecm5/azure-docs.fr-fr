---
title: Informations de référence pour Azure Functions à destination des développeurs Python
description: Développer des fonctions avec Python
ms.topic: article
ms.date: 11/4/2020
ms.custom: devx-track-python
ms.openlocfilehash: b4342a70e5fa7c5a0a7a2fd51dcfe97476f34995
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027201"
---
# <a name="azure-functions-python-developer-guide"></a>Guide des développeurs Python sur Azure Functions

Cet article est une introduction au développement d’Azure Functions avec Python. Le contenu ci-dessous suppose d’avoir lu le [Guide de développement Azure Functions](functions-reference.md).

En tant que développeur Python, vous pouvez également être intéressé par l’un des articles suivants :

| Prise en main | Concepts| Scénarios/exemples |
|--|--|--|
| <ul><li>[Fonction Python avec Visual Studio Code](./create-first-function-vs-code-python.md)</li><li>[Fonction Python avec le terminal/l’invite de commandes](./create-first-function-cli-python.md)</li></ul> | <ul><li>[Guide du développeur](functions-reference.md)</li><li>[Options d’hébergement](functions-scale.md)</li><li>[Considérations relatives aux&nbsp;performances](functions-best-practices.md)</li></ul> | <ul><li>[Classification d’images avec PyTorch](machine-learning-pytorch.md)</li><li>[Exemple Azure Automation](/samples/azure-samples/azure-functions-python-list-resource-groups/azure-functions-python-sample-list-resource-groups/)</li><li>[Machine learning avec TensorFlow](functions-machine-learning-tensorflow.md)</li><li>[Parcourir les exemples Python](/samples/browse/?products=azure-functions&languages=python)</li></ul> |

> [!NOTE]
> Même si vous pouvez [développer votre solution Azure Functions basée sur Python localement sur Windows](create-first-function-vs-code-python.md#run-the-function-locally), Python n’est pris en charge que sur un plan d’hébergement basé sur Linux lors de l’exécution dans Azure. Consultez la liste des combinaisons [système d’exploitation/runtime](functions-scale.md#operating-systemruntime) prises en charge.

## <a name="programming-model"></a>Modèle de programmation

Azure Functions s’attend à ce qu’une fonction soit une méthode sans état qui traite une entrée et produit une sortie dans un script Python. Par défaut, le runtime s’attend à ce que la méthode soit implémentée en tant que méthode globale nommée `main()` dans le fichier `__init__.py`. Vous pouvez également [spécifier un autre point d’entrée](#alternate-entry-point).

Les données issues des déclencheurs et des liaisons sont liées à la fonction par des attributs de méthode avec la propriété `name` qui est définie dans le fichier *function.json*. L’exemple de fichier _function.json_ ci-dessous décrit une fonction simple déclenchée par une requête HTTP nommée `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Sur la base de cette définition, le fichier `__init__.py` qui contient le code de fonction peut se présenter comme dans l’exemple suivant :

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Vous pouvez aussi déclarer explicitement les types d’attributs et le type de retour dans la fonction à l’aide d’annotations de type Python. L’utilisation des fonctionnalités IntelliSense et de saisie semi-automatique fournies par de nombreux éditeurs de code Python s’en trouve facilitée.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Utilisez les annotations Python incluses dans le package [azure.functions.*](/python/api/azure-functions/azure.functions) pour lier des entrées et des sorties à vos méthodes.

## <a name="alternate-entry-point"></a>Autre point d’entrée

Vous pouvez changer le comportement par défaut d’une fonction en spécifiant éventuellement les propriétés `scriptFile` et `entryPoint` dans le fichier *function.json*. Par exemple, le fichier _function.json_ (voir ci-dessous) indique au runtime d’utiliser la méthode `customentry()` dans le fichier _main.py_ comme point d’entrée de la fonction Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers recommandée d’un projet Python Functions se présente comme l’exemple suivant :

```
 <project_root>/
 | - .venv/
 | - .vscode/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function/
 | | - __init__.py
 | | - function.json
 | - shared_code/
 | | - __init__.py
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - tests/
 | | - test_my_second_function.py
 | - .funcignore
 | - host.json
 | - local.settings.json
 | - requirements.txt
 | - Dockerfile
```
Le dossier principal du projet (<project_root>) peut contenir les fichiers suivants :

* *local.settings.json* : Utilisé pour stocker les paramètres d’application et les chaînes de connexion lors d’une exécution locale. Ce fichier n’est pas publié sur Azure. Pour en savoir plus, consultez la section [local.settings.file](functions-develop-local.md#local-settings-file).
* *requirements.txt* : Contient la liste des packages Python que le système installe lors de la publication sur Azure.
* *host.json* : contient les options de configuration qui affectent toutes les fonctions d’une instance d’application de fonction. Ce fichier est publié sur Azure. Toutes les options ne sont pas prises en charge lors de l’exécution locale. Pour en savoir plus, consultez la section [host.json](functions-host-json.md).
* *.vscode/*  : (Facultatif) Contient la configuration VSCode du magasin. Pour plus d’informations, consultez [Paramètre VSCode](https://code.visualstudio.com/docs/getstarted/settings).
* *.venv/*  : (Facultatif) Contient un environnement virtuel Python utilisé par le développement local.
* *Dockerfile* : (Facultatif) Utilisé lors de la publication de votre projet dans un [conteneur personnalisé](functions-create-function-linux-custom-image.md).
* *tests/*  : (Facultatif) Contient les cas de test de votre application de fonction.
* *.funcignore* : (Facultatif) Déclare des fichiers qui ne devraient pas être publiés sur Azure. En règle générale, ce fichier contient `.vscode/` pour ignorer le paramètre de votre éditeur, `.venv/` pour ignorer l’environnement virtuel Python local, `tests/` pour ignorer les cas de test et `local.settings.json` pour empêcher la publication des paramètres de l’application locale.

Chaque fonction a son propre fichier de code et son propre fichier de configuration de liaison (function.json).

Quand vous déployez votre projet dans une application de fonction sur Azure, tout le contenu du dossier principal du projet ( *<project_root>* ) doit être inclus dans le package, mais pas le dossier lui-même, ce qui signifie que `host.json` doit se trouver dans la racine du package. Nous vous recommandons de conserver vos tests dans un dossier avec d’autres fonctions, `tests/` dans cet exemple. Pour plus d’informations, consultez [Test unitaire](#unit-testing).

## <a name="import-behavior"></a>Comportement d’importation

Vous pouvez importer des modules dans votre code de fonction en utilisant des références absolues et relatives. Sur la base de la structure de dossiers présentée ci-dessus, les importations suivantes fonctionnent à partir du fichier de fonction *<project_root>\my\_first\_function\\_\_init\_\_.py* :

```python
from shared_code import my_first_helper_function #(absolute)
```

```python
import shared_code.my_second_helper_function #(absolute)
```

```python
from . import example #(relative)
```

> [!NOTE]
>  Le dossier *shared_code/* doit contenir un fichier \_\_init\_\_.py pour le marquer comme package Python lors de l’utilisation de la syntaxe d’importation absolue.

L’importation \_\_app\_\_ suivante et l’importation relative au-delà du niveau supérieur sont déconseillées, car elles ne sont pas prises en charge par le vérificateur de type statique ni par les infrastructures de test Python :

```python
from __app__.shared_code import my_first_helper_function #(deprecated __app__ import)
```

```python
from ..shared_code import my_first_helper_function #(deprecated beyond top-level relative import)
```

## <a name="triggers-and-inputs"></a>Déclencheurs et entrées

Les entrées sont réparties en deux catégories dans Azure Functions : l’entrée du déclencheur et l’autre entrée. Ces entrées sont différentes dans le fichier `function.json`, mais leur utilisation est identique dans le code Python.  Les chaînes de connexion ou les secrets pour les sources de déclencheur et d’entrée sont mappés aux valeurs dans le fichier `local.settings.json` lors d’une exécution locale, et aux paramètres d’application lors d’une exécution dans Azure.

L’exemple de code suivant illustre la différence entre les deux :

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quand la fonction est appelée, la requête HTTP est passée à la fonction dans `req`. Une entrée est récupérée du Stockage Blob Azure sur la base de l’_ID_ présent dans l’URL de la route, puis elle est mise à disposition comme `obj` dans le corps de la fonction.  Ici, le compte de stockage spécifié est la chaîne de connexion trouvée dans le paramètre d’application AzureWebJobsStorage, qui est le même compte de stockage utilisé par l’application de fonction.


## <a name="outputs"></a>Sorties

Les sorties peuvent être exprimées aussi bien dans une valeur de retour que dans des paramètres de sortie. S’il n’y a qu’une seule sortie, nous recommandons d’utiliser la valeur de retour. Lorsqu’il y en a plusieurs, il est nécessaire d’utiliser les paramètres de sortie.

Pour utiliser la valeur de retour d’une fonction comme valeur d’une liaison de sortie, la propriété `name` de la liaison doit être définie sur `$return` dans `function.json`.

Pour produire plusieurs sorties, utilisez la méthode `set()` fournie par l’interface [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out) afin d’affecter une valeur à la liaison. Par exemple, la fonction suivante peut placer un message dans une file d’attente tout en renvoyant une réponse HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Journalisation

L’accès à l’enregistreur d’événements du runtime d’Azure Functions se fait par l’intermédiaire du gestionnaire [`logging`](https://docs.python.org/3/library/logging.html#module-logging) racine dans l’application de fonction. Cet enregistreur d’événements, lié à Application Insights, permet de signaler les avertissements et les erreurs qui se produisent pendant l’exécution de la fonction.

L’exemple suivant enregistre un message d’informations lorsque la fonction est appelée avec un déclencheur HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

D’autres méthodes d’enregistrement permettent d’écrire dans la console à différents niveaux de trace :

| Méthode                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Écrit un message de niveau CRITIQUE sur l’enregistreur racine.  |
| **`error(_message_)`**   | Écrit un message de niveau ERREUR sur l’enregistreur racine.    |
| **`warning(_message_)`**    | Écrit un message de niveau AVERTISSEMENT sur l’enregistreur racine.  |
| **`info(_message_)`**    | Écrit un message de niveau INFO sur l’enregistreur racine.  |
| **`debug(_message_)`** | Écrit un message de niveau DÉBOGAGE sur l’enregistreur racine.  |

Pour en savoir plus sur la journalisation, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

### <a name="log-custom-telemetry"></a>Enregistrer une télémétrie personnalisée

Par défaut, le runtime Functions collecte les journaux et d’autres données de télémétrie générées par vos fonctions. Ces données de télémétrie finissent comme des traces dans les Insights d’applications. La télémétrie des requêtes et des dépendances pour certains services Azure est également collectée par défaut via des [déclencheurs et des liaisons](functions-triggers-bindings.md#supported-bindings). Pour collecter une requête et des données de télémétrie de dépendance personnalisées en dehors des liaisons, vous pouvez utiliser les [Extensions Python OpenCensus](https://github.com/census-ecosystem/opencensus-python-extensions-azure), qui envoient des données de télémétrie personnalisées à votre instance Application Insights. Vous trouverez une liste des extensions prises en charge dans le [référentiel OpenCensus](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib).

>[!NOTE]
>Pour utiliser les extensions Python OpenCensus, vous devez activer les [Extensions Python Worker](#python-worker-extensions) dans votre application de fonction en affectant la valeur `PYTHON_ENABLE_WORKER_EXTENSIONS` à `1` dans vos[paramètres d’application](functions-how-to-use-azure-function-app-settings.md#settings).


```
// requirements.txt
...
opencensus-extension-azure-functions
opencensus-ext-requests
```

```python
import json
import logging

import requests
from opencensus.extension.azure.functions import OpenCensusExtension
from opencensus.trace import config_integration

config_integration.trace_integrations(['requests'])

OpenCensusExtension.configure()

def main(req, context):
    logging.info('Executing HttpTrigger with OpenCensus extension')

    # You must use context.tracer to create spans
    with context.tracer.span("parent"):
        response = requests.get(url='http://example.com')

    return json.dumps({
        'method': req.method,
        'response': response.status_code,
        'ctx_func_name': context.function_name,
        'ctx_func_dir': context.function_directory,
        'ctx_invocation_id': context.invocation_id,
        'ctx_trace_context_Traceparent': context.trace_context.Traceparent,
        'ctx_trace_context_Tracestate': context.trace_context.Tracestate,
        'ctx_retry_context_RetryCount': context.retry_context.retry_count,
        'ctx_retry_context_MaxRetryCount': context.retry_context.max_retry_count,
    })
```

## <a name="http-trigger-and-bindings"></a>Déclencheur et liaisons HTTP

Le déclencheur HTTP est défini dans le fichier function.json. Le `name` de la liaison doit correspondre au paramètre nommé dans la fonction.
Dans les exemples précédents, un nom de liaison `req` est utilisé. Ce paramètre est un objet [HttpRequest], et un objet [HttpResponse] est retourné.

À partir de l’objet [HttpRequest], vous pouvez obtenir des en-têtes de demande, des paramètres de requête, des paramètres de route et le corps du message.

L’exemple suivant provient du [modèle de déclencheur HTTP pour Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Dans cette fonction, la valeur du paramètre de requête `name` est obtenue à partir du paramètre `params` de l’objet [HttpRequest]. Le corps du message encodé JSON est lu à l’aide de la méthode `get_json`.

De même, vous pouvez définir les `status_code` et `headers` pour le message de réponse dans l’objet [HttpResponse] retourné.

## <a name="scaling-and-performance"></a>Mise à l'échelle et niveau de performance

Pour connaître les meilleures pratiques en matière de mise à l’échelle et de niveau de performance pour les applications de fonction Python, consultez l’[article sur la mise à l’échelle et le niveau de performance Python](python-scale-performance-reference.md).

## <a name="context"></a>Context

Pour obtenir le contexte d’appel d’une fonction pendant l’exécution, ajoutez l’argument [`context`](/python/api/azure-functions/azure.functions.context) à sa signature.

Par exemple :

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

La classe [**Contexte**](/python/api/azure-functions/azure.functions.context) contient les attributs de chaîne suivants :

`function_directory` : répertoire dans lequel s’exécute la fonction.

`function_name` : nom de la fonction.

`invocation_id` : ID de l’appel de fonction en cours.

`trace_context` Contexte pour le suivi distribué. Pour plus d’informations, consultez [`Trace Context`](https://www.w3.org/TR/trace-context/).

`retry_context` Contexte pour les nouvelles tentatives d’appel de la fonction. Pour plus d’informations, consultez [`retry-policies`](./functions-bindings-errors.md#retry-policies-preview).

## <a name="global-variables"></a>Variables globales

L’état de votre application n’est pas systématiquement conservé en vue des exécutions ultérieures. Toutefois, le runtime Azure Functions réutilise souvent le même processus pour plusieurs exécutions de la même application. Pour mettre en cache les résultats d’un calcul coûteux, vous devez le déclarer en tant que variable globale.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variables d'environnement

Dans Functions, les [paramètres de l’application](functions-app-settings.md), par exemple, les chaînes de connexion de service, sont exposés en tant que variables d’environnement pendant l’exécution. Il existe deux méthodes principales pour accéder à ces paramètres dans votre code. 

| Méthode | Description |
| --- | --- |
| **`os.environ["myAppSetting"]`** | Tente d’accéder au paramètre d’application par nom de clé, en générant une erreur en cas d’échec.  |
| **`os.getenv("myAppSetting")`** | Tente d’accéder au paramètre d’application par nom de clé, en retournant « Null » en cas d’échec.  |

Ces deux méthodes vous obligent à déclarer `import os`.

L’exemple suivant utilise `os.environ["myAppSetting"]` pour obtenir le [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings), avec la clé nommée `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Pour le développement local, les paramètres d’application sont [conservés dans le fichier local.settings.json](functions-develop-local.md#local-settings-file).

## <a name="python-version"></a>Version Python

Azure Functions prend en charge les versions de Python suivantes :

| Version de Functions | Versions<sup>*</sup> de Python |
| ----- | ----- |
| 4.x | 3.9<br/> 3.8<br/>3.7 |
| 3.x | 3.9<br/> 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Distributions CPython officielles

Pour demander une version de Python particulière lorsque vous créez votre application de fonction dans Azure, utilisez l’option `--runtime-version` de la commande [`az functionapp create`](/cli/azure/functionapp#az_functionapp_create). La version du runtime Functions est définie par l’option `--functions-version`. La version Python est définie lors de la création de l’application de fonction et ne peut pas être modifiée.

Lors d’une exécution locale, le runtime utilise la version de Python disponible.

### <a name="changing-python-version"></a>Modification de la version de Python

Pour définir une application de fonction Python sur une version de langage spécifique, vous devez spécifier le langage ainsi que la version du langage dans le champ `LinuxFxVersion` de la configuration du site. Par exemple, pour modifier l’application Python et utiliser Python 3.8, affectez `linuxFxVersion` à `python|3.8`.

Pour en savoir plus sur la stratégie de support du runtime Azure Functions, veuillez consulter cet [article](./language-support-policy.md)

Pour afficher la liste complète des applications de fonctions des versions de Python prises en charge, veuillez vous reporter à cet [article](./supported-languages.md)



# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-linux)

Il est possible d’afficher et de définir `linuxFxVersion` dans Azure CLI.  

Avec Azure CLI, affichez la `linuxFxVersion` actuelle avec la commande [az functionapp config show](/cli/azure/functionapp/config).

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

Dans ce code, remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. 

Observez `linuxFxVersion` dans la sortie suivante, tronquée pour une meilleure lisibilité :

```output
{
  ...
  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

Vous pouvez mettre à jour le paramètre `linuxFxVersion` dans l’application de fonction avec la commande [az functionapp config set](/cli/azure/functionapp/config).

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Remplacez `<FUNCTION_APP>` par le nom de votre application de fonction. Remplacez également `<RESOURCE_GROUP>` par le nom du groupe de ressources de votre application de fonction. Remplacez également `<LINUX_FX_VERSION>` par la version de Python que vous souhaitez utiliser, précédée du préfixe `python|`  ex. `python|3.9`

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure/reference-index#az-login) pour vous connecter.

L’application de fonction redémarre une fois la modification apportée à la configuration du site.

--- 


## <a name="package-management"></a>Gestion des packages

Si vous développez en local avec Azure Functions Core Tools ou Visual Studio Code, ajoutez les noms et les versions des packages requis au fichier `requirements.txt` et installez-les avec `pip`.

Par exemple, le fichier de configuration requise suivant et la commande pip permettent d’installer le package `requests` à partir de PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publier sur Azure

Avant de procéder à la publication, vérifiez que toutes les dépendances disponibles publiquement sont listées dans le fichier requirements.txt, situé à la racine de votre répertoire de projet.

Les dossiers et fichiers projet qui sont exclus de la publication, y compris le dossier d’environnement virtuel, sont listés dans le fichier .funcignore.

Les actions de build prises en charge pour la publication de votre projet Python sur Azure sont au nombre de trois : build distante, build locale et builds utilisant des dépendances personnalisées.

Vous pouvez utiliser Azure Pipelines pour générer vos dépendances et publier en livraison continue (CD). Pour plus d’informations, consultez [Livraison continue à l’aide d’Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Build distante

Quand la build distante est utilisée, les dépendances restaurées sur le serveur et les dépendances natives correspondent à l’environnement de production. La taille du package de déploiement à charger s’en trouve réduite. Utilisez la build distante pour développer des applications Python sur Windows. Si votre projet a des dépendances personnalisées, vous pouvez [utiliser la build distante avec une URL d’index supplémentaire](#remote-build-with-extra-index-url).

les dépendances sont obtenues à distance en fonction du contenu du fichier requirements.txt. L’option [Build distante](functions-deployment-technologies.md#remote-build) est la méthode de génération recommandée. Par défaut, Azure Functions Core Tools demande une build distante lorsque vous utilisez la commande [`func azure functionapp publish`](functions-run-local.md#publish) suivante pour publier votre projet Python dans Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Veillez à remplacer `<APP_NAME>` par le nom de votre application de fonction dans Azure.

L’[extension Azure Functions pour Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) demande également une build distante par défaut.

### <a name="local-build"></a>Build locale

les dépendances sont obtenues localement en fonction du contenu du fichier requirements.txt. Vous pouvez éviter de créer une build distante en utilisant la commande [`func azure functionapp publish`](functions-run-local.md#publish) suivante pour publier avec une build locale.

```command
func azure functionapp publish <APP_NAME> --build local
```

Veillez à remplacer `<APP_NAME>` par le nom de votre application de fonction dans Azure.

Avec l’option `--build local`, les dépendances de projet sont lues à partir du fichier requirements.txt et les packages dépendants sont téléchargés et installés localement. Les fichiers de projet et les dépendances sont déployés de votre ordinateur local vers Azure, ce qui entraîne le chargement d’un package de déploiement plus important sur Azure. Si, pour une raison quelconque, les dépendances de votre fichier requirements.txt ne peuvent pas être acquises par Core Tools, vous devez utiliser l’option de dépendances personnalisées pour la publication.

Nous vous déconseillons d’utiliser des builds locales pour un développement local sur Windows.

### <a name="custom-dependencies"></a>Dépendances personnalisées

Si les dépendances de votre projet sont introuvables dans l’[index du package Python](https://pypi.org/), il existe deux façons de générer le projet. La méthode de build dépend de la façon dont vous générez le projet.

#### <a name="remote-build-with-extra-index-url"></a>Build distante avec l’URL d’index supplémentaire

Si vos packages sont disponibles à partir d’un index de package personnalisé accessible, utilisez une build distante. Avant la publication, veillez à [créer un paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) nommé `PIP_EXTRA_INDEX_URL`. La valeur de ce paramètre est l’URL de votre index de package personnalisé. L’utilisation de ce paramètre indique à la build distante d’exécuter `pip install` en utilisant l’option `--extra-index-url`. Pour plus d’informations, consultez la [documentation Python pip install](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Vous pouvez aussi utiliser des informations d’authentification de base avec vos URL d’index de package supplémentaires. Pour plus d’informations, consultez [Basic authentication credentials](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) dans la documentation Python.

#### <a name="install-local-packages"></a>Installer des packages locaux

Si votre projet utilise des packages non disponibles publiquement pour nos outils, vous pouvez les rendre disponibles pour votre application en les plaçant dans le répertoire \_\_app\_\_/.python_packages directory. Avant d’effectuer la publication, exécutez la commande suivante pour installer les dépendances localement :

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Si vous utilisez des dépendances personnalisées, vous devez utiliser l’option de publication `--no-build`, car vous avez déjà installé les dépendances dans le dossier du projet.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Veillez à remplacer `<APP_NAME>` par le nom de votre application de fonction dans Azure.

## <a name="unit-testing"></a>Tests unitaires

Les fonctions écrites en Python peuvent être testées comme tout autre code Python à l’aide des frameworks de test standard. Pour la plupart des liaisons, il est possible de créer un objet d’entrée factice en créant une instance d’une classe appropriée à partir du package `azure.functions`. Comme le package [`azure.functions`](https://pypi.org/project/azure-functions/) n’est pas immédiatement disponible, assurez-vous de l’installer via votre fichier `requirements.txt`, comme décrit dans la section [gestion des packages](#package-management) ci-dessus.

Prenez *my_second_function* comme exemple. Voici un test fictif d’une fonction déclenchée par HTTP :

Tout d’abord, nous devons créer le fichier *<project_root>/my_second_function/function.json* et définir cette fonction en tant que déclencheur http.

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "main",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

À présent, nous pouvons implémenter *my_second_function* et *shared_code.my_second_helper_function*.

```python
# <project_root>/my_second_function/__init__.py
import azure.functions as func
import logging

# Use absolute import to resolve shared_code modules
from shared_code import my_second_helper_function

# Define an http trigger which accepts ?value=<int> query parameter
# Double the value and return the result in HttpResponse
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Executing my_second_function.')

    initial_value: int = int(req.params.get('value'))
    doubled_value: int = my_second_helper_function.double(initial_value)

    return func.HttpResponse(
      body=f"{initial_value} * 2 = {doubled_value}",
      status_code=200
    )
```

```python
# <project_root>/shared_code/__init__.py
# Empty __init__.py file marks shared_code folder as a Python package
```

```python
# <project_root>/shared_code/my_second_helper_function.py

def double(value: int) -> int:
  return value * 2
```

Nous pouvons commencer à écrire des cas de test pour notre déclencheur http.

```python
# <project_root>/tests/test_my_second_function.py
import unittest

import azure.functions as func
from my_second_function import main

class TestFunction(unittest.TestCase):
    def test_my_second_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/my_second_function',
            params={'value': '21'})

        # Call the function.
        resp = main(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'21 * 2 = 42',
        )
```

Dans votre environnement virtuel Python `.venv`, installez votre infrastructure de test Python favorite, telle que `pip install pytest`. Exécutez ensuite `pytest tests` pour vérifier le résultat du test.

## <a name="temporary-files"></a>Fichiers temporaires

La méthode `tempfile.gettempdir()` retourne un dossier temporaire, `/tmp` sous Linux. Votre application peut utiliser ce répertoire pour stocker les fichiers temporaires générés et utilisés par vos fonctions lors de l’exécution.

> [!IMPORTANT]
> Il n’est pas garanti que les fichiers écrits dans le répertoire temporaire persistent d’un appel à l’autre. Lors du scale-out, les fichiers temporaires ne sont pas partagés entre les instances.

L’exemple suivant crée un fichier temporaire nommé dans le répertoire temporaire (`/tmp`) :

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Nous vous recommandons de conserver vos tests dans un dossier distinct du dossier de projet. Cela vous évite de déployer du code de test avec votre application.

## <a name="preinstalled-libraries"></a>Bibliothèques préinstallées

Le runtime Functions Python s’accompagne de quelques bibliothèques.

### <a name="python-standard-library"></a>Bibliothèque standard Python

La bibliothèque standard Python contient une liste de modules Python intégrés qui sont fournis avec chaque distribution Python. La plupart de ces bibliothèques vous permettent d’accéder à des fonctionnalités système, comme l’E/S de fichiers. Sur les systèmes Windows, ces bibliothèques sont installées avec Python. Sur les systèmes basés sur Unix, ils sont fournis par des collections de packages.

Pour consulter la liste de ces bibliothèques dans le détail, référez-vous aux liens ci-dessous :

* [Bibliothèque standard Python 3.6](https://docs.python.org/3.6/library/)
* [Bibliothèque standard Python 3.7](https://docs.python.org/3.7/library/)
* [Bibliothèque standard Python 3.8](https://docs.python.org/3.8/library/)
* [Bibliothèque standard Python 3.9](https://docs.python.org/3.9/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Dépendances du Worker Python Azure Functions

Le Worker Python Functions a besoin d’un ensemble spécifique de bibliothèques. Vous pouvez aussi utiliser ces bibliothèques dans vos fonctions, mais elles ne font pas partie intégrante du standard Python. Si vos fonctions reposent sur une de ces bibliothèques, il se peut que votre code n’y ait pas accès quand il s’exécute en dehors d’Azure Functions. Vous trouverez une liste détaillée des dépendances dans la section **install\_requires** du fichier [setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282).

> [!NOTE]
> Si le fichier requirements.txt de votre application de fonction contient une entrée `azure-functions-worker`, supprimez-la. Le worker functions est géré automatiquement par la Plateforme Azure Functions et nous le mettons régulièrement à jour avec de nouvelles fonctionnalités et de correctifs de bogues. L’installation manuelle d’une ancienne version du worker dans requirements.txt peut entraîner des problèmes inattendus.

> [!NOTE]
>  Si votre package contient certaines bibliothèques susceptibles d’entrer en conflit avec les dépendances du Worker (par exemple, protobuf, tensorflow, grpcio), veuillez configure [`PYTHON_ISOLATE_WORKER_DEPENDENCIES`](functions-app-settings.md#python_isolate_worker_dependencies-preview) sur `1` dans les paramètres de l’application pour empêcher votre application de faire référence aux dépendances du Worker. Cette fonctionnalité est en préversion.

### <a name="azure-functions-python-library"></a>Bibliothèque Python Azure Functions

Chaque mise à jour du Worker Python comprend une nouvelle version de la [bibliothèque Python Azure Functions (azure.functions)](https://github.com/Azure/azure-functions-python-library). Cette approche facilite la mise à jour continue de vos applications de fonction Python, car chaque mise à jour offre une compatibilité descendante. La liste des versions de cette bibliothèque se trouve dans [azure-functions PyPi](https://pypi.org/project/azure-functions/#history).

La version de la bibliothèque runtime est corrigée par Azure et ne peut pas être remplacée par requirements.txt. L’entrée `azure-functions` dans requirements.txt est réservée au linting et à la sensibilisation des clients.

Utilisez le code suivant pour effectuer le suivi de la version effective de la bibliothèque Functions Python dans votre runtime :

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Bibliothèques système runtime

Pour obtenir la liste des bibliothèques système préinstallées dans les images Docker du Worker Python, référez-vous aux liens ci-dessous :

|  Runtime Functions  | Version de Debian | Versions de Python |
|------------|------------|------------|
| Version 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Version 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile)<br/> [Python 3.9](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python39/python39.Dockerfile)|

## <a name="python-worker-extensions"></a>Extensions de Worker Python  

Le processus Worker Python qui s’exécute dans Azure Functions vous permet d’intégrer des bibliothèques tierces dans votre application de fonction. Ces bibliothèques d’extensions jouent le rôle d’intergiciel qui peut injecter des opérations spécifiques pendant le cycle de vie de l’exécution de votre fonction. 

Les extensions sont importées dans votre code de fonction de la même manière qu’un module de bibliothèque Python standard. Les extensions sont exécutées en fonction des étendues suivantes : 

| Étendue | Description |
| --- | --- |
| **Au niveau de l'application** | Lorsqu’elle est importée dans un déclencheur de fonction, l’extension s’applique à chaque exécution de fonction dans l’application. |
| **Au niveau de la fonction** | L’exécution est limitée uniquement au déclencheur de fonction spécifique dans lequel elle est importée. |

Passez en revue les informations relatives à une extension donnée pour en savoir plus sur l’étendue dans laquelle l’extension s’exécute. 

Les extensions implémentent une interface d’extension de Worker Python qui permet au processus Worker Python d’appeler le code d’extension pendant le cycle de vie de l’exécution de la fonction. Pour plus d’informations, consultez [Création d’extensions](#creating-extensions).

### <a name="using-extensions"></a>Utilisation d’extensions 

Vous pouvez utiliser une bibliothèque d’extensions de Worker Python dans vos fonctions Python en suivant ces étapes de base :

1. Ajoutez le package d’extension dans le fichier requirements.txt de votre projet.
1. Installez la bibliothèque dans votre application.
1. Ajoutez le paramètre d’application `PYTHON_ENABLE_WORKER_EXTENSIONS` :
    + Localement : Ajoutez `"PYTHON_ENABLE_WORKER_EXTENSIONS": "1"` dans la section `Values` de votre [fichier local.settings.json](functions-develop-local.md#local-settings-file)
    + Azure : Ajoutez `PYTHON_ENABLE_WORKER_EXTENSIONS=1` à vos [paramètres d’application](functions-how-to-use-azure-function-app-settings.md#settings).
1. Importez le module d’extension dans votre déclencheur de fonction. 
1. Configurez l’instance d’extension, le cas échéant. Les exigences de configuration doivent être mentionnées dans la documentation de l’extension. 

> [!IMPORTANT]
> Les bibliothèques d’extension de Worker Python tierces ne sont pas prises en charge ni garanties par Microsoft. Vous devez vous assurer que toutes les extensions que vous utilisez dans votre application de fonction sont dignes de confiance, et vous assumez pleinement le risque lié à l’utilisation d’une extension malveillante ou mal écrite. 

Les tiers doivent fournir une documentation spécifique sur la façon d’installer et de consommer leur extension spécifique dans votre application de fonction. Pour obtenir un exemple de base de la consommation d’une extension, consultez [Consommation de votre extension](develop-python-worker-extensions.md#consume-your-extension-locally). 

Voici des exemples d’utilisation d’extensions dans une application de fonction, par étendue :

# <a name="application-level"></a>[Au niveau de l'application](#tab/application-level)

```python
# <project_root>/requirements.txt
application-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from application_level_extension import AppExtension
AppExtension.configure(key=value)

def main(req, context):
  # Use context.app_ext_attributes here
```
# <a name="function-level"></a>[Au niveau de la fonction](#tab/function-level)
```python
# <project_root>/requirements.txt
function-level-extension==1.0.0
```

```python
# <project_root>/Trigger/__init__.py

from function_level_extension import FuncExtension
func_ext_instance = FuncExtension(__file__)

def main(req, context):
  # Use func_ext_instance.attributes here
```
---

### <a name="creating-extensions"></a>Création d’extensions 

Les extensions sont créées par des développeurs de bibliothèques tierces qui ont créé des fonctionnalités qui peuvent être intégrées dans Azure Functions.  Un développeur d’extensions conçoit, implémente et publie des packages Python qui contiennent une logique personnalisée conçue spécifiquement pour être exécutée dans le contexte d’une exécution de fonction. Ces extensions peuvent être publiées dans le registre PyPI ou dans des référentiels GitHub.

Pour savoir comment créer, empaqueter, publier et consommer un package d’extension de Worker Python, consultez [Développer des extensions de Worker Python pour Azure Functions](develop-python-worker-extensions.md).

#### <a name="application-level-extensions"></a>Extensions au niveau de l’application

Une extension héritée de [`AppExtensionBase`](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py) s’exécute dans une étendue d’_application_. 

`AppExtensionBase` expose les méthodes de classe abstraite suivantes que vous pouvez implémenter :

| Méthode | Description |
| --- | --- |
| **`init`** | Appelée après l’importation de l’extension. |
| **`configure`** | Appelée à partir du code de fonction lorsque cela est nécessaire pour configurer l’extension. |
| **`post_function_load_app_level`** | Appelée juste après le chargement de la fonction. Le nom de la fonction et le répertoire de la fonction sont transmis à l’extension. Gardez à l’esprit que le répertoire de la fonction est en lecture seule et que toute tentative d’écriture dans le fichier local de ce répertoire échoue. |
| **`pre_invocation_app_level`** | Appelée juste avant le déclenchement de la fonction. Le contexte de la fonction et les arguments d’appel de la fonction sont transmis à l’extension. Vous pouvez généralement transmettre d’autres attributs dans l’objet de contexte pour que le code de la fonction les consomme. |
| **`post_invocation_app_level`** | Appelée juste après la fin de l’exécution de la fonction. Le contexte de la fonction, les arguments d’appel de la fonction et l’objet de retour d’appel sont transmis à l’extension. Cette implémentation est un bon endroit pour vérifier si l’exécution des crochets du cycle de vie a réussi. |

#### <a name="function-level-extensions"></a>Extensions au niveau de la fonction

Une extension qui hérite de [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) s’exécute dans un déclencheur de fonction spécifique. 

`FuncExtensionBase` expose les méthodes de classe abstraite suivantes que vous pouvez implémenter :

| Méthode | Description |
| --- | --- |
| **`__init__`** | Cette méthode est le constructeur de l’extension. Elle est appelée lorsqu’une instance d’extension est initialisée dans une fonction spécifique. Lors de l’implémentation de cette méthode abstraite, vous souhaiterez peut-être accepter un paramètre `filename` et le transmettre à la méthode `super().__init__(filename)` du parent pour une inscription appropriée de l’extension. |
| **`post_function_load`** | Appelée juste après le chargement de la fonction. Le nom de la fonction et le répertoire de la fonction sont transmis à l’extension. Gardez à l’esprit que le répertoire de la fonction est en lecture seule et que toute tentative d’écriture dans le fichier local de ce répertoire échoue. |
| **`pre_invocation`** | Appelée juste avant le déclenchement de la fonction. Le contexte de la fonction et les arguments d’appel de la fonction sont transmis à l’extension. Vous pouvez généralement transmettre d’autres attributs dans l’objet de contexte pour que le code de la fonction les consomme. |
| **`post_invocation`** | Appelée juste après la fin de l’exécution de la fonction. Le contexte de la fonction, les arguments d’appel de la fonction et l’objet de retour d’appel sont transmis à l’extension. Cette implémentation est un bon endroit pour vérifier si l’exécution des crochets du cycle de vie a réussi. |

## <a name="cross-origin-resource-sharing"></a>Partage de ressources cross-origin

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS est entièrement pris en charge pour les applications de fonction Python.

## <a name="known-issues-and-faq"></a>Problèmes connus et FAQ

Voici la liste des guides de résolution des problèmes courants :

* [ModuleNotFoundError et ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Impossible d’importer « cygrpc »](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Tous les problèmes connus et les demandes de fonctionnalités sont listés dans [Problèmes GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Si vous rencontrez un problème qui n’apparaît pas dans GitHub, soumettez un nouveau problème en incluant une description détaillée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Documentation sur l’API du package Azure Functions](/python/api/azure-functions/azure.functions)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
* [Liaisons de Stockage Blob](functions-bindings-storage-blob.md)
* [Liaisons HTTP et Webhook](functions-bindings-http-webhook.md)
* [Liaisons de Stockage File d’attente](functions-bindings-storage-queue.md)
* [Déclencheur de minuteur](functions-bindings-timer.md)

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/python-functions-ref-survey)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse
