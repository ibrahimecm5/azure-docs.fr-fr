---
title: Référence de Translator v3.0
titleSuffix: Azure Cognitive Services
description: Documentation de référence de Translator v3.0 Translator v3 fournit une API web moderne basée sur JSON.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 09/09/2021
ms.author: lajanuar
ms.openlocfilehash: 777ee0bcbf139c9edc9e4715133faec3318f692b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434500"
---
# <a name="translator-v30"></a>Translator v3.0

## <a name="whats-new"></a>Nouveautés

Translator v3 fournit une API web moderne basée sur JSON. Elle simplifie l’utilisation et améliorer les performances en regroupant les fonctionnalités existantes en moins d’opérations, et elle fournit de nouvelles fonctionnalités.

* Translittération pour convertir du texte dans une langue d’un script vers un autre script.
* Traduction en plusieurs langues en une seule demande.
* Détection de la langue, traduction et translittération en une seule demande.
* Dictionnaire permettant de rechercher d’autres traductions d’un terme et de rechercher des traductions inverses et des exemples montrant les termes utilisés en contexte.
* Résultats de détection de langue plus informatifs.

## <a name="base-urls"></a>URL de base

Les requêtes adressées à Translator sont dans la plupart des cas gérées par le centre de données le plus proche de l’emplacement d’origine de la requête. En cas de défaillance du centre de production lors de l’utilisation du point de terminaison global, la requête peut être acheminée en dehors de la zone géographique.

Pour forcer le traitement de la requête dans une zone géographique spécifique, utilisez le point de terminaison géographique souhaité. Toutes les requêtes sont traitées dans les centres de données de la zone géographique. 

|Geography|URL de base (point de terminaison géographique)|Centres de données|
|:--|:--|:--|
|Global (région non précisée)|    api.cognitive.microsofttranslator.com|Centre de données disponible le plus proche|
|Asie-Pacifique|    api-apc.cognitive.microsofttranslator.com|Corée Sud, Japon Est, Asie Sud-Est et Australie Est|
|Europe|    api-eur.cognitive.microsofttranslator.com|Europe Nord, Europe Ouest|
|États-Unis|    api-nam.cognitive.microsofttranslator.com|USA Est, USA Centre Sud, USA Centre-Ouest, USA Ouest 2|

<sup>1</sup> Les clients disposant d’une ressource située dans la région Suisse Nord ou Suisse Ouest peuvent s’assurer que leurs requêtes d’API de texte sont traitées en Suisse. Pour garantir que les requêtes sont gérées en Suisse, créez la ressource Translator dans la « Région de ressource » « Suisse Nord » ou « Suisse Ouest », puis utilisez le point de terminaison personnalisé de la ressource dans vos requêtes d’API. Par exemple : si vous créez une ressource Translator dans le portail Azure avec « Suisse Nord » comme « Région de ressource » et que le nom de votre ressource est « my-ch-n », votre point de terminaison personnalisé est « https://my-ch-n.cognitiveservices.azure.com ». Et voici un exemple de demande de traduction :
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> Custom Translator n’est actuellement pas disponible en Suisse.

## <a name="authentication"></a>Authentification

Abonnez-vous à Translator ou à [un abonnement multiservice Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) dans Azure Cognitive Services, et utilisez votre clé d’abonnement (disponible dans le portail Azure) pour vous authentifier.

Trois en-têtes sont à votre disposition pour authentifier votre abonnement. Ce tableau décrit la façon dont chaque en-tête est utilisé :

|headers|Description|
|:----|:----|
|Ocp-Apim-Subscription-Key|*À utiliser avec un abonnement à Cognitive Services si vous transmettez votre clé secrète*.<br/>La valeur est la clé secrète Azure pour votre abonnement à Translator.|
|Autorisation|*À utiliser avec un abonnement à Cognitive Services si vous transmettez un jeton d'authentification.*<br/>La valeur est le jeton du porteur : `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Utilisation avec la ressource multiservice Cognitive Services et la ressource du traducteur régional.*<br/>La valeur est la région de la ressource multiservice ou de la ressource du traducteur régional. Cette valeur est facultative si vous utilisez une ressource de traducteur globale.|

###  <a name="secret-key"></a>Clé secrète
La première option consiste à procéder à l’authentification à l’aide de l’en-tête `Ocp-Apim-Subscription-Key`. Ajoutez l’en-tête `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` à votre requête.

#### <a name="authenticating-with-a-global-resource"></a>Authentification avec une ressource globale

Lorsque vous utilisez une [ressource de traducteur globale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation), vous devez inclure un en-tête pour appeler le Translator.

|headers|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| La valeur est la clé secrète Azure pour votre abonnement à Translator.|

Voici un exemple de demande d’appel du Translator à l’aide de la ressource de traducteur globale

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Authentification avec une ressource régionale

Lorsque vous utilisez une [ressource de traducteur régionale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Il existe 2 en-têtes dont vous avez besoin pour appeler le Translator.

|headers|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| La valeur est la clé secrète Azure pour votre abonnement à Translator.|
|Ocp-Apim-Subscription-Region| La valeur est la région de la ressource du traducteur. |

Voici un exemple de demande d’appel du Translator à l’aide de la ressource de traducteur régional

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Authentification avec une ressource multiservice

Lorsque vous utilisez une ressource multiservice Cognitive Services. Vous pouvez ainsi utiliser une clé secrète unique pour authentifier les requêtes de plusieurs services.

Quand vous utilisez une clé secrète multiservice, vous devez inclure deux en-têtes d’authentification avec votre requête. Il existe 2 en-têtes dont vous avez besoin pour appeler le Translator.

|headers|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| La valeur est la clé secrète Azure pour votre ressource multiservice.|
|Ocp-Apim-Subscription-Region| La valeur est la région de la ressource multiservice. |

La région est obligatoire pour l’abonnement à l’API Texte multiservice. La région que vous sélectionnez est la seule région que vous pouvez utiliser pour la traduction de texte lors de l’utilisation de la clé d’abonnement multiservice. En outre, il doit s’agir de la même région que celle sélectionnée lors de la souscription à votre abonnement multiservice via le portail Microsoft Azure.

Les régions disponibles sont `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, `japaneast`, `japanwest`, `koreacentral`, `northcentralus`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, `westus2` et `southafricanorth`.

Si vous passez la clé secrète dans la chaîne de requête avec le paramètre `Subscription-Key`, vous devez spécifier la région avec le paramètre de requête `Subscription-Region`.

### <a name="authenticating-with-an-access-token"></a>S’authentifier avec un jeton d’accès

Vous pouvez également échanger votre clé secrète contre un jeton d’accès. Ce jeton est inclus avec chaque requête sous la forme de l’en-tête `Authorization`. Pour obtenir un jeton d’autorisation, exécutez une demande `POST` pour l’URL suivante :

| Type de ressource     | URL du service d’authentification                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Régional or multi-service | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Voici des exemples de demandes pour obtenir un jeton avec une clé secrète :

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Une demande réussie retourne le jeton d’accès codé sous forme de texte brut dans le corps de la réponse. Le jeton valide est transmis au service de traduction sous forme de jeton du porteur dans l’autorisation.

```http
Authorization: Bearer <Base64-access_token>
```

Un jeton d’authentification est valide pour une durée de 10 minutes. Le jeton doit être réutilisé lorsque vous effectuez plusieurs appels au Translator. Toutefois, si votre programme effectue des requêtes au Translator sur une période prolongée, votre programme doit demander un nouveau jeton d’accès à intervalles réguliers (par exemple, toutes les 8 minutes).

## <a name="authentication-with-azure-active-directory-azure-ad"></a>Authentification à l’aide d’Azure Active Directory (Azure AD)

 Traducteur 3.0 prend en charge l’authentification Azure AD, la solution de gestion des identités et des accès basée sur le cloud de Microsoft.  Les en-têtes d’autorisation permettent au service Traducteur de valider le fait que le client demandeur est autorisé à utiliser la ressource et à terminer la requête.

### <a name="prerequisites"></a>**Composants requis**

* Notions sur [**l’authentification avec Azure Active Directory**](../../authentication.md?tabs=powershell#authenticate-with-azure-active-directory).

* Notions sur [**l’autorisation d’accès aux identités managées**](../../authentication.md?tabs=powershell#authorize-access-to-managed-identities).

### <a name="headers"></a>**En-têtes**

|En-tête|Valeur|
|:-----|:----|
|Autorisation| La valeur est un **jeton du porteur** d’accès généré par Azure AD.</br><ul><li> Le jeton du porteur fournit une preuve d’authentification et valide l’autorisation du client pour utiliser la ressource.</li><li> Un jeton d’authentification est valide pendant 10 minutes et doit être réutilisé lors de l’exécution de plusieurs appels de Traducteur.</br></li>*Consultez*  [S’authentifier avec un jeton d’accès](#authenticating-with-an-access-token), précédemment. </ul>|
|Ocp-Apim-Subscription-Region| La valeur est la région de la **ressource du traducteur**.</br><ul><li> Cette valeur est facultative si la ressource est globale.</li></ul>|
|Ocp-Apim-ResourceId| La valeur est l’ID de ressource de votre instance de ressource Traducteur.</br><ul><li>Vous trouverez l’ID de ressource dans **Ressource Traducteur → Propriétés** dans le Portail Azure. </li><li>Format d’ID de ressource : </br>/subscriptions/<**subscriptionId**>/resourceGroups/<**nomGroupeRessources**>/providers/Microsoft.CognitiveServices/accounts/<**nomRessource**>/</li></ul>|

##### <a name="translator-property-pageazure-portal"></a>**Page de propriétés de Traducteur — Portail Azure**

:::image type="content" source="../media/managed-identities/resource-id-property.png" alt-text="Capture d’écran:Page de propriétés de Traducteur dans le Portail Azure.":::

### <a name="examples"></a>**Exemples**

#### <a name="using-the-global-endpoint"></a>**À l’aide du point de terminaison global**

```curl
 // Using headers, pass a bearer token generated by Azure AD, resource ID, and the region.

curl -X POST "https://api.cognitive.microsofttranslator.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Authorization: Bearer <Base64-access_token>"\
     -H "Ocp-Apim-ResourceId: <Resource ID>" \
     -H "Ocp-Apim-Subscription-Region: <your-region>" \
     -H "Content-Type: application/json" \
     -data-raw "[{'Text':'Hello, friend.'}]"
```

#### <a name="using-your-custom-endpoint"></a>**À l’aide de votre point de terminaison personnalisé**

```curl
// Using headers, pass a bearer token generated by Azure AD.

curl -X POST https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es \
     -H "Authorization: Bearer <Base64-access_token>"\
     -H "Content-Type: application/json" \
     -data-raw "[{'Text':'Hello, friend.'}]"
```

### <a name="examples-using-managed-identities"></a>**Exemple utilisant des identités managées**

Traducteur 3.0 prend également en charge l’autorisation d’accès aux identités managées. Si une identité managée est activée pour une ressource du traducteur, vous pouvez transmettre le jeton du porteur généré par l’identité managée dans l’en-tête de requête.

#### <a name="with-the-global-endpoint"></a>**Avec le point de terminaison global**

```curl
// Using headers, pass a bearer token generated either by Azure AD or Managed Identities, resource ID, and the region.

curl -X POST https://api.cognitive.microsofttranslator.com/translator/text/v3.0/translate?api-version=3.0&to=es \
     -H "Authorization: Bearer <Base64-access_token>"\
     -H "Ocp-Apim-ResourceId: <Resource ID>" \
     -H "Ocp-Apim-Subscription-Region: <your-region>" \
     -H "Content-Type: application/json" \
     -data-raw "[{'Text':'Hello, friend.'}]"
```

#### <a name="with-your-custom-endpoint"></a>**Avec votre point de terminaison personnalisé**

```curl
//Using headers, pass a bearer token generated by Managed Identities.

curl -X POST https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es \
     -H "Authorization: Bearer <Base64-access_token>"\
     -H "Content-Type: application/json" \
     -data-raw "[{'Text':'Hello, friend.'}]"
```

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels

Le service Translator est désormais disponible avec des capacités de réseau virtuel (VNET) dans toutes les régions du cloud public Azure. Pour activer le réseau virtuel, *consultez* [Configuration de réseaux virtuels Azure Cognitive Services](../../cognitive-services-virtual-networks.md?tabs=portal).

Après avoir activé cette capacité, vous devez utiliser le point de terminaison personnalisé pour appeler Translator. Vous ne pouvez pas utiliser le point de terminaison du traducteur global (« api.cognitive.microsofttranslator.com ») et vous ne pouvez pas vous authentifier avec un jeton d’accès.

Vous pouvez trouver le point de terminaison personnalisé après avoir créé une [ressource de traduction](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) et autorisé l’accès à partir des réseaux et points terminaux privés sélectionnés.

|headers|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| La valeur est la clé secrète Azure pour votre abonnement à Translator.|
|Ocp-Apim-Subscription-Region| La valeur est la région de la ressource du traducteur. Cette valeur est facultative si la ressource est `global`|

Voici un exemple de requête d’appel de Translator à l’aide du point de terminaison personnalisé

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Erreurs

Une réponse d’erreur standard est un objet JSON avec une paire nom/valeur nommée `error`. La valeur est également un objet JSON avec les propriétés :

  * `code`: code d’erreur défini par le serveur.
  * `message`: chaîne fournissant une représentation lisible de l’erreur.

Par exemple, un client avec un abonnement d’essai gratuit recevrait l’erreur suivante une fois le quota gratuit épuisé :

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```

Le code d’erreur est un nombre à 6 chiffres qui combine le code d’état HTTP à 3 chiffres et un nombre à 3 chiffres qui sert à catégoriser plus précisément l’erreur. Voici les codes d’erreur courants :

| Code | Description |
|:----|:-----|
| 400000| Une des requêtes d’entrées n’est pas valide.|
| 400001| Le paramètre « scope » n’est pas valide.|
| 400002| Le paramètre « category » n’est pas valide.|
| 400003| Un spécificateur de langage est manquant ou non valide.|
| 400004| Un spécificateur de script cible (« To script ») est manquant ou non valide.|
| 400005| Un texte d’entrée est manquant ou non valide.|
| 400006| La combinaison de langue et de script n’est pas valide.|
| 400018| Un spécificateur de script source (« From script ») est manquant ou non valide.|
| 400019| L’une des langues spécifiées n’est pas prise en charge.|
| 400020| L’un des éléments du tableau de texte d’entrée n’est pas valide.|
| 400021| Le paramètre de version d’API est manquant ou non valide.|
| 400023| L’une des paires de langues spécifiées n’est pas valide.|
| 400035| La langue source (champ « From ») n’est pas valide.|
| 400036| La langue cible (champ « To ») est manquante ou non valide.|
| 400042| L’une des options spécifiées (champ « Options ») n’est pas valide.|
| 400043| L’ID de trace client (champ de ClientTraceId ou en-tête de X-ClientTranceId) est manquant ou non valide.|
| 400050| Le texte d’entrée est trop long. Affichez les [limites de requête](../request-limits.md).|
| 400064| Le paramètre « translation » est manquant ou non valide.|
| 400070| Le nombre de scripts de cible (paramètre ToScript) ne correspond pas au nombre de langages cible (paramètre To).|
| 400071| La valeur n’est pas valide pour TextType.|
| 400072| Le tableau de texte d’entrée compte trop d’éléments.|
| 400073| Le paramètre de script n’est pas valide.|
| 400074| Le corps de la requête n’est pas un élément JSON valide.|
| 400075| La combinaison de paire de langue et de catégorie n’est pas valide.|
| 400077| La taille de requête maximale a été dépassée. Affichez les [limites de requête](../request-limits.md).|
| 400079| Le système personnalisé demandé pour la traduction entre le langage source et le langage cible n’existe pas.|
| 400080| La translittération n’est pas prise en charge pour la langue ou le script.|
| 401000| La requête n’est pas autorisée car les informations d’identification sont manquantes ou non valides.|
| 401015| « Les informations d’identification fournies sont valables pour l’API Speech. Cette requête nécessite des informations d’identification pour l’API Texte. Utilisez un abonnement à Translator. »|
| 403000| L’opération n’est pas autorisée.|
| 403001| L’opération n’est pas autorisée, car l’abonnement a dépassé son quota gratuit.|
| 405000| La méthode de requête n’est pas prise en charge pour la ressource demandée.|
| 408001| Le système de traduction demandé est en cours de préparation. Réessayez dans quelques minutes.|
| 408002| Le délai d’attente de la requête a expiré sur le flux entrant. Le client n’a pas produit de requête dans la limite du délai pendant lequel le serveur était préparé à attendre. Le client peut répéter la requête sans modification à tout moment.|
| 415000| L’en-tête Content-Type est manquante ou invalide.|
| 429000, 429001, 429002| Le serveur a rejeté la requête, car le client a dépassé les limites de requête.|
| 500000| Une erreur inattendue s’est produite. Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de l’erreur, identificateur de la demande dans l’en-tête de réponse X-RequestId, et identificateur du client dans l’en-tête de demande X-ClientTraceId.|
| 503000| Le service est temporairement indisponible. Reprise Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de l’erreur, identificateur de la demande dans l’en-tête de réponse X-RequestId, et identificateur du client dans l’en-tête de demande X-ClientTraceId.|

## <a name="metrics"></a>Mesures
Les métriques vous permettent de voir des informations sur l’utilisation et la disponibilité du traducteur dans le portail Azure, sous la section des métriques, comme illustré dans la capture d’écran ci-dessous. Pour plus d’informations, consultez [Métriques de données et de plateforme](../../../azure-monitor/essentials/data-platform-metrics.md).

![Métriques de traducteur](../media/translatormetrics.png)

Ce tableau liste les métriques disponibles et décrit la façon dont elles sont utilisées pour superviser les appels d’API de traduction.

| Mesures | Description |
|:----|:-----|
| TotalCalls| Nombre total d’appels d’API.|
| TotalTokenCalls| Nombre total d’appels d’API par le biais du service de jeton qui utilisent un jeton d’authentification.|
| SuccessfulCalls| Nombre d’appels réussis.|
| TotalErrors| Nombre d’appels avec réponse d’erreur.|
| BlockedCalls| Nombre d’appels ayant dépassé la limite de débit ou de quota.|
| ServerErrors| Nombre d’appels avec erreur interne du serveur (5XX).|
| ClientErrors| Nombre d’appels avec erreur côté client (4XX).|
| Latence| Durée d’exécution de la demande en millisecondes.|
| CharactersTranslated| Nombre total de caractères dans la requête de texte entrante.|
