---
title: Charger et indexer les vidéos avec Azure Video Analyzer for Media (anciennement Video Indexer)
description: Cette rubrique montre comment utiliser des API pour charger et indexer vos vidéos avec Azure Video Analyzer for Media (anciennement Video Indexer).
ms.service: azure-video-analyzer
ms.topic: article
ms.date: 05/12/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 507e92a83f2c8f9f0a21368808b57b1fadc1b64d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071683"
---
# <a name="upload-and-index-your-videos"></a>Charger et indexer vos vidéos  

Lorsque votre vidéo a été chargée, Azure Video Analyzer for Media (anciennement Video Indexer) encode (si besoin) la vidéo (cette opération est décrite dans l’article). Lorsque vous créez un compte Azure Video Analyzer for Media, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limité par le quota). Avec l’essai gratuit, Azure Video Analyzer for Media fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec l’option payante, vous créez un compte Azure Video Analyzer for Media [connecté à votre abonnement Azure et un compte Azure Media Services](connect-to-azure.md). Vous payez pour les minutes indexées ; pour plus d’informations, consultez [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).

Lorsque vous chargez des vidéos avec l’API Video Analyzer for Media, vous disposez des options de chargement suivantes : 

* charger votre vidéo à partir d’une URL (par défaut),
* Envoyer le fichier vidéo sous forme de tableau d’octets dans le corps de la demande,
* Utilisez la ressource Azure Media Services existante en fournissant l’[ID de la ressource](../../media-services/latest/assets-concept.md) (pris en charge dans les comptes payants uniquement).

L’article décrit comment télécharger et indexer vos vidéos avec les options suivantes :

* Le site web Azure Video Analyzer for Media 
* Les APIs Video Analyzer for Media 

## <a name="supported-file-formats-for-video-analyzer-for-media"></a>Formats de fichiers pris en charge pour Azure Video Analyzer for Media

Pour obtenir la liste des formats de fichiers que vous pouvez utiliser avec Azure Video Analyzer for Media, consultez l’article sur les [formats de conteneurs/fichiers d’entrée](../../media-services/latest/encode-media-encoder-standard-formats-reference.md).

## <a name="video-files-storage"></a>Stockage de fichiers vidéo

- Avec une compte Video Analyzer for Media payant, vous créez un compte Video Analyzer for Media connecté à votre abonnement Azure et un compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte Video Analyzer for Media connecté à Azure](connect-to-azure.md).
- Les fichiers vidéo sont stockés dans le stockage Azure par Azure Media Services. Il n’existe aucune limite de temps.
- Vous pouvez toujours supprimer vos fichiers vidéo et audio, ainsi que les métadonnées et les insights qui ont été extraits par Video Analyzer for Media. Quand vous supprimez un fichier de Video Analyzer for Media, le fichier, ses métadonnées et ses insights sont définitivement supprimés de Video Analyzer for Media. Toutefois, si vous avez implémenté votre propre solution de sauvegarde dans le stockage Azure, le fichier demeure dans votre stockage Azure.
- La persistance d’une vidéo est identique, que le chargement soit effectué à partir du site web de Video Analyzer for Media ou à l’aide de l’API de chargement.
   
## <a name="upload-and-index-a-video-using-the-video-analyzer-for-media-website"></a>Charger et indexer une vidéo à l’aide du site web Azure Video Analyzer for Media

> [!NOTE]
> Un nom de vidéo ne doit pas dépasser 80 caractères.

1. Connectez-vous au site web [Azure Video Analyzer for Media](https://www.videoindexer.ai/).
1. Pour charger une vidéo, appuyez sur le bouton ou le lien **Charger**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Charger":::
1. Une fois votre vidéo chargée, Azure Video Analyzer for Media démarre l’indexation et l’analyse.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Progression du chargement":::
1. Au terme de l’analyse réalisée par Azure Video Analyzer for Media, vous recevez un e-mail contenant un lien vers votre vidéo et une brève description du contenu de celle-ci. Par exemple : personnes, rubriques, OCR.

## <a name="upload-and-index-with-api"></a>Télécharger et indexer avec API

Utilisez l’API [Télécharger une vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) pour télécharger et indexer vos vidéos en fonction d’une URL. L’exemple de code dans l’article inclut le code commenté qui montre comment charger le tableau d’octets. 

### <a name="configurations-and-params"></a>Configurations et paramètres

Cette section décrit certains des paramètres facultatifs et les moments où vous souhaitez les définir. Pour obtenir les informations les plus récentes sur les paramètres, consultez l’API [Télécharger une vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video).

#### <a name="externalid"></a>externalID 

Ce paramètre vous permet de spécifier un ID qui sera associé à la vidéo. L’ID peut être appliqué à l’intégration de système externe « Gestion de contenu vidéo» (VCM). Les vidéos situées dans le portail Video Analyzer for Media peuvent faire l’objet d’une recherche à l’aide de l’ID externe spécifié.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Autres considérations

- Video Analyzer for Media retourne tous les paramètres existants fournis dans l’URL d’origine.
- L’URL fournie doit être encodée.

#### <a name="indexingpreset"></a>indexingPreset

Utilisez ce paramètre pour définir le pack d’IA que vous souhaitez appliquer à votre fichier audio ou vidéo. Ce paramètre est utilisé pour configurer le processus d’indexation. Vous pouvez spécifier les valeurs suivantes :

- `AudioOnly` – Permet d’indexer et d’extraire des insights de l’audio uniquement (la vidéo est ignorée).
- `VideoOnly` – Permet d’indexer et d’extraire des insights de la vidéo uniquement (l’audio est ignoré).
- `Default` – Permet d’indexer et d’extraire des insights de l’audio et de la vidéo.
- `DefaultWithNoiseReduction` – Permet d’indexer et d’extraire des insights de l’audio et vidéo, tout en appliquant des algorithmes de réduction du bruit sur les flux audio.

    La valeur `DefaultWithNoiseReduction` est maintenant mappée à la présélection par défaut (déconseillée).
- `BasicAudio` — Permet d’indexer et d’extraire des insights à l’aide de l’audio uniquement (la vidéo est ignorée), y compris uniquement les fonctionnalités audio de base (transcription, traduction, format de légendes et de sous-titres).
- `AdvancedAudio` — Permet d’indexer et d’extraire des insights en utilisant uniquement l’audio (la vidéo est ignorée), y compris les fonctionnalités audio avancées (détection des événements audio) en plus de l’analyse audio standard.
- `AdvancedVideo` – Permet d’indexer et d’extraire des insights en utilisant uniquement la vidéo (l’audio est ignoré), y compris les fonctionnalités vidéo avancées (Traçage des personnes observées) en plus de l’analyse vidéo standard.
- `AdvancedVideoAndAudio` – Permet d’indexer et d’extraire des insights en utilisant à la fois l’audio avancé et l’analyse vidéo avancée. 

> [!NOTE]
> Parmi les présélections avancées (listées ci-dessus) figurent des modèles en préversion publique. La mise en disponibilité générale de ces modèles pourrait avoir des répercussions sur le tarif. 

Video Indexer prend en charge jusqu’à deux pistes audio. Si le fichier contient plus de pistes audio, elles sont traitées comme une seule piste.<br/>
Si vous souhaitez indexer les pistes séparément, vous devez extraire le fichier audio approprié et l’indexer comme `AudioOnly`.

Le prix dépend de l’option d’indexation sélectionnée. Pour plus d'informations, consultez la page [Tarification Media Services](https://azure.microsoft.com/pricing/details/media-services/).

#### <a name="priority"></a>priority

Les vidéos sont indexées par Video Analyzer for Media selon leur priorité. Utilisez le paramètre **priority** pour spécifier la priorité d’index. Les valeurs suivantes sont valides : **Basse**, **Normale** (valeur par défaut) et **Haute**.

Le paramètre **priority** est uniquement pris en charge dans les comptes payants.

#### <a name="streamingpreset"></a>streamingPreset

Une fois votre vidéo chargée, Video Analyzer for Media peut l’encoder (si besoin). Il passe ensuite à l’indexation et à l’analyse de la vidéo. Lorsque Video Analyzer for Media a terminé l’analyse, vous recevrez une notification avec l’ID de la vidéo.  

Lorsque vous utilisez l’API [Charger une vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) ou [Réindexer une vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video), un des paramètres facultatifs est `streamingPreset`. Si vous définissez `streamingPreset` sur `Default`, `SingleBitrate`, ou `AdaptiveBitrate`, le processus d’encodage est déclenché. Une fois les travaux d’indexation et d’encodage effectués, la vidéo est publiée et vous pouvez donc la diffuser. Le point de terminaison de streaming à partir duquel vous souhaitez diffuser la vidéo doit se trouver dans l’état **En cours d’exécution**.

Pour SingleBitrate, le coût de l’encodeur standard s’applique à chaque sortie. Si la hauteur de la vidéo est supérieure ou égale à 720, Video Analyzer for Media l’encode au format 1280 x 720. Sinon, au format 640 x 468.
Le paramètre par défaut est [l’encodage sensible au contenu](../../media-services/latest/encode-content-aware-concept.md).

Pour exécuter les travaux d’indexation et d’encodage, le [compte Azure Media Services connecté à votre compte Video Analyzer for Media](connect-to-azure.md), nécessite des unités réservées. Pour plus d’informations, consultez [Mise à l’échelle du traitement multimédia](../../media-services/previous/media-services-scale-media-processing-overview.md). Dans la mesure où il s’agit de tâches de calcul intensif, un type d’unité S3 est fortement recommandé. Le nombre d’unités de demande définit le nombre maximal de travaux pouvant s’exécuter en parallèle. La suggestion de base de référence est 10 unités de demande S3. 

Si vous souhaitez uniquement indexer votre vidéo sans l’encoder, définissez `streamingPreset` sur `NoStreaming`.

#### <a name="videourl"></a>videoUrl

URL du fichier audio/vidéo à indexer. L’URL doit pointer vers un fichier multimédia (les pages HTML ne sont pas prises en charge). Le fichier peut être protégé par un jeton d’accès fourni dans le cadre de l’URI et le point de terminaison qui traite le fichier doit être sécurisé avec TLS 1.2 ou version ultérieure. L’URL doit être encodée. 

Si le paramètre `videoUrl` n’est pas spécifié, Video Analyzer for Media s’attend à ce que vous passiez le fichier en tant que contenu de corps multipart/form.

### <a name="code-sample"></a>Exemple de code

Les extraits de code C# suivants illustrent l’utilisation conjointe de toutes les API Video Analyzer for Media.

### <a name="classic-account"></a>[Compte classique](#tab/With-classic-account/)

Après avoir copié ce code dans votre plateforme de développement, vous devez fournir deux paramètres : URL de la vidéo et clé d’authentification de Gestion des API.

* Clé API : la clé API est votre clé d’abonnement de gestion des API personnelle, qui vous permet d’obtenir un jeton d’accès afin d’effectuer des opérations sur votre compte Video Analyzer for Media. 

    Pour obtenir votre clé API, effectuez les étapes suivantes :

    * Accédez à https://api-portal.videoindexer.ai/.
    * Connexion
    * Accédez à **Products** -> **Authorization** -> **Authorization subscription**.
    * Copiez la **clé primaire**.
* URL de la vidéo : URL du fichier audio/vidéo à indexer. L’URL doit pointer vers un fichier multimédia (les pages HTML ne sont pas prises en charge). Le fichier peut être protégé par un jeton d’accès fourni dans le cadre de l’URI et le point de terminaison qui traite le fichier doit être sécurisé avec TLS 1.2 ou version ultérieure. L’URL doit être encodée.

Le résultat de l’exécution de l’exemple de code inclut une URL de widget d’insight et une URL de widget de lecteur qui vous permettra d’examiner respectivement les insights et la vidéo chargée. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="arm-account"></a>[Compte ARM](#tab/with-arm-account-account/)

Après avoir copié ce projet C# dans votre plateforme de développement, vous devrez effectuer les étapes suivantes : 
1. Accédez à Program.cs et renseignez ```SubscriptionId``` avec votre ID d’abonnement.
2. Accédez à Program.cs et renseignez ```ResourceGroup``` avec votre groupe de ressources.
3. Accédez à Program.cs et renseignez ```AccountName``` avec votre nom de compte.
4. Accédez à Program.cs et renseignez ```VideoUrl``` avec l’URL de votre vidéo.
5. Vérifiez que dotnet 6.0 est installé. Si ce n’est pas le cas, cliquez [ici](https://dotnet.microsoft.com/download/dotnet/6.0) pour l’installer.
6. Vérifiez qu’Azure CLI est installé. Si ce n’est pas le cas, cliquez [ici](/cli/azure/install-azure-cli) pour l’installer.
7. Ouvrez votre terminal et accédez au dossier VideoIndexerArm.
8. Connectez-vous à Azure : ```az login --use-device```
9. Générez le projet : ```dotnet build```
10. Exécutez le projet : ```dotnet run```

```csharp
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Azure.Identity" Version="1.4.1" />
    <PackageReference Include="Microsoft.Identity.Client" Version="4.36.2" />
  </ItemGroup>

</Project>
```

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using System.Web;
using Azure.Core;
using Azure.Identity;


namespace VideoIndexerArm
{
    public class Program
    {
        private const string AzureResourceManager = "https://management.azure.com";
        private const string SubscriptionId = ""; // Your azure subscription
        private const string ResourceGroup = ""; // Your resource group
        private const string AccountName = ""; // Your account name
        private const string VideoUrl = ""; // The video url you would like to index

        public static async Task Main(string[] args)
        {
            // Build Azure Video Analyzer for Media resource provider client that has access token throuhg ARM
            var videoIndexerResourceProviderClient = await VideoIndexerResourceProviderClient.BuildVideoIndexerResourceProviderClient();

            // Get account details
            var account = await videoIndexerResourceProviderClient.GetAccount();
            var accountId = account.Properties.Id;
            var accountLocation = account.Location;
            Console.WriteLine($"account id: {accountId}");
            Console.WriteLine($"account location: {accountLocation}");

            // Get account level access token for Azure Video Analyzer for Media 
            var accessTokenRequest = new AccessTokenRequest
            {
                PermissionType = AccessTokenPermission.Contributor,
                Scope = ArmAccessTokenScope.Account
            };

            var accessToken = await videoIndexerResourceProviderClient.GetAccessToken(accessTokenRequest);
            var apiUrl = "https://api.videoindexer.ai";
            System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;


            // Create the http client
            var handler = new HttpClientHandler();
            handler.AllowAutoRedirect = false;
            var client = new HttpClient(handler);

            // Upload a video
            var content = new MultipartFormDataContent();
            Console.WriteLine("Uploading...");
            // Get the video from URL

            // As an alternative to specifying video URL, you can upload a file.
            // Remove the videoUrl parameter from the query params below and add the following lines:
            // FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
            // byte[] buffer =new byte[video.Length];
            // video.Read(buffer, 0, buffer.Length);
            // content.Add(new ByteArrayContent(buffer));

            var queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"name", "video sample"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", VideoUrl},
                });
            var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos?{queryParams}", content);
            var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

            // Get the video ID from the upload result
            string videoId = JsonSerializer.Deserialize<Video>(uploadResult).Id;
            Console.WriteLine("Uploaded");
            Console.WriteLine("Video ID:");
            Console.WriteLine(videoId);

            // Wait for the video index to finish
            while (true)
            {
                await Task.Delay(10000);

                queryParams = CreateQueryString(
                    new Dictionary<string, string>()
                    {
                {"accessToken", accessToken},
                {"language", "English"},
                    });

                var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/Index?{queryParams}");
                var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

                string processingState = JsonSerializer.Deserialize<Video>(videoGetIndexResult).State;

                Console.WriteLine("");
                Console.WriteLine("State:");
                Console.WriteLine(processingState);

                // Job is finished
                if (processingState != "Uploaded" && processingState != "Processing")
                {
                    Console.WriteLine("");
                    Console.WriteLine("Full JSON:");
                    Console.WriteLine(videoGetIndexResult);
                    break;
                }
            }

            // Search for the video
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"id", videoId},
                });

            var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/Search?{queryParams}");
            var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
            Console.WriteLine("");
            Console.WriteLine("Search:");
            Console.WriteLine(searchResult);

            // Get insights widget url
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
                });
            var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?{queryParams}");
            var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
            Console.WriteLine("Insights Widget url:");
            Console.WriteLine(insightsWidgetLink);

            // Get player widget url
            queryParams = CreateQueryString(
                new Dictionary<string, string>()
                {
            {"accessToken", accessToken},
                });
            var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountLocation}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?{queryParams}");
            var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
            Console.WriteLine("");
            Console.WriteLine("Player Widget url:");
            Console.WriteLine(playerWidgetLink);
            Console.WriteLine("\nPress Enter to exit...");
            String line = Console.ReadLine();
            if (line == "enter")
            {
                System.Environment.Exit(0);
            }

        }

        static string CreateQueryString(IDictionary<string, string> parameters)
        {
            var queryParameters = HttpUtility.ParseQueryString(string.Empty);
            foreach (var parameter in parameters)
            {
                queryParameters[parameter.Key] = parameter.Value;
            }

            return queryParameters.ToString();
        }

        public class VideoIndexerResourceProviderClient
        {
            private readonly string armAaccessToken;

            async public static Task<VideoIndexerResourceProviderClient> BuildVideoIndexerResourceProviderClient()
            {
                var tokenRequestContext = new TokenRequestContext(new[] { $"{AzureResourceManager}/.default" });
                var tokenRequestResult = await new DefaultAzureCredential().GetTokenAsync(tokenRequestContext);
                return new VideoIndexerResourceProviderClient(tokenRequestResult.Token);
            }
            public VideoIndexerResourceProviderClient(string armAaccessToken)
            {
                this.armAaccessToken = armAaccessToken;
            }

            public async Task<string> GetAccessToken(AccessTokenRequest accessTokenRequest)
            {
                Console.WriteLine($"Getting access token. {JsonSerializer.Serialize(accessTokenRequest)}");
                // Set the generateAccessToken (from video indexer) http request content
                var jsonRequestBody = JsonSerializer.Serialize(accessTokenRequest);
                var httpContent = new StringContent(jsonRequestBody, System.Text.Encoding.UTF8, "application/json");

                // Set request uri
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/generateAccessToken?api-version=2021-08-16-preview";

                // Generate access token from video indexer
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.PostAsync(requestUri, httpContent);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<GenerateAccessTokenResponse>(jsonResponseBody).AccessToken;
            }

            public async Task<Account> GetAccount()
            {

                Console.WriteLine($"Getting account.");
                // Set request uri
                var requestUri = $"{AzureResourceManager}/subscriptions/{SubscriptionId}/resourcegroups/{ResourceGroup}/providers/Microsoft.VideoIndexer/accounts/{AccountName}/?api-version=2021-08-16-preview";

                // Get account
                var client = new HttpClient(new HttpClientHandler());
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", armAaccessToken);
                var result = await client.GetAsync(requestUri);
                var jsonResponseBody = await result.Content.ReadAsStringAsync();
                return JsonSerializer.Deserialize<Account>(jsonResponseBody);
            }
        }

        public class AccessTokenRequest
        {
            [JsonPropertyName("permissionType")]
            public AccessTokenPermission PermissionType { get; set; }

            [JsonPropertyName("scope")]
            public ArmAccessTokenScope Scope { get; set; }

            [JsonPropertyName("projectId")]
            public string ProjectId { get; set; }
            
            [JsonPropertyName("videoId")]
            public string VideoId { get; set; }
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum AccessTokenPermission
        {
            Reader,
            Contributor,
            MyAccessAdministrator,
            Owner,
        }

        [JsonConverter(typeof(JsonStringEnumConverter))]
        public enum ArmAccessTokenScope
        {
            Account,
            Project,
            Video
        }

        public class GenerateAccessTokenResponse
        {
            [JsonPropertyName("accessToken")]
            public string AccessToken { get; set; }

        }
        public class AccountProperties
        {
            [JsonPropertyName("accountId")]
            public string Id { get; set; }
        }

        public class Account
        {
            [JsonPropertyName("properties")]
            public AccountProperties Properties { get; set; }

            [JsonPropertyName("location")]
            public string Location { get; set; }

        }

        public class Video
        {
            [JsonPropertyName("id")]
            public string Id { get; set; }

            [JsonPropertyName("state")]
            public string State { get; set; }
        }
    }
}

```

### <a name="common-errors"></a>Erreurs courantes

Les codes d’état répertoriés dans le tableau suivant peuvent être renvoyés par l’opération de chargement (Upload).

|Code d’état|ErrorType (dans le corps de la réponse)|Description|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|La même vidéo est déjà en cours de traitement dans le compte en question.|
|400|VIDEO_ALREADY_FAILED|La même vidéo n’a pas pu être traitée dans le compte en question moins de 2 heures auparavant. Les clients API doivent attendre au moins 2 heures avant de recharger une vidéo.|
|429||Les comptes de version d’essai sont autorisés à effectuer 5 chargements par minute. Les comptes payants sont autorisés à effectuer 50 chargements par minute.|

## <a name="uploading-considerations-and-limitations"></a>Considérations et limitations relatives au chargement
 
- Un nom de vidéo ne doit pas dépasser 80 caractères.
- Lors du chargement de votre vidéo à partir de l’URL (par défaut), le point de terminaison doit être sécurisé avec TLS 1.2 (ou version ultérieure).
- La taille du chargement avec l’option URL est limitée à 30 Go.
- La longueur de l’URL de la requête est limitée à 6 144 caractères, et la longueur de l’URL de la chaîne de requête est limitée à 4 096 caractères.
- La taille du chargement avec l’option Tableau d’octets est limitée à 2 Go.
- L’option Tableau d’octets expire au bout de 30 minutes.
- L’URL fournie dans le paramètre `videoURL` doit être encodée.
- L’indexation des actifs multimédias Media Services a la même restriction que l’indexation à partir d’une URL.
- Video Analyzer for Media peut traiter des fichiers d’une durée maximale de 4 heures.
- L’URL doit être accessible (par exemple, une URL publique). 

    S’il s’agit d’une URL privée, le jeton d’accès doit être fourni dans la requête.
- L’URL doit emmener vers un fichier multimédia valide et non vers une page Web, par exemple un lien vers la page de `www.youtube.com`.
- Vous pouvez charger jusqu’à 50 films par minute dans un compte payant et jusqu’à 5 films par minute dans un compte de version d’essai.

> [!Tip]
> Il est recommandé d’utiliser la version 4.6.2 du .NET Framework. ou une version ultérieure, car les anciens .NET Framework ne sont pas définis par défaut sur TLS 1.2.
>
> Si vous devez utiliser une version antérieure du .NET Framework, ajoutez une ligne dans votre code avant d’effectuer l’appel de l’API REST :  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="firewall"></a>Pare-feu

Consultez [Compte de stockage qui se trouve derrière un pare-feu](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall).

## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie d’Azure Video Analyzer for Media générée par l’API](video-indexer-output-json-v2.md)
