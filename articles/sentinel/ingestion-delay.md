---
title: Gestion du délai d’ingestion dans Microsoft Sentinel | Microsoft Docs
description: Gérez le délai d’ingestion dans les règles d’analytique planifiée de Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2021
ms.author: bagol
ms.openlocfilehash: 41143675d54e4353f7ea5402a4503c78a74a12e2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522932"
---
# <a name="handle-ingestion-delay-in-scheduled-analytics-rules"></a>Gestion du délai d’ingestion dans les règles d’analytique planifiée

Microsoft Sentinel peut ingérer des données provenant de [différentes sources](connect-data-sources.md), mais le temps d’ingestion de chacune peut varier selon les circonstances.

Cet article explique quel impact peut avoir le délai d’ingestion sur les règles d’analytique planifiée et comment les corriger pour combler ces lacunes.

## <a name="why-delay-is-significant"></a>Importance du délai

Supposons par exemple que vous écriviez une règle de détection personnalisée, en définissant les champs **Exécuter la requête toutes les** et **Rechercher les données des dernières** de sorte qu’elle s’exécute toutes les cinq minutes et recherche les données des cinq dernières minutes :

:::image type="content" source="media/ingestion-delay/create-rule.png" alt-text="Capture d’écran montrant la fenêtre Assistant règle d’analytique – Créer une règle." border="false":::

Le champ **Rechercher les données des dernières** définit un paramètre appelé période de *« rétrospection »* . Dans l’idéal, lorsqu’il n’y a aucun délai, cette détection ne manque aucun événement, comme le montre le diagramme suivant :

:::image type="content" source="media/ingestion-delay/look-back.png" alt-text="Diagramme montrant une fenêtre de rétrospection de cinq minutes." border="false":::

L’événement arrive dès qu’il est généré. Il est inclus dans la période de *rétrospection*.

Maintenant, supposons qu’il existe un certain délai pour votre source de données, par exemple que l’événement a été *ingéré* deux minutes après sa *génération*. Le délai est donc de deux minutes :

:::image type="content" source="media/ingestion-delay/look-back-delay.png" alt-text="Diagramme montrant des fenêtres de rétrospection de cinq minutes avec un délai de deux minutes." border="false":::

L’événement est généré au cours de la première période de rétrospection, mais n’est pas ingéré dans votre espace de travail Microsoft Sentinel à la première exécution. Lors de son exécution suivante, la requête planifiée ingère l’événement. Cependant, le filtre de l’heure de génération le supprime, car il s’est produit il y a plus de cinq minutes. Dans ce cas, **la règle ne déclenche pas d’alerte**.

## <a name="how-to-handle-delay"></a>Gestion du délai

Pour résoudre le problème, vous devez connaître le délai de votre type de données. Dans cet exemple, vous savez déjà qu’il est de deux minutes. 

Dans le cas de vos propres données, vous pouvez déterminer le délai en utilisant la fonction Kusto `ingestion_time()` et en calculant la différence entre **TimeGenerated** et l’heure d’ingestion. Pour plus d’informations, consultez [Calcul du délai d’ingestion](#calculate-ingestion-delay).

Une fois que vous avez déterminé le délai, vous pouvez résoudre le problème comme suit :

- **Augmentez la période de rétrospection**. L’intuition de base vous indique qu’une augmentation de la taille de la période de rétrospection est utile. Étant donné que votre période de rétrospection est de cinq minutes et votre délai de deux minutes, une période de rétrospection de *sept* minutes permet de résoudre ce problème. Vous pouvez par exemple la définir ainsi dans vos paramètres de règle :

    :::image type="content" source="media/ingestion-delay/set-look-back.png" alt-text="Capture d’écran montrant comment définir la fenêtre de rétrospection sur sept minutes.":::

    Le diagramme suivant illustre la période de rétrospection qui contient maintenant l’événement manqué :

    :::image type="content" source="media/ingestion-delay/longer-look-back.png" alt-text="Diagramme montrant des fenêtres de rétrospection de sept minutes avec un délai de deux minutes." border="false":::

- **Gérez la duplication**. Seule l’augmentation de la période de rétrospection peut créer une duplication, car les fenêtres de rétrospection se chevauchent maintenant. Par exemple, un événement différent peut se présenter comme dans le diagramme suivant :

    :::image type="content" source="media/ingestion-delay/overlapping-look-back.png" alt-text="Diagramme montrant comment les fenêtres de rétrospection en chevauchement créent une duplication." border="false":::

    Étant donné que la valeur **TimeGenerated** de l’événement figure dans les deux périodes de rétrospection, l’événement déclenche deux alertes. Vous devez trouver un moyen de résoudre la duplication.

- **Associez l’événement à une période de rétrospection spécifique**. Dans le premier exemple, vous avez manqué des événements, car vos données n’étaient pas ingérées lors de l’exécution de la requête planifiée. Vous avez étendu la période de rétrospection de façon à inclure l’événement, mais cela a provoqué une duplication. Vous devez associer l’événement à la fenêtre que vous avez étendue pour le contenir.

    Pour ce faire, définissez `ingestion_time() > ago(5m)` la place de la règle d’origine `look-back = 5m`. Ce paramètre associe l’événement à la première fenêtre de rétrospection. Par exemple :

    :::image type="content" source="media/ingestion-delay/ago-restriction.png" alt-text="Diagramme montrant comment la restriction ago permet d’éviter la duplication." border="false":::

    La restriction du temps d’ingestion découpe maintenant les deux minutes supplémentaires que vous avez ajoutées à la période de rétrospection. Dans le premier exemple, la deuxième exécution de la période de rétrospection capture maintenant l’événement :

    :::image type="content" source="media/ingestion-delay/ago-restriction-capture.png" alt-text="Diagramme montrant comment la restriction ago permet de capturer l’événement." border="false":::

L’exemple de requête suivant résume la solution à appliquer pour résoudre les problèmes de délai d’ingestion :

```kusto
let ingestion_delay = 2min;
let rule_look_back = 5min;
CommonSecurityLog
| where TimeGenerated >= ago(ingestion_delay + rule_look_back)
| where ingestion_time() > ago(rule_look_back)
```


## <a name="calculate-ingestion-delay"></a>Calcul du délai d’ingestion

Par défaut, les règles d’alerte planifiées de Microsoft Sentinel sont configurées pour présenter une période de rétrospection de cinq minutes. Toutefois, chaque source de données peut posséder son propre délai d’ingestion. Lorsque vous joignez plusieurs types de données, vous devez connaître le délai de chacun pour configurer correctement la période de rétrospection.

Le **Rapport d’utilisation des espaces de travail**, fourni prêt à l’emploi dans Microsoft Sentinel, comprend un tableau de bord qui indique la latence et le délai des différents types de données qui transitent dans l’espace de travail.

Par exemple :

:::image type="content" source="media/ingestion-delay/end-to-end-latency.png" alt-text="Capture d’écran du Rapport d’utilisation des espaces de travail montrant la latence de bout en bout par table.":::


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md)
- [Personnaliser des détails d’alerte dans Azure Sentinel](customize-alert-details.md)
- [Gérer des versions de modèles pour vos règles d’analyse planifiée dans Azure Sentinel](manage-analytics-rule-templates.md)