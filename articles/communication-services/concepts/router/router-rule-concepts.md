---
title: Concepts du moteur de règles du routeur pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts du moteur de règles du routeur de travaux Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5d53f2492888323107fa539283f128ed6b6ebd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319222"
---
# <a name="job-router-rules-engine-concepts"></a>Concepts du moteur de règles du routeur de travaux

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Le routeur de travaux Azure Communication Services utilise un moteur de règles extensible pour traiter les données et prendre des décisions concernant vos travaux et vos Workers. Ce document décrit l’action du moteur de règles et explique pourquoi l’appliquer à votre implémentation.

## <a name="rules-engine-overview"></a>Vue d’ensemble du moteur de règles

Le contrôle du comportement de votre implémentation peut souvent inclure une prise de décision complexe. Le routeur de travaux offre un moyen flexible d’appeler le comportement programmatiquement à l’aide de divers moteurs de règles. Les moteurs de règles d’un routeur de travaux prennent généralement un ensemble **d’étiquettes** définies sur des objets comme un travail, une file d’attente ou un Worker en tant qu’entrée, ils appliquent la règle et produisent une sortie.

> [!NOTE]
> Bien que le moteur de règles utilise généralement des étiquettes comme entrée, il peut également définir des valeurs comme un ID de file d’attente sans utiliser l’évaluation des étiquettes.

En fonction de l’endroit où vous appliquez des règles dans le routeur de travaux, le résultat peut varier. Par exemple, une stratégie de classification peut choisir un ID de file d’attente en fonction des étiquettes définies sur l’entrée d’un travail. Dans un autre exemple, une stratégie de distribution peut trouver le meilleur Worker à l’aide d’une règle de score personnalisée définie par `RouterRule`.

### <a name="example-use-a-static-rule-in-a-classification-policy-to-set-the-queue-id"></a>Exemple : utiliser une règle statique dans une stratégie de classification pour définir l’ID de file d’attente

Dans cet exemple, on utilise `StaticRule`, qui est un type de `RouterRule` pouvant être utilisé pour définir l’ID de file d’attente de tous les travaux, qui référence l’ID de stratégie de classification `XBOX_QUEUE_POLICY`.

```csharp
await client.SetClassificationPolicyAsync(
    id: "XBOX_QUEUE_POLICY",
    queueSelector: new QueueIdSelector(new StaticRule("XBOX"))
)
```
## <a name="routerrule-types"></a>Types RouterRule

Les types `RouterRule` suivants existent dans le routeur de travaux pour garantir la flexibilité de traitement de vos travaux.

**Règle statique** : cette règle peut être utilisée pour spécifier une valeur statique, comme la sélection d’un ID de file d’attente spécifique.

**Règle d’expression** : une règle d’expression utilise le langage [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) pour définir votre règle en tant qu’expression inline.

**Règle de fonction Azure** : si vous spécifiez un URI et une valeur `AzureFunctionRuleCredential`, cette règle permet au routeur de travaux de transmettre les étiquettes d’entrée en tant que charge utile et de répondre avec une valeur de sortie. Ce type de règle peut être utilisé quand vos exigences sont complexes.

> [!NOTE]
> Bien que la **règle de carte directe** fasse partie du kit SDK du routeur de travaux, elle est uniquement prise en charge dans le `NearestQueueLabelSelector` pour le moment.
