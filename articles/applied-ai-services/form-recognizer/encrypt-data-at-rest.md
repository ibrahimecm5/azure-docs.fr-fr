---
title: Chiffrement Form Recognizer des données au repos
titleSuffix: Azure Applied AI Services
description: Microsoft propose des clés de chiffrement gérées par Microsoft et vous permet également de gérer vos abonnements Cognitive Services à l’aide de vos propres clés, appelées clés gérées par le client (CMK). Cet article traite du chiffrement des données au repos pour Form Recognizer et de l’activation et de la gestion de CMK.
author: erindormier
manager: venkyv
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: fad7440c82ee22a370bfecc13c60f3d4d4828b07
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122534934"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Chiffrement Form Recognizer des données au repos

Azure Form Recognizer chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement Form Recognizer protège vos données et vous aide à répondre aux engagements de votre organisation en matière de sécurité et de conformité.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Les clés gérées par le client sont des ressources disponibles créées après le 11 mai 2020 uniquement. Pour utiliser une clé gérée par le client (CMK, Customer-Managed Key) avec Form Recognizer, vous devez créer une ressource Form Recognizer. Une fois la ressource créée, vous pouvez utiliser Azure Key Vault pour configurer votre identité managée.

[!INCLUDE [cognitive-services-cmk](../../cognitive-services/includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Formulaire de demande de clé gérée par le client du service Form Recognizer](https://aka.ms/cogsvc-cmk)
* [En savoir plus sur Azure Key Vault](../../key-vault/general/overview.md)