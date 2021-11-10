---
title: Installer et exécuter des conteneurs Docker pour l’API Translator
titleSuffix: Azure Cognitive Services
description: Utilisez un conteneur Docker pour l’API Translator afin de traduire du texte.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 10/14/2021
ms.author: lajanuar
recommendations: false
keywords: local, Docker, conteneur, identifier
ms.openlocfilehash: dbee487212f56eb5c99d3cc785cf8a2205280fcf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426142"
---
# <a name="install-and-run-translator-containers-preview"></a>Installer et exécuter des conteneurs Translator (préversion)

Les conteneurs vous permettent d’exécuter plusieurs des fonctionnalités du service Translator dans votre propre environnement. Les conteneurs conviennent particulièrement bien à certaines exigences de sécurité et de gouvernance des données. Cet article explique comment télécharger, installer et exécuter un conteneur Translator.

Les conteneurs Translator vous permettent de créer une architecture d’application Translator optimisée pour tirer parti des fonctionnalités robustes du cloud et de la localité en périphérie.

Lors de l’utilisation de conteneurs Translator, consultez la liste des [langues prises en charge](../language-support.md). 

> [!IMPORTANT]
>
> * Le conteneur Translator est disponible en préversion contrôlée. Pour l’utiliser, vous devez envoyer une demande en ligne et obtenir une approbation. Pour plus d’informations, consultez la section [Demande d’approbation pour l’exécution du conteneur](#request-approval-to-run-container), plus loin dans cet article.
> * Le conteneur Translator prend en charge des fonctionnalités limitées par rapport aux offres cloud.  Pour plus d’informations, consultez **Conteneur : Translator**.

<!-- markdownlint-disable MD033 -->

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous avez besoin d’un [**compte Azure**](https://azure.microsoft.com/free/cognitive-services/) actif.  Si vous n’en avez pas, vous pouvez [**créer un compte gratuit**](https://azure.microsoft.com/free/).

Vous devez également disposer des éléments suivants :

| Obligatoire | Objectif |
|--|--|
| Bonne connaissance de Docker | <ul><li>Vous devez comprendre les concepts de base liés à Docker (comme les registres, les référentiels, les conteneurs et les images conteneurs), et connaître les principaux [termes et commandes](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology) utilisés pour `docker`.</li></ul> |
| Moteur Docker | <ul><li>Vous avez besoin d’un moteur Docker installé sur un [ordinateur hôte](#host-computer). Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) et [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).</li><li> Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. </li><li> Sur **Windows**, vous devez également configurer Docker pour la prise en charge des conteneurs **Linux**.</li></ul> |
| Ressource Translator | <ul><li>Une ressource Azure [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) avec une région autre que « global », avec une clé API et un URI de point de terminaison associés. Les deux valeurs sont requises pour démarrer le conteneur ; elles sont indiquées dans la page de vue d’ensemble de la ressource.</li></ul>|
|||

|Facultatif|Objectif|
|---------|----------|
|Azure CLI (interface de ligne de commande) |<ul><li> L’interface [Azure CLI](/cli/azure/install-azure-cli) vous permet d’utiliser un ensemble de commandes en ligne pour créer et gérer des ressources Azure. Elle peut s’installer dans les environnements Windows, macOS et Linux, et s’exécuter dans un conteneur Docker et Azure Cloud Shell.</li></ul> |
|||

## <a name="required-elements"></a>Éléments obligatoires

Tous les conteneurs Cognitive Services nécessitent trois éléments principaux :

* Un **paramètre d’acceptation du CLUF**. Un contrat de licence utilisateur final (CLUF) défini avec la valeur `Eula=accept`.

* Une **clé API** et une **URL de point de terminaison**.  La clé API sert à démarrer le conteneur. Vous pouvez obtenir les valeurs de la clé API et de l’URL de point de terminaison en accédant à la page _Clés et point de terminaison_ de la ressource Translator, puis en sélectionnant l’icône `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>.

> [!IMPORTANT]
>
> * Vous utilisez des clés d’abonnement pour accéder à votre API Cognitive Service. Ne partagez pas vos clés. Stockez-les en toute sécurité, par exemple, à l’aide de Azure Key Vault. Nous vous recommandons également de régénérer ces clés régulièrement. Une seule clé est nécessaire pour effectuer un appel d’API. Lors de la régénération de la première clé, vous pouvez utiliser la deuxième clé pour un accès continu au service.

## <a name="host-computer"></a>Ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

## <a name="container-requirements-and-recommendations"></a>Exigences et suggestions relatives au conteneur

Le tableau suivant décrit les spécifications minimales et recommandées pour les conteneurs Translator. Au moins 2 gigaoctets (Go) de mémoire sont requis, et chaque CPU doit être cadencé à au moins 2,6 gigahertz (GHz). Plus 8 gigaoctets (Go) de mémoire à allouer pour chaque conteneur Translator. Le tableau suivant décrit l’allocation de ressources minimale et recommandée pour chaque conteneur Translator.

| Conteneur | Minimum |Recommandé | Paire de langues |
|-----------|---------|---------------|----------------------|
| Translator connecté |2 cœurs, 2 Go de mémoire |4 cœurs, 8 Go de mémoire | 4 |
|||

Pour chaque paire de langues, il est recommandé d’avoir 2 Go de mémoire. Par défaut, le conteneur Translator hors connexion a quatre paires de langues. Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

> [!NOTE]
>
> * Le nombre de cœurs et la quantité de mémoire CPU correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans la commande « docker run ».
>
> * Les spécifications minimale et recommandée sont basées sur les limites de Docker, pas sur les ressources de la machine hôte.

## <a name="request-approval-to-run-container"></a>Demande d’approbation pour l’exécution d’un conteneur

Complétez et envoyez le [**formulaire des services contrôlés Azure Cognitive Services**](https://aka.ms/csgate-translator) pour demander l’accès au conteneur.

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

## <a name="get-container-images-with-docker-commands"></a>Obtenir des images conteneurs en utilisant les **commandes docker**

> [!IMPORTANT]
>
> * les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte.
> * Vous devez spécifier les options `EULA`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.

Utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour télécharger une image conteneur du registre de conteneurs Microsoft et l’exécuter.

```Docker
docker run --rm -it -p 5000:80 --memory 12g --cpus 4 \
-v /mnt/d/TranslatorContainer:/usr/local/models \
-e apikey={API_KEY} \
-e eula=accept \
-e billing={ENDPOINT_URI} \
-e Languages=en,fr,es,ar,ru  \
mcr.microsoft.com/azure-cognitive-services/translator/text-translation
```

La commande ci-dessus :

* Télécharge et exécute un conteneur Translator à partir de l’image conteneur.
* Alloue 12 gigaoctets (Go) de mémoire et quatre cœurs CPU.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Accepte le contrat de licence utilisateur final (CLUF).
* Configure le point de terminaison de facturation.
* Télécharge des modèles de traduction pour certaines langues (anglais, français, espagnol, arabe et russe).
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

### <a name="run-multiple-containers-on-the-same-host"></a>Exécuter plusieurs conteneurs sur le même hôte

Si vous envisagez d’exécuter plusieurs conteneurs avec les ports exposés, veillez à exécuter chaque conteneur avec un port exposé différent. Par exemple, exécutez le premier conteneur sur le port 5000 et le second conteneur sur le port 5001.

Vous pouvez avoir ce conteneur, et un autre conteneur Azure Cognitive Services qui s’exécutent ensemble sur l’hôte. Vous pouvez également disposer de plusieurs conteneurs du même conteneur Cognitive Services en cours d’exécution.

## <a name="query-the-containers-translator-endpoint"></a>Interroger le point de terminaison Translator du conteneur

 Le conteneur fournit une API de point de terminaison REST pour Translator. Voici un exemple de requête :

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en&to=zh-HANS"
    -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

> [!NOTE]
> Si vous tentez la requête cURL POST avant que le conteneur soit prêt, vous obtenez en réponse un message *Le service est temporairement indisponible*. Attendez que le conteneur soit prêt, puis réessayez.

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshoot"></a>Dépanner

### <a name="validate-that-a-container-is-running"></a>Valider l’exécution d’un conteneur

Il existe plusieurs façons de vérifier que le conteneur est exécuté :

* Le conteneur fournit une page d’accueil au niveau `\`, qui est une validation visuelle que le conteneur est en cours d’exécution.

* Vous pouvez ouvrir votre navigateur web habituel, puis accéder à l’adresse IP externe et au port exposé du conteneur en question. Utilisez les différentes URL de requête ci-dessous pour vérifier que le conteneur est en cours d’exécution. Les exemples d’URL de requête listés ci-dessous sont `http://localhost:5000`, mais votre conteneur spécifique peut varier. N’oubliez pas que vous accédez à l’**adresse IP externe** et au port exposé de votre conteneur.

| URL de la demande | Objectif |
|--|--|
| `http://localhost:5000/` | Le conteneur fournit une page d’hébergement. |
| `http://localhost:5000/ready` | Obligatoire avec GET. Vérifie si le conteneur est prêt à accepter une requête sur le modèle.  Cette requête peut être utilisée pour les [probes liveness et readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) de Kubernetes. |
| `http://localhost:5000/status` | Obligatoire avec GET.  Vérifie si la clé API servant à démarrer le conteneur est valide sans provoquer de requête de point de terminaison. Cette requête peut être utilisée pour les [probes liveness et readiness](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) de Kubernetes. |
| `http://localhost:5000/swagger` | Le conteneur fournit un ensemble complet de documentation pour les points de terminaison et une fonctionnalité **Essayer**. Avec cette fonctionnalité, vous pouvez entrer vos paramètres dans un formulaire HTML basé sur le web, et constituer la requête sans avoir à écrire du code. Une fois la requête retournée, un exemple de commande CURL est fourni pour illustrer les en-têtes HTTP, et le format du corps qui est nécessaire. |

:::image type="content" source="../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png" alt-text="Page d’accueil du conteneur":::

[!INCLUDE [Diagnostic container](../../containers/includes/diagnostics-container.md)]

## <a name="text-translation-code-samples"></a>Exemples de code de traduction de texte

### <a name="translate-text-with-swagger"></a>Traduire du texte avec Swagger

#### <a name="english-leftrightarrow-german"></a>Anglais &leftrightarrow; Allemand

Accédez à la page Swagger : `<http://localhost:5000/swagger/index.html>`

1. Sélectionnez **POST /translate**
1. Sélectionnez **Faites un essai**.
1. Entrez le paramètre **De** comme `en`
1. Entrez le paramètre **À** comme `de`
1. Entrez le paramètre **api-version** comme `3.0`
1. Sous **texts**, remplacez `string` par le code JSON suivant

```json
  [
        {
            "text": "hello, how are you"
        }
  ]
```

Sélectionnez **Exécuter**, les traductions obtenues sont générées dans le **corps de la réponse**. Vous devez voir un texte semblable à cette réponse :

```json
"translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de"
      }
    ]
```

### <a name="translate-text-with-python"></a>Traduire du texte avec Python

```python
import requests, json

url = 'http://localhost:5000/translate?api-version=3.0&from=en&to=fr'
headers = { 'Content-Type': 'application/json' }
body = [{ 'text': 'Hello, how are you' }]

request = requests.post(url, headers=headers, json=body)
response = request.json()

print(json.dumps(
    response,
    sort_keys=True,
     indent=4,
     ensure_ascii=False,
     separators=(',', ': ')))
```

### <a name="translate-text-with-cnet-console-app"></a>Traduire du texte avec une application console C#/.NET

Ouvrez Visual Studio et créez une nouvelle application de console. Modifiez le fichier `*.csproj` pour ajouter le nœud `<LangVersion>7.1</LangVersion>`, qui spécifie la version 7.1 de C#. Ajoutez le package NuGet [Newtoonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/), version 11.0.2.

Dans le fichier `Program.cs`, remplacez tout le code existant par ce qui suit :

```csharp
using Newtonsoft.Json;
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace TranslateContainer
{
    class Program
    {
        const string ApiHostEndpoint = "http://localhost:5000";
        const string TranslateApi = "/translate?api-version=3.0&from=en&to=de";

        static async Task Main(string[] args)
        {
            var textToTranslate = "Sunny day in Seattle";
            var result = await TranslateTextAsync(textToTranslate);

            Console.WriteLine(result);
            Console.ReadLine();
        }

        static async Task<string> TranslateTextAsync(string textToTranslate)
        {
            var body = new object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            var client = new HttpClient();
            using (var request =
                new HttpRequestMessage
                {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                    Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                })
            {
                // Send the request and await a response.
                var response = await client.SendAsync(request);

                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez découvert les concepts et les workflows utilisés pour le téléchargement, l’installation et l’exécution d’un conteneur Translator. Vous savez à présent que :

* Translator fournit des conteneurs Linux pour Docker.
* Les images conteneurs sont téléchargées du registre de conteneurs, puis sont exécutées dans Docker.
* Vous pouvez utiliser l’API REST pour appeler l’opération « translate » dans le conteneur Translator en spécifiant l’URI hôte du conteneur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les conteneurs Azure Cognitive Services](../../containers/index.yml?context=%2fazure%2fcognitive-services%2ftranslator%2fcontext%2fcontext)
