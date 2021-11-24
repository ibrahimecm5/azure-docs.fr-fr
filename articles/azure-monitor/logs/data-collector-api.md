---
title: API Collecte de données HTTP Azure Monitor | Microsoft Docs
description: L’API Collecte de données HTTP Azure Monitor permet d’ajouter des données POST JSON à un espace de travail Log Analytics à partir de tout client pouvant appeler l’API REST. Cet article explique comment utiliser l’API et contient des exemples montrant comment publier des données à l’aide de divers langages de programmation.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: a7d998499285dbaa400269bc87d027febfd0ca14
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325616"
---
# <a name="send-log-data-to-azure-monitor-by-using-the-http-data-collector-api-preview"></a>Envoyer des données de journal à Azure Monitor à l’aide de l’API Collecte de données HTTP (préversion)

Cet article vous montre comment utiliser l’API Collecte de données HTTP pour transmettre des données à Azure Monitor à partir d’un client API REST.  Il explique comment mettre en forme les données collectées par votre script ou votre application, les inclure dans une demande et faire en sorte qu’Azure Monitor autorise cette demande. Nous fournissons des exemples pour Azure PowerShell, C# et Python.

> [!NOTE]
> L’API Collecteur de données HTTP Azure Monitor est en préversion publique.

## <a name="concepts"></a>Concepts
L’API Collecte de données HTTP permet d’ajouter des données de journal d’activité à un espace de travail Log Analytics dans Azure Monitor à partir de tout client pouvant appeler une API REST.  Le client peut être un runbook dans Azure Automation qui collecte des données de gestion à partir d’Azure ou d’un autre cloud, ou il peut s’agir d’un système de gestion alternatif qui utilise Azure Monitor pour regrouper et analyser les données de journal.

Toutes les données de l’espace de travail Log Analytics sont stockées en tant qu’enregistrement avec un type d’enregistrement particulier.  Vous formatez vos données à envoyer à l’API Collecte de données HTTP en plusieurs enregistrements dans JSON (JavaScript Object Notation).  Lorsque vous envoyez vos données, un enregistrement individuel est créé dans le référentiel pour chaque enregistrement dans la charge utile de la requête.

![Capture d’écran illustrant la vue d’ensemble de la collecte de données HTTP](media/data-collector-api/overview.png)

## <a name="create-a-request"></a>Créer une demande
Pour utiliser l’API Collecte de données HTTP, vous créez une demande POST qui inclut les données à envoyer dans JSON. Les trois tableaux suivants répertorient les attributs requis pour chaque requête. Nous décrivons chaque attribut de façon plus détaillée plus loin dans cet article.

### <a name="request-uri"></a>URI de demande
| Attribut | Propriété |
|:--- |:--- |
| Méthode |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Type de contenu |application/json |
| | |

### <a name="request-uri-parameters"></a>Paramètres de l’URI de demande
| Paramètre | Description |
|:--- |:--- |
| CustomerID |Identificateur unique de l’espace de travail Log Analytics. |
| Ressource |Nom de ressource de l’API : / api/logs. |
| Version de l'API |Version de l’API à utiliser avec cette demande. Actuellement, la version est 2016-04-01. |
| | |

### <a name="request-headers"></a>En-têtes de requête
| En-tête | Description |
|:--- |:--- |
| Autorisation |Signature de l’autorisation. Plus loin dans cet article, vous pouvez lire comment créer un en-tête HMAC-SHA256. |
| Log-Type |Spécifiez le type d’enregistrement des données en cours d’envoi. Il peut contenir uniquement des lettres, des chiffres et des caractères de soulignement (_), et ne peut pas dépasser 100 caractères. |
| x-ms-date |Date à laquelle la demande a été traitée, au format RFC 7234. |
| x-ms-AzureResourceId | ID de la ressource Azure à laquelle les données doivent être associées. Il remplit la propriété [_ResourceId](./log-standard-columns.md#_resourceid) et permet d’inclure les données dans des requêtes de [contexte de ressource](./design-logs-deployment.md#access-mode). Si ce champ n’est pas spécifié, les données ne sont pas incluses dans les requêtes de contexte de ressource. |
| time-generated-field | Nom d’un champ de données qui contient l’horodateur de l’élément de données. Si vous spécifiez un champ, son contenu est utilisé pour **TimeGenerated**. Si vous ne spécifiez pas ce champ, la valeur par défaut de **TimeGenerated** est l’heure d’ingestion du message. Le contenu du champ de message doit suivre le format ISO 8601 AAAA-MM-JJThh:mm:ssZ. |
| | |

## <a name="authorization"></a>Autorisation
Toute demande adressée à l’API Collecte de données HTTP Azure Monitor doit inclure un en-tête d’autorisation. Pour authentifier une demande, signez la demande avec la clé primaire ou secondaire de l’espace de travail qui effectue la demande. Ensuite, transmettez cette signature dans le cadre de la demande.   

Voici le format de l’en-tête d’autorisation :

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* est l’identificateur unique de l’espace de travail Log Analytics. *Signature* est un [code d’authentification de message basé sur le hachage (HMAC)](/dotnet/api/system.security.cryptography.hmacsha256) élaboré à partir de la demande, puis calculé à l’aide de l’[algorithme SHA256](/dotnet/api/system.security.cryptography.sha256). Ensuite, vous l’encodez à l’aide d’un encodage Base64.

Utilisez ce format pour encoder la chaîne de signature **SharedKey** :

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
                  Content-Type + "\n" +
                  "x-ms-date:" + x-ms-date + "\n" +
                  "/api/logs";
```

Voici un exemple de chaîne de signature :

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Lorsque vous avez la chaîne de signature, encodez-la en utilisant l’algorithme HMAC-SHA256 sur la chaîne encodée en UTF-8, puis encodez le résultat en Base64. Utilisez le format suivant :

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Les exemples fournis dans les sections suivantes comportent un exemple de code pour vous aider à créer un en-tête d’autorisation.

## <a name="request-body"></a>Corps de la demande
Le corps du message doit être au format JSON. Il doit inclure un ou plusieurs enregistrements avec les paires nom de propriété/valeur au format suivant. Le nom de la propriété peut contenir uniquement des lettres, des chiffres et des traits de soulignement (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Vous pouvez regrouper plusieurs enregistrements par lot dans une seule requête en utilisant le format suivant. Tous les enregistrements doivent être du même type.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Type et propriétés d’enregistrement
Vous définissez un type d’enregistrement personnalisé lorsque vous envoyez des données via l’API Collecte de données HTTP Azure Monitor. Actuellement, vous ne pouvez pas écrire de données dans des types d’enregistrements existants qui ont été créés par d’autres types de données et solutions. Azure Monitor lit les données entrantes, puis crée des propriétés correspondant aux types de données des valeurs que vous entrez.

Chaque demande adressée à l’API Azure Monitor doit inclure un en-tête **Log-Type** avec le nom du type d’enregistrement. Le suffixe **_CL** est automatiquement ajouté au nom que vous entrez pour le distinguer d’autres types de journaux en tant que journal personnalisé. Par exemple, si vous entrez le nom **MyNewRecordType**, Azure Monitor crée un enregistrement du type **MyNewRecordType_CL**. Cela évite tout conflit entre les noms de type créés par l’utilisateur et ceux fournis dans les solutions Microsoft actuelles ou futures.

Pour identifier le type de données d’une propriété, Azure Monitor ajoute un suffixe au nom de celle-ci. Si une propriété contient une valeur null, la propriété n’est pas incluse dans cet enregistrement. Ce tableau répertorie les types de données de propriété et les suffixes correspondants :

| Type de données de propriété | Suffixe |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Date/time |_t |
| GUID (stocké en tant que chaîne) |_g |
| | |

> [!NOTE]
> Les valeurs de chaîne qui sont des GUID se voient attribuer le suffixe _g et sont formatées en tant que GUID, même si la valeur entrante ne comporte pas de tirets. Par exemple, « 8145d822-13a7-44ad-859c-36f31a84f6dd » et « 8145d82213a744ad859c36f31a84f6dd » sont tous deux stockés en tant que « 8145d822-13a7-44ad-859c-36f31a84f6dd ». Les seules différences entre cela et une autre chaîne sont le _g dans le nom et l’insertion de tirets s’ils ne sont pas fournis dans l’entrée. 

Le type de données que Azure Monitor utilise pour chaque propriété dépend de l’existence préalable ou non du type d’enregistrement pour le nouvel enregistrement.

* Si le type d’enregistrement n’existe pas, Azure Monitor en crée un à l’aide de l’inférence de type JSON pour déterminer le type de données pour chaque propriété du nouvel enregistrement.
* Si le type d’enregistrement existe, Azure Monitor tente de créer un enregistrement en fonction des propriétés existantes. Si le type de données d’une propriété dans le nouvel enregistrement ne correspond pas et ne peut pas être converti vers le type existant, ou si l’enregistrement contient une propriété inexistante, Azure Monitor crée une propriété portant le suffixe approprié.

Par exemple, l’entrée de soumission suivante créerait un enregistrement avec trois propriétés, **number_d**, **boolean_b** et **string_s** :

![Capture d’écran de l’exemple d’enregistrement 1](media/data-collector-api/record-01.png)

Si vous deviez envoyer ensuite l’entrée suivante, avec toutes les valeurs formatées en tant que chaînes, les propriétés ne changeraient pas. Vous pouvez convertir les valeurs en types de données existants.

![Capture d’écran de l’exemple d’enregistrement 2](media/data-collector-api/record-02.png)

Mais, si vous faisiez ensuite l’envoi suivant, Azure Monitor créerait les nouvelles propriétés **boolean_d** et **string_d**. Vous ne pouvez pas convertir ces valeurs.

![Capture d’écran de l’exemple d’enregistrement 3](media/data-collector-api/record-03.png)

Si vous envoyiez ensuite l’entrée suivante, avant la création du type d’enregistrement, Azure Monitor créerait un enregistrement avec trois propriétés, **number_s**, **boolean_s** et **string_s**. Dans cette entrée, toutes les valeurs initiales sont au format de chaîne :

![Capture d’écran de l’exemple d’enregistrement 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Propriétés réservées
Les propriétés suivantes sont réservées et ne doivent pas être utilisées dans un type d’enregistrement personnalisé. Vous recevrez une erreur si votre charge utile contient l’un de ces noms de propriété :

- tenant

## <a name="data-limits"></a>Limites de données
Les données publiées dans l’API Collecte de données Azure Monitor sont soumises à certaines contraintes :

* Un maximum de 30 Mo par publication sur l’API de collecte de données d’Azure Monitor. Il s’agit d’une limite de taille pour une publication unique. Si les données d’une publication unique dépassent 30 Mo, vous devez diviser les données en blocs plus petits et les envoyer simultanément.
* Maximum de 32 Ko pour les valeurs de champ. Si la valeur de champ est supérieure à 32 Ko, les données seront tronquées.
* Maximum recommandé de 50 champs pour un type donné. Il s’agit d’une limite pratique du point de vue de la facilité d’utilisation et de l’expérience de recherche.  
* Les tables figurant dans les espaces de travail Log Analytics prennent en charge un maximum de 500 colonnes (appelées champs dans cet article). 
* Maximum de 50 caractères pour les noms de colonnes.

## <a name="return-codes"></a>Codes de retour
Le code d’état HTTP 200 signifie que la demande a été reçue et doit être traitée. Cela indique que l’opération a été accomplie avec succès.

L’ensemble complet de codes d’état que le service peut retourner est fourni dans le tableau suivant :

| Code | Statut | Code d'erreur | Description |
|:--- |:--- |:--- |:--- |
| 200 |OK | |La demande a été acceptée. |
| 400 |Demande incorrecte |InactiveCustomer |L’espace de travail a été fermé. |
| 400 |Demande incorrecte |InvalidApiVersion |La version d’API que vous avez spécifiée n’est pas reconnue par le service. |
| 400 |Demande incorrecte |InvalidCustomerId |L’ID d’espace de travail spécifié est non valide. |
| 400 |Demande incorrecte |InvalidDataFormat |Du code JSON non valide a été envoyé. Il se peut que le corps de la réponse contiennent des informations supplémentaires sur la manière de résoudre l’erreur. |
| 400 |Demande incorrecte |InvalidLogType |Le type de journal spécifié contient des caractères spéciaux ou numériques. |
| 400 |Demande incorrecte |MissingApiVersion |La version de l’API n’était pas spécifiée. |
| 400 |Demande incorrecte |MissingContentType |Le type de contenu n’était pas spécifié. |
| 400 |Demande incorrecte |MissingLogType |Le type de journal de valeur requis n’était pas spécifié. |
| 400 |Demande incorrecte |UnsupportedContentType |Le type de contenu n’était pas défini sur **application/json**. |
| 403 |Interdit |InvalidAuthorization |Le serveur n’a pas pu authentifier la demande. Vérifiez que la clé de connexion et l’ID de l’espace de travail sont valides. |
| 404 |Introuvable | | L’URL fournie est incorrecte ou la demande est trop grande. |
| 429 |Trop de demandes | | Le service reçoit un volume important de données à partir de votre compte. Relancez la requête ultérieurement. |
| 500 |Erreur interne du serveur |UnspecifiedError |Le service a rencontré une erreur interne. Relancez la requête. |
| 503 |Service indisponible |ServiceUnavailable |Le service est actuellement indisponible pour recevoir des demandes. Relancez la requête. |
| | |

## <a name="query-data"></a>Interroger des données
Pour interroger des données soumises par l’API Collecte de données HTTP Azure Monitor, recherchez des enregistrements dont la valeur **Type** correspond à la valeur **LogType** que vous avez spécifiée, assortie de **_CL**. Par exemple, si vous avez utilisé **MyCustomLog**, vous retournez tous les enregistrements avec `MyCustomLog_CL`.

## <a name="sample-requests"></a>Exemples de demandes
Les sections suivantes contiennent des exemples illustrant comment envoyer des données à l’API Collecte de données HTTP Azure Monitor à l’aide de divers langages de programmation.

Pour chaque exemple, définissez les variables pour l’en-tête d’autorisation en procédant comme suit :

1. Dans le portail Azure, recherchez votre espace de travail Log Analytics.
2. Sélectionnez **Gestion des agents**.
2. À droite de **ID de l’espace de travail**, sélectionnez l’icône **Copier**, puis collez l’ID en tant que valeur de la variable **ID client**.
3. À droite de **Clé primaire**, sélectionnez l’icône **Copier**, puis collez l’ID en tant que valeur de la variable **Clé partagée**.

Vous pouvez également modifier les variables pour le type de journal et les données JSON.

### <a name="powershell-sample"></a>Exemple de code PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}

# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemple de code C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Exemple de code Python
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


### <a name="java-sample"></a>Exemple Java

```java

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.springframework.http.MediaType;

import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.text.SimpleDateFormat;
import java.util.Base64;
import java.util.Calendar;
import java.util.TimeZone;
import java.util.Locale;

import static org.springframework.http.HttpHeaders.CONTENT_TYPE;

public class ApiExample {

  private static final String workspaceId = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
  private static final String sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";
  private static final String logName = "DemoExample";
  /*
  You can use an optional field to specify the timestamp from the data. If the time field is not specified,
  Azure Monitor assumes the time is the message ingestion time
   */
  private static final String timestamp = "";
  private static final String json = "{\"name\": \"test\",\n" + "  \"id\": 1\n" + "}";
  private static final String RFC_1123_DATE = "EEE, dd MMM yyyy HH:mm:ss z";

  public static void main(String[] args) throws IOException, NoSuchAlgorithmException, InvalidKeyException {
    String dateString = getServerTime();
    String httpMethod = "POST";
    String contentType = "application/json";
    String xmsDate = "x-ms-date:" + dateString;
    String resource = "/api/logs";
    String stringToHash = String
        .join("\n", httpMethod, String.valueOf(json.getBytes(StandardCharsets.UTF_8).length), contentType,
            xmsDate , resource);
    String hashedString = getHMAC256(stringToHash, sharedKey);
    String signature = "SharedKey " + workspaceId + ":" + hashedString;

    postData(signature, dateString, json);
  }

  private static String getServerTime() {
    Calendar calendar = Calendar.getInstance();
    SimpleDateFormat dateFormat = new SimpleDateFormat(RFC_1123_DATE, Locale.US);
    dateFormat.setTimeZone(TimeZone.getTimeZone("GMT"));
    return dateFormat.format(calendar.getTime());
  }

  private static void postData(String signature, String dateString, String json) throws IOException {
    String url = "https://" + workspaceId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
    HttpPost httpPost = new HttpPost(url);
    httpPost.setHeader("Authorization", signature);
    httpPost.setHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE);
    httpPost.setHeader("Log-Type", logName);
    httpPost.setHeader("x-ms-date", dateString);
    httpPost.setHeader("time-generated-field", timestamp);
    httpPost.setEntity(new StringEntity(json));
    try(CloseableHttpClient httpClient = HttpClients.createDefault()){
      HttpResponse response = httpClient.execute(httpPost);
      int statusCode = response.getStatusLine().getStatusCode();
      System.out.println("Status code: " + statusCode);
    }
  }

  private static String getHMAC256(String input, String key) throws InvalidKeyException, NoSuchAlgorithmException {
    String hash;
    Mac sha256HMAC = Mac.getInstance("HmacSHA256");
    Base64.Decoder decoder = Base64.getDecoder();
    SecretKeySpec secretKey = new SecretKeySpec(decoder.decode(key.getBytes(StandardCharsets.UTF_8)), "HmacSHA256");
    sha256HMAC.init(secretKey);
    Base64.Encoder encoder = Base64.getEncoder();
    hash = new String(encoder.encode(sha256HMAC.doFinal(input.getBytes(StandardCharsets.UTF_8))));
    return hash;
  }

}


```


## <a name="alternatives-and-considerations"></a>Alternatives et considérations

Bien que l’API Collecte de données soit censée répondre à la plupart de vos besoins dans le cadre de la collecte de données de forme libre dans les journaux Azure, vous aurez probablement besoin d’une approche alternative pour surmonter certaines limitations de l’API. Vos options, y compris les considérations majeures, sont listées dans le tableau suivant :

| Alternative | Description | Idéale pour |
|---|---|---|
| [Événements personnalisés](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties) : Ingestion native basée sur le Kit de développement logiciel (SDK) dans Application Insights | Application Insights, généralement instrumenté via un kit SDK au sein de votre application, vous permet d’envoyer des données personnalisées par le biais d’événements personnalisés. | <ul><li> Données générées au sein de votre application, mais non récupérées par le kit SDK via un des types de données par défaut (demandes, dépendances, exceptions, etc.).</li><li> Données le plus souvent corrélées à d’autres données d’application dans Application Insights. </li></ul> |
| API de collecte de données dans les journaux Azure Monitor | L’API de collecte de données dans les journaux Azure Monitor est une méthode d’ingestion de données complètement flexible. Toutes les données mises en forme dans un objet JSON peuvent être envoyées ici. Une fois envoyées, elles sont traitées et mises à disposition dans des journaux Azure Monitor pour être corrélées à d’autres données de journal Azure Monitor ou par rapport à d’autres données Application Insights. <br/><br/> Il est relativement facile de charger les données en tant que fichiers dans un objet blob de Stockage Blob Azure, où ces fichiers seront traités, puis chargés dans Log Analytics. Pour obtenir un exemple d’implémentation, consultez [Créer un pipeline de données avec l’API Collecte de données](./create-pipeline-datacollector-api.md). | <ul><li> Données qui ne sont pas nécessairement générées dans une application instrumentée dans Application Insights.<br>Les exemples incluent les tables de consultations et de faits, les données de référence, les statistiques pré-agrégées, etc. </li><li> Données qui seront croisées avec d’autres données d’Azure Monitor (Application Insights, autres types de données de journaux d’activité Azure Monitor, Defender pour le cloud, Container Insights et machines virtuelles, etc.). </li></ul> |
| [Explorateur de données Azure](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer, maintenant en disponibilité générale, est la plateforme de données sur laquelle s’appuient Application Insights Analytics et les journaux Azure Monitor. En utilisant la plateforme de données dans sa forme brute, vous disposez d’une flexibilité totale (mais nécessitez une surcharge de gestion) sur le cluster (contrôle d’accès en fonction du rôle Kubernetes (RBAC), taux de conservation, schéma, etc.). Azure Data Explorer propose de nombreuses [options d’ingestion](/azure/data-explorer/ingest-data-overview#ingestion-methods), notamment les fichiers [CSV, TSV et JSON](/azure/kusto/management/mappings). | <ul><li> Données qui ne seront pas corrélées à d’autres données dans Application Insights ou les journaux Azure Monitor. </li><li> Données nécessitant des fonctionnalités avancées d’ingestion ou de traitement qui ne sont pas disponibles aujourd’hui dans les journaux Azure Monitor. </li></ul> |


## <a name="next-steps"></a>Étapes suivantes
- Utilisez l’[API Recherche de journal](./log-query-overview.md) pour récupérer des données à partir de l’espace de travail Log Analytics.

- Découvrez plus en détail comment [créer un pipeline de données à l’aide de l’API Collecte de données ](create-pipeline-datacollector-api.md) en utilisant un workflow Logic Apps dans Azure Monitor.
