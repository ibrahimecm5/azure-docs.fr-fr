---
title: Bien démarrer avec la traduction de documents
description: Comment créer un service Traduction de documentation avec les plateformes et les langages de programmation C#, Go, Java, Node.js ou Python
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 051e218b8c0bce8249c6fbe3dd785d797130b61f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062725"
---
# <a name="get-started-with-document-translation"></a>Bien démarrer avec la traduction de documents

 Dans cet article, vous allez apprendre à utiliser la Traduction de documentation avec les méthodes de l’API REST HTTP. La Traduction de documentation est une fonctionnalité cloud du service [Azure Translator](../translator-overview.md).  L’API Traduction de documentation permet de traduire des documents entiers tout en conservant la structure et la mise en forme du texte du document source.

## <a name="prerequisites"></a>Prérequis

> [!NOTE]
>
> 1. En général, quand vous créez une ressource Cognitive Services dans le portail Azure, vous avez la possibilité de créer une clé d’abonnement multiservice ou une clé d’abonnement monoservice. Toutefois, la Traduction de documentation est actuellement prise en charge dans la ressource Translator (monoservice) uniquement, et n’est **pas** incluse dans la ressource Cognitive Services (multiservice).
> 2. La Traduction de documentation est disponible **uniquement** dans le plan de service Standard S1 (paiement à l’utilisation) ou dans le plan de remise sur la quantité D3. _Voir_ [Tarification de Cognitive Services - Traducteur](https://azure.microsoft.com/pricing/details/cognitive-services/translator/).
>

Avant de commencer, vérifiez que vous disposez des éléments suivants :

* Un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

* Une [**ressource Translator de service unique**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (et **pas** une ressource Cognitive Services multiservice).

* Un [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Vous allez créer des conteneurs pour stocker et organiser vos données de blob dans votre compte de stockage.

## <a name="custom-domain-name-and-subscription-key"></a>Nom de domaine personnalisé et votre clé d’abonnement

> [!IMPORTANT]
>
> * **Toutes les requêtes d’API adressées au service Traduction de documentation nécessitent un point de terminaison de domaine personnalisé**.
> * Vous n’utiliserez pas le point de terminaison se trouvant dans la page _Clés et point de terminaison_ de votre ressource du portail Azure, ni le point de terminaison du traducteur global (`api.cognitive.microsofttranslator.com`) pour soumettre des requêtes HTTP à la Traduction de documentation.

### <a name="what-is-the-custom-domain-endpoint"></a>Qu’est-ce que le point de terminaison de domaine personnalisé ?

Le point de terminaison de domaine personnalisé est une URL mise en forme avec votre nom de ressource, votre nom d’hôte et les sous-répertoires de Translator :

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0
```

### <a name="find-your-custom-domain-name"></a>Rechercher votre nom de domaine personnalisé

Le paramètre **NAME-OF-YOUR-RESOURCE** (également appelé *nom de domaine personnalisé*) est la valeur que vous avez entrée dans le champ **Nom** quand vous avez créé votre ressource Translator.

:::image type="content" source="../media/instance-details.png" alt-text="Image du champ Nom dans Créer une ressource sous Détails instantanés dans le portail Azure.":::

### <a name="get-your-subscription-key"></a>Obtenir votre clé d’abonnement

Les requêtes adressées au service Translator nécessitent une clé en lecture seule pour l’authentification de l’accès.

1. Si vous avez créé une ressource, après son déploiement, sélectionnez **Accéder à la ressource**. Si vous disposez déjà d’une ressource de Traduction de documentation, accédez directement à la page de votre ressource.
1. Dans le rail de gauche, sous *Gestion des ressources*, sélectionnez **Clés et point de terminaison**.
1. Copiez et collez votre clé d’abonnement dans un endroit pratique, par exemple le *Bloc-notes Microsoft*.
1. Vous allez la coller dans le code ci-dessous pour authentifier votre requête auprès du service Traduction de documentation.

:::image type="content" source="../media/translator-keys.png" alt-text="Image du champ Récupérer votre clé d’abonnement dans le portail Azure.":::

## <a name="create-azure-blob-storage-containers"></a>Créer des conteneurs de stockage d’objets blob Azure

Vous devez [**créer des conteneurs**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) dans votre [**compte de stockage Blob Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) pour les fichiers sources et cibles.

* **Conteneur source**. Ce conteneur est l’emplacement où vous chargez vos fichiers pour la traduction (obligatoire).
* **Conteneur cible**. Ce conteneur est l’emplacement où vos fichiers traduits seront stockés (obligatoire).

> [!NOTE]
> La traduction de documents prend en charge les glossaires en tant qu’objets BLOB dans les conteneurs cibles (pas les conteneurs de Glossaire distincts). Si vous souhaitez inclure un glossaire personnalisé, ajoutez-le au conteneur cible et incluez ` glossaryUrl` avec la requête.  Si la paire de langues de traduction n’est pas présente dans le glossaire, elle ne sera pas appliquée. *Voir* [Traduisez des documents à l’aide d’un glossaire personnalisé](#translate-documents-using-a-custom-glossary)

### <a name="create-sas-access-tokens-for-document-translation"></a>**Créer des jetons d’accès SAS pour la Traduction de documentation**

`sourceUrl`, `targetUrl` et `glossaryUrl` (facultatif) doivent inclure un jeton de signature d’accès partagé (SAS), ajouté comme chaîne de requête. Le jeton peut être attribué à votre conteneur ou à des blobs spécifiques. *Consultez* [ **Créer des jetons SAS pour le processus de Traduction de documentation**](create-sas-tokens.md).

* Votre blob ou conteneur **source** doit disposer d’un accès **lecture**  et d’un accès **liste** désignés.
* Votre blob ou conteneur **cible** doit disposer d’un accès **écriture** et d’un accès **liste** désignés.
* Votre blob **Glossaire** doit disposer d’un accès **lecture**  et d’un accès **liste** désignés.

> [!TIP]
>
> * Si vous traduisez **plusieurs** fichiers (blobs) dans une opération, **déléguez l’accès SAS au niveau du conteneur**.
> * Si vous traduisez un **seul** fichier (blob) dans une opération, **déléguez l’accès SAS au niveau du blob**.
>

## <a name="document-translation-http-requests"></a>Traduction de documentation : requêtes HTTP

Une demande de Traduction de documentation par lot est soumise à votre point de terminaison de service Translator par le biais d’une requête POST. En cas de réussite, la méthode POST retourne un code de réponse `202 Accepted` et la demande de lot est créée par le service.

### <a name="http-headers"></a>En-têtes HTTP

Les en-têtes suivants sont inclus avec chaque demande d’API du Traducteur de documentation :

|En-tête HTTP|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**Obligatoire** : la valeur est la clé d’abonnement Azure de votre ressource Translator ou Cognitive Services.|
|Content-Type|**Obligatoire** : spécifie le type de contenu de la charge utile. Les valeurs acceptées sont application/json ou charset=UTF-8.|
|Content-Length|**Obligatoire** : longueur du corps de la demande.|

### <a name="post-request-body-properties"></a>Propriétés du corps de la requête POST

* L’URL de la requête POST est POST `https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches`
* Le corps de la requête POST est un objet JSON nommé `inputs`.
* L’objet `inputs` contient à la fois les adresses de conteneur `sourceURL` et `targetURL` pour vos paires de langue source et cible
* Les champs `prefix` et `suffix` (facultatifs) sont utilisés pour filtrer les documents présents dans le conteneur, dont les dossiers.
* Une valeur du champ `glossaries` (facultatif) est appliquée quand le document est en cours de traduction.
* La valeur `targetUrl` de chaque langue cible doit être unique.

>[!NOTE]
> Si un fichier portant le même nom existe déjà dans la destination, le travail se solde par un échec.

<!-- markdownlint-disable MD024 -->
### <a name="translate-all-documents-in-a-container"></a>Traduire tous les documents dans un conteneur

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

### <a name="translate-a-specific-document-in-a-container"></a>Traduire un document spécifique dans un conteneur

* Vérifiez que vous avez spécifié "storageType": "File"
* Vérifiez que vous avez créé une URL source et un jeton SAS pour l’objet blob/document spécifique (et non pour le conteneur)
* Vérifiez que vous avez spécifié le nom de fichier cible dans le cadre de l’URL cible, bien que le jeton SAS soit toujours pour le conteneur.
* L’exemple de demande ci-dessous montre un document unique qui est traduit en deux langues cibles

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="translate-documents-using-a-custom-glossary"></a>Traduire des documents à l’aide d’un glossaire personnalisé

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://myblob.blob.core.windows.net/source",
                "filter": {
                    "prefix": "myfolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://myblob.blob.core.windows.net/target",
                    "language": "es",
                    "glossaries": [
                        {
                            "glossaryUrl": "https:// myblob.blob.core.windows.net/glossary/en-es.xlf",
                            "format": "xliff"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="use-code-to-submit-document-translation-requests"></a>Utiliser du code pour soumettre des demandes de traduction de documents

### <a name="set-up-your-coding-platform"></a>Configurer votre plateforme de programmation

### <a name="c"></a>[C#](#tab/csharp)

* Créez un projet.
* Remplacez Program.cs par le code C# indiqué ci-dessous.
* Définissez vos valeurs de point de terminaison, de clé d’abonnement et d’URL de conteneur dans Program.cs.
* Pour traiter des données JSON, ajoutez le [package Newtonsoft.Json en utilisant l’interface CLI .NET](https://www.nuget.org/packages/Newtonsoft.Json/).
* Exécutez le programme à partir du répertoire du projet.

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

* Créez un projet Node.js.
* Installez la bibliothèque Axios avec `npm i axios`.
* Copiez et collez le code ci-dessous dans votre projet.
* Définissez vos valeurs de point de terminaison, de clé d’abonnement et d’URL de conteneur.
* Exécuter le programme

### <a name="python"></a>[Python](#tab/python)

* Créez un projet.
* Copiez et collez le code de l’un des exemples dans votre projet.
* Définissez vos valeurs de point de terminaison, de clé d’abonnement et d’URL de conteneur.
* Exécutez le programme. Par exemple : `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Créez un répertoire de travail pour votre projet. Par exemple :

```powershell
mkdir sample-project
```

* Dans le répertoire de votre projet, créez la structure de sous-répertoire suivante :

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**REMARQUE** : Les fichiers sources Java (par exemple, _sample.java_) résident dans src/main/**java**.

* Dans votre répertoire racine (par exemple, *sample-projectroject*), initialisez votre projet avec Gradle :

```powershell
gradle init --type basic
```

* Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

* Mettez à jour le fichier `build.gradle.kts`. N’oubliez pas que vous devez mettre à jour votre `mainClassName` en fonction de l’exemple :

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Créez un fichier Java dans le répertoire **java**, puis copiez/collez le code de l’exemple fourni. N’oubliez pas d’ajouter votre clé d’abonnement et votre point de terminaison.

* **Générez et exécutez l’exemple à partir du répertoire racine** :

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)

* Créez un projet Go.
* Ajoutez le code ci-dessous.
* Définissez vos valeurs de point de terminaison, de clé d’abonnement et d’URL de conteneur.
* Enregistrez le fichier avec une extension .go.
* Ouvrez une invite de commandes sur un ordinateur sur lequel Go est installé.
* Générez le fichier. Par exemple : « go build example-code.go ».
* Exécutez le fichier, par exemple : « example-code ».

 ---

> [!IMPORTANT]
>
> Pour les exemples de code ci-dessous, vous allez coder en dur votre clé et votre point de terminaison aux endroits indiqués. N’oubliez pas de supprimer la clé de votre code quand vous avez terminé et ne la postez jamais publiquement.  Pour savoir comment stocker vos informations d’identification et y accéder de manière sécurisée, consultez [Sécurité Azure Cognitive Services](../../cognitive-services-security.md?tabs=command-line%2ccsharp).
>
> Vous devrez peut-être mettre à jour les champs suivants, en fonction de l’opération :
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id` (ID de tâche)
>>

#### <a name="locating--the-id-value"></a>Recherche de la valeur `id`

* L’`id` de tâche se trouve dans la valeur d’URL `Operation-Location` de l’en-tête de réponse de la méthode POST. Le dernier paramètre de l’URL est l’ **`id`** de tâche de l’opération :

|**En-tête de réponse**|**URL de résultat**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NOM-DE-VOTRE-RESSOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* Vous pouvez également utiliser une requête **GET Jobs** pour récupérer un `id` de tâche de Traduction de documentation.

>

 ## <a name="translate-documents"></a>Traduire des documents

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;


    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");

        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {

                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;

                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:");
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="get-file-formats"></a>Obtenir des formats de fichiers

Récupérez la liste des formats de fichiers pris en charge. En cas de réussite, cette méthode retourne un code de réponse `200 OK`.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-job-status"></a>Obtenir l’état de la tâche

Obtenez l’état actuel d’une seule tâche et un récapitulatif de toutes les tâches d’une demande de Traduction de documentation. En cas de réussite, cette méthode retourne un code de réponse `200 OK`.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-document-status"></a>Obtenir l’état d’un document

### <a name="brief-overview"></a>Petite vue d’ensemble

Récupérez l’état d’un document spécifique dans une demande de Traduction de documentation. En cas de réussite, cette méthode retourne un code de réponse `200 OK`.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="delete-job"></a>Supprimer le travail

### <a name="brief-overview"></a>Petite vue d’ensemble

Annulez la tâche en cours de traitement ou en file d’attente. Seuls les documents pour lesquels la traduction n’a pas commencé seront annulés.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.JS](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Limites de contenu

Le tableau ci-dessous liste les limites applicables aux données que vous envoyez au service Traduction de documentation.

|Attribut | Limite|
|---|---|
|Taille du document| ≤ 40 Mo |
|Nombre total de fichiers|≤ 1 000 |
|Taille totale du contenu dans un lot | ≤ 250 Mo|
|Nombre de langues cibles dans un lot| ≤ 10 Mo |
|Taille du fichier de mémoire de traduction| ≤ 10 Mo|

La Traduction de documentation ne peut pas être utilisée pour traduire des documents sécurisés, tels que ceux définis avec un mot de passe chiffré ou avec un accès restreint pour copier le contenu.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="common-http-status-codes"></a>Codes d'état HTTP courants

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| 200 | OK | La demande a abouti. |
| 400 | Demande incorrecte | Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long. |
| 401 | Non autorisé | La demande n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton est valide et dans la région appropriée. Lors de la gestion de votre abonnement sur le portail Azure, assurez-vous que vous utilisez la ressource à service unique **Traducteur** et _non_ la ressource multiservice de **Cognitive Services**.
| 429 | Trop de demandes | Vous avez dépassé le quota ou le taux de requêtes autorisé pour votre abonnement. |
| 502 | Passerelle incorrecte    | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides. |

## <a name="learn-more"></a>En savoir plus

* [Informations de référence sur l’API Translator v3](../reference/v3-0-reference.md)
* [Prise en charge linguistique](../language-support.md)
* [Abonnements dans Gestion des API Azure](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un système de langue personnalisé avec Custom Translator](../custom-translator/overview.md)
>
>
