---
titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
ms.openlocfilehash: 2d26c560cd05aad4962d318e8c2c798c576db811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "131011645"
---
Si vous rencontrez des problèmes lors de l’exécution d’un conteneur Cognitive Services, essayez d’utiliser le conteneur de diagnostics Microsoft. Utilisez ce conteneur pour diagnostiquer dans votre environnement de déploiement les erreurs courantes pouvant nuire au bon fonctionnement des conteneurs Cognitive Services.

Pour obtenir le conteneur, utilisez la commande Docker `pull` suivante :

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

Exécutez le conteneur, puis remplacez `{ENDPOINT_URI}` par votre point de terminaison et `{API_KEY}` par la clé de votre ressource :

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Le conteneur teste la connectivité réseau avec le point de terminaison de facturation.