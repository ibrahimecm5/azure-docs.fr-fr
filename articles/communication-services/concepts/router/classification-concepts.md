---
title: Concepts relatifs à la classification des travaux pour Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Découvrez les concepts relatifs à la classification de Job Router d’Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: c31c0831f625f65075f3c1f5cde2647ebc73ce64
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308424"
---
# <a name="job-classification-concepts"></a>Concepts relatifs à la classification des travaux

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Job Router d’Azure Communication Services utilise un processus appelé **classification** lorsqu’un travail est soumis. Cet article décrit les différentes façons dont un travail peut être classé et l’effet de ce processus.

## <a name="job-classification-overview"></a>Vue d’ensemble de la classification des travaux

Job Router utilise deux méthodes principales pour classer un travail : la méthode statique et la méthode dynamique. Si l’application appelante possède des informations sur l’ID de file d’attente, la priorité ou les sélecteurs de collaborateurs, le travail peut être envoyé sans stratégie de classification : il s’agit de la **classification statique**. Si vous préférez laisser Job Router décider de l’ID de file d’attente, une stratégie de classification peut être utilisée pour modifier les propriétés du travail. On appelle ce processus **classification dynamique**.

Lorsque vous soumettez un travail avec le kit de développement logiciel (SDK, Software Development Kit) Job Router, un événement est envoyé à votre abonnement Azure Communication Services Event Grid du fait du processus de classification. Les événements générés dans le cadre du cycle de vie de la classification fournissent des insights sur les actions effectuées par Job Router. Par exemple, une classification réussie produit un événement **RouterJobClassified**, une défaillance un événement **RouterJobClassificationFailed**.

Le processus de classification d’un travail implique éventuellement de définir les propriétés suivantes :

- ID de file d’attente
- Priority
- Sélecteurs de collaborateurs

## <a name="static-classification"></a>Classification statique

Le fait d’envoyer un travail avec un ID de file d’attente, une priorité et des sélecteurs de collaborateurs prédéfinis vous permet de commencer rapidement. Job Router ne modifie pas ces propriétés une fois le travail envoyé, sauf si vous le mettez à jour en spécifiant une stratégie de classification avant l’attribution à un collaborateur. Vous pouvez modifier la propriété de stratégie de classification d’un travail après l’envoi, ce qui déclenche le processus de classification dynamique.

> [!NOTE]
> Vous avez la possibilité de remplacer le résultat de la classification dynamique en utilisant le kit SDK Job Router pour mettre à jour les propriétés du travail manuellement. Par exemple, vous pouvez spécifier initialement un ID de file d’attente statique, puis modifier le travail avec un ID de stratégie de classification pour le classer de manière dynamique, et enfin remplacer l’ID de file d’attente.

## <a name="dynamic-classification"></a>Classification dynamique

Si vous spécifiez une stratégie de classification lorsque vous envoyez un travail, Job Router peut affecter dynamiquement l’ID de file d’attente et la priorité, et éventuellement modifier les sélecteurs de collaborateurs. Ce type de classification est intéressant dans la mesure où l’application appelante n’a pas besoin de connaître les propriétés du travail (pas même l’ID de file d’attente) au moment de l’exécution.

### <a name="queue-selectors"></a>Sélecteurs de file d’attente

Une stratégie de classification peut faire référence à un `QueueSelector`, qui est utilisé par le processus de classification dans le but de déterminer l’ID de file d’attente à choisir pour un travail particulier. Les types `QueueSelector` suivants, disponibles dans Job Router, constituent des options applicables au processus de sélection de file d’attente pendant la classification :

**QueueLabelSelector** : lorsque vous créez une file d’attente Job Router, vous pouvez spécifier des étiquettes pour faciliter le processus de sélection de file d’attente lors de la classification des travaux. Ce type de sélecteur emploie une collection de types `LabelSelectorAttachment` pour offrir la plus grande flexibilité possible lors de la sélection de la file d’attente pendant le processus de classification. Utilisez ce sélecteur pour permettre au processus de classification des travaux de sélectionner l’ID de file d’attente en fonction de ses étiquettes. Pour plus d'informations, consultez la section [ci-dessous](#using-labels-and-selectors-in-classification).

**QueueIdSelector** : ce sélecteur permet d’utiliser l’un des nombreux moteurs de règles pour déterminer l’ID de file d’attente du travail en fonction du résultat de la règle. Pour plus d’informations, consultez la page [Concepts de RouterRule](router-rule-concepts.md).

### <a name="worker-selectors"></a>Sélecteurs de collaborateurs

Dans une stratégie de classification, un sélecteur de collaborateurs contient une collection de types `LabelSelectorAttachment`, utilisée par le processus de classification pour attacher des sélecteurs de collaborateurs à un travail en fonction de ses étiquettes. Pour plus d'informations, consultez la section [ci-dessous](#using-labels-and-selectors-in-classification).

### <a name="prioritization-rule"></a>Règle de priorité

La priorité d’un travail peut être résolue lors de la classification à l’aide de l’un des nombreux moteurs de règles, suivant un fonctionnement similaire à celui de `QueueIdSelector`. Pour plus d’informations, consultez la page [Concepts de RouterRule](router-rule-concepts.md).

## <a name="using-labels-and-selectors-in-classification"></a>Utilisation d’étiquettes et de sélecteurs dans la classification

Job Router utilise les « étiquettes » de paire clé/valeur d’un travail, d’un collaborateur et d’une file d’attente pour prendre diverses décisions sur le routage. Utilisé sur un `QueueSelector`, `LabelSelectorAttachment` fonctionne comme un filtre. Dans le contexte de `WorkerSelectors`, en revanche, il joint des sélecteurs à l’ensemble qui a été créé initialement avec le travail. Peuvent être utilisés les types `LabelSelectorAttachment` suivants :

**Sélecteur d’étiquette statique** : attache toujours le `LabelSelector` donné.

**Sélecteur d’étiquette conditionnel** : évalue une condition définie par une règle.  Si la valeur obtenue est `true`, la collection de sélecteurs spécifiée est appliquée.

**Sélecteur d’étiquette passthrough** : utilise une clé et `LabelOperator` pour vérifier l’existence de la clé. Ce sélecteur peut être utilisé dans `QueueLabelSelector` pour mettre en correspondance une file d’attente en fonction de l’ensemble d’étiquettes. Utilisée avec des `WorkerSelectors`, la paire clé/valeur du travail est attachée aux `WorkerSelectors` du travail.

**Sélecteur d’étiquette de règle** : récupère une collection de sélecteurs auprès de l’un des nombreux moteurs de règles. Pour plus d’informations, consultez la page [Concepts de RouterRule](router-rule-concepts.md).

**Sélecteur d’étiquette à allocation pondérée** : utilise une collection d'objets `WeightedAllocation` qui spécifient chacun une pondération en pourcentage et une collection de sélecteurs à appliquer en fonction de l’allocation des pondérations. Il est par exemple possible de définir que 30 % des travaux sont envoyés à « Contoso » et les 70 % restants à « Fabrikam ».

## <a name="reclassifying-a-job"></a>Reclassification d’un travail
Un travail classé peut être reclassé de différentes manières :

1. Vous pouvez mettre à jour les étiquettes du travail, ce qui amène Job Router à les évaluer avec la stratégie de classification précédente.
2. Vous pouvez mettre à jour l’ID de stratégie de classification d’un travail, ce qui amène Job Router à traiter le travail existant par rapport à la nouvelle stratégie.
3. Un **déclencheur** de stratégie d’exception peut effectuer **l’action** de demande de reclassification d’un travail. 

> [!NOTE]
> Le kit SDK Job Router comprend une méthode `UpdateJobLabels` qui met simplement à jour les étiquettes sans obliger Job Router à exécuter le processus de reclassification.
