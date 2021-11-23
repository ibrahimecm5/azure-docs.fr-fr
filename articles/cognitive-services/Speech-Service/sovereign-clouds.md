---
title: Clouds souverains – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les clouds souverains
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 11/09/2021
ms.author: alexeyo
ms.openlocfilehash: ae288de8ae05efc22534cfaf87c42261a5e6d59a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135803"
---
# <a name="speech-services-in-sovereign-clouds"></a>Services Speech dans des clouds souverains

## <a name="azure-government-united-states"></a>Azure Government (États-Unis)

Disponible uniquement pour les entités gouvernementales américaines et leurs partenaires. Pour plus d’informations sur Azure Government, consultez [cette page](../../azure-government/documentation-government-welcome.md) et [cette page.](../../azure-government/compare-azure-government-global-azure.md)

- **Portail Azure :**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Régions :**
  - Gouvernement des États-Unis – Arizona
  - Gouvernement américain - Virginie
- **Niveaux tarifaires disponibles** :
  - Gratuit (F0) et Standard (S0). Vous trouverez plus de détails [ici](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Fonctionnalités prises en charge :**
  - Reconnaissance vocale
    - Reconnaissance vocale personnalisée (modèle acoustique (AM) et modèle de langage (LM))
      - [Speech Studio](https://speech.azure.us/)
  - Synthèse vocale
    - Voix standard
    - Voix neuronale
  - Traduction vocale
- **Fonctionnalités non prises en charge :**
  - Custom Voice
- **Langues prises en charge :**
  - Consultez la liste des langues prises en charge [ici](language-support.md).

### <a name="endpoint-information"></a>Informations sur le point de terminaison

Cette section contient des informations sur le point de terminaison des services Speech pour utilisation avec le [SDK Speech](speech-sdk.md), l’[API REST de reconnaissance vocale](rest-speech-to-text.md) et l’[API REST de synthèse vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST Speech Services

Les points de terminaison de l’API REST des services Speech dans Azure Government ont le format suivant :

|  Type/opération de l’API REST | Format du point de terminaison |
|--|--|
| Access token (Jeton d’accès) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [API REST de synthèse vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Remplacez `<REGION_IDENTIFIER>` par l'identificateur correspondant à la région de votre abonnement dans le tableau suivant :

|                     | Identificateur de la région |
|--|--|
| **Gouvernement des États-Unis – Arizona**  | `usgovarizona` |
| **Gouvernement américain - Virginie** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Pour le [SDK Speech](speech-sdk.md) dans les clouds souverains, vous devez utiliser l’instanciation « from host / with host » de la classe `SpeechConfig` ou l’option `--host` de l’[interface CLI Speech](spx-overview.md). (Vous pouvez également utiliser l’instanciation « from endpoint / with endpoint » et l’option `--endpoint` d’interface CLI Speech).

La classe `SpeechConfig` doit être instanciée comme suit :

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(usGovHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(usGovHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=usGovHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:usGovHost subscription:subscriptionKey];
```
***

L’interface CLI Speech doit être utilisée comme ceci (notez l’option `--host`) :
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Remplacez `subscriptionKey` par votre clé de ressource Speech. Remplacez `usGovHost` par l’expression qui correspond à l’offre de service requise et la région de votre abonnement à partir de cette table :

|  Région/offre de service | Expression hôte |
|--|--|
| **Gouvernement des États-Unis – Arizona** | |
| Reconnaissance vocale | `wss://usgovarizona.stt.speech.azure.us` |
| Synthèse vocale | `https://usgovarizona.tts.speech.azure.us` |
| **Gouvernement américain - Virginie** | |
| Reconnaissance vocale | `wss://usgovvirginia.stt.speech.azure.us` |
| Synthèse vocale | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure Chine

Disponible pour les organisations ayant une présence commerciale en Chine. Pour plus d’informations sur Azure Chine, [cliquez ici](/azure/china/overview-operations). 


- **Portail Azure :**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Régions :**
  - Chine orientale 2
  - Chine Nord 2
- **Niveaux tarifaires disponibles** :
  - Gratuit (F0) et Standard (S0). Vous trouverez plus de détails [ici](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Fonctionnalités prises en charge :**
  - Reconnaissance vocale
    - Reconnaissance vocale personnalisée (modèle acoustique (AM) et modèle de langage (LM))
      - [Speech Studio](https://speech.azure.cn/)
  - Synthèse vocale
    - Voix standard
    - Voix neuronale
  - Traduction vocale
- **Fonctionnalités non prises en charge :**
  - Custom Voice
- **Langues prises en charge :**
  - Consultez la liste des langues prises en charge [ici](language-support.md).

### <a name="endpoint-information"></a>Informations sur le point de terminaison

Cette section contient des informations sur le point de terminaison des services Speech pour utilisation avec le [SDK Speech](speech-sdk.md), l’[API REST de reconnaissance vocale](rest-speech-to-text.md) et l’[API REST de synthèse vocale](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>API REST Speech Services

Les points de terminaison de l’API REST des services Speech dans Azure Chine ont le format suivant :

|  Type/opération de l’API REST | Format du point de terminaison |
|--|--|
| Access token (Jeton d’accès) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [API REST de synthèse vocale](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Remplacez `<REGION_IDENTIFIER>` par l'identificateur correspondant à la région de votre abonnement dans le tableau suivant :

|                     | Identificateur de la région |
|--|--|
| **Chine orientale 2**  | `chinaeast2` |
| **Chine Nord 2**  | `chinanorth2` |

#### <a name="speech-sdk"></a>Kit de développement logiciel (SDK) de reconnaissance vocale

Pour le [SDK Speech](speech-sdk.md) dans les clouds souverains, vous devez utiliser l’instanciation « from host / with host » de la classe `SpeechConfig` ou l’option `--host` de l’[interface CLI Speech](spx-overview.md). (Vous pouvez également utiliser l’instanciation « from endpoint / with endpoint » et l’option `--endpoint` d’interface CLI Speech).

La classe `SpeechConfig` doit être instanciée comme suit :

# <a name="c"></a>[C#](#tab/c-sharp)
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
# <a name="c"></a>[C++](#tab/cpp)
```cpp
auto config = SpeechConfig::FromHost(azCnHost, subscriptionKey);
```
# <a name="java"></a>[Java](#tab/java)
```java
SpeechConfig config = SpeechConfig.fromHost(azCnHost, subscriptionKey);
```
# <a name="python"></a>[Python](#tab/python)
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(host=azCnHost, subscription=subscriptionKey)
```
# <a name="objective-c"></a>[Objective-C](#tab/objective-c)
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithHost:azCnHost subscription:subscriptionKey];
```
***

L’interface CLI Speech doit être utilisée comme ceci (notez l’option `--host`) :
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Remplacez `subscriptionKey` par votre clé de ressource Speech. Remplacez `azCnHost` par l’expression qui correspond à l’offre de service requise et la région de votre abonnement à partir de cette table :

|  Région/offre de service | Expression hôte |
|--|--|
| **Chine orientale 2** | |
| Reconnaissance vocale | `wss://chinaeast2.stt.speech.azure.cn` |
| Synthèse vocale | `https://chinaeast2.tts.speech.azure.cn` |
| **Chine Nord 2** | |
| Reconnaissance vocale | `wss://chinanorth2.stt.speech.azure.cn` |
| Synthèse vocale | `https://chinanorth2.tts.speech.azure.cn` |