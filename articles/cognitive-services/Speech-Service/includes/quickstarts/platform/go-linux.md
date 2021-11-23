---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 9efcbb7b42fd9bc2a44211b765dfd9254623d5ce
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252304"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour GO sur Linux. 

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux : consultez la liste des [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* [Binaire Go (1.13 ou version ultérieure)](https://golang.org/dl/)
* Configuration de l’environnement Linux en fonction de la [configuration système demandée et des instructions d’installation](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk).


[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Configurer l’environnement Go

Effectuez les étapes suivantes afin de configurer votre environnement Go pour qu’il trouve le SDK Speech. Dans ces deux étapes, remplacez `<architecture>` par l’architecture de votre processeur. L’architecture sera `x86`, `x64`, `arm32` ou `arm64`.

1. Étant donné que les liaisons s’appuient sur `cgo`, vous devez définir les variables d’environnement pour que Go puisse trouver le kit de développement logiciel (SDK) :

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Pour exécuter des applications, y compris le SDK, nous devons indiquer au système d’exploitation où trouver les bibliothèques :

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list-go.md)]
