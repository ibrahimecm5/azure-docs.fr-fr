---
title: Utiliser les anomalies SOC-ML pour détecter les menaces dans Microsoft Sentinel | Microsoft Docs
description: Cet article explique comment utiliser les nouvelles capacités de détection d’anomalie SOC-ML dans Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 233f83083a061bf12caae58172d2c80bab876fcf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519835"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-microsoft-sentinel"></a>Utiliser des anomalies SOC-ML pour détecter les menaces dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Les anomalies SOC-ML sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="what-are-soc-ml-anomalies"></a>Que sont les anomalies SOC-ML ?

Avec les attaquants et les défenseurs qui luttent constamment pour prendre l’avantage dans la course aux armements de la cybersécurité, les attaquants trouvent toujours des moyens d’échapper à la détection. Inévitablement, toutefois, les attaques entraînent toujours un comportement inhabituel dans les systèmes attaqués. Les anomalies basées sur le Machine Learning SOC-ML de Microsoft Sentinel peuvent identifier ce comportement à l’aide de modèles de règle d’analyse qui peuvent être implémentés sans configuration supplémentaire. Même si les anomalies n’indiquent pas nécessairement un comportement malveillant ou suspect, elles peuvent être utilisées pour améliorer les détections, les investigations et la recherche de menaces :

- **Signaux supplémentaires pour améliorer la détection** : les analystes de sécurité peuvent utiliser les anomalies pour détecter les nouvelles menaces et rendre les détections existantes plus efficaces. Une anomalie seule n’est pas un signal fort de comportement malveillant, mais lorsqu’elle est associée à plusieurs anomalies qui se produisent à différents moments de la chaîne de destruction, leur effet cumulatif est bien plus fort. Les analystes de sécurité peuvent également améliorer les détections existantes en faisant en sorte que le comportement inhabituel identifié par les anomalies soit une condition de déclenchement d’alertes.

- **Preuve au cours des investigations** : les analystes de sécurité peuvent également utiliser les anomalies au cours des investigations pour vous aider à confirmer une violation, trouver de nouveaux chemins d’accès pour l’examiner et évaluer son impact potentiel. Ces gains d’efficacité réduisent le temps que les analystes de sécurité consacrent aux investigations.

- **Le début des recherches proactives de menaces** : les chasseurs de menaces peuvent utiliser les anomalies comme contexte pour déterminer si leurs requêtes présentent un comportement suspect non couvert. Lorsque le comportement est suspect, les anomalies pointent également vers des directions à suivre potentielles en vue d’une nouvelle chasse. Ces indices fournis par les anomalies réduisent le temps nécessaire à la détection d’une menace et son risque de nuire.

Les anomalies peuvent être des outils puissants, mais elles sont notoirement très bruyantes. Elles nécessitent généralement beaucoup de paramétrages fastidieux pour des environnements spécifiques ou un traitement complexe. Les modèles d’anomalies SOC-ML pour Microsoft Sentinel sont réglés par notre équipe de science des données de sorte à être prêts à l’emploi, mais si vous devez les adapter davantage, le processus est simple et ne nécessite aucune connaissance en Machine Learning. Les seuils et les paramètres de la plupart des anomalies peuvent être configurés et affinés par le biais de l’interface utilisateur de règles d’analyse qui vous est déjà familière. Les performances du seuil et des paramètres d’origine peuvent être comparées aux nouvelles valeurs de l’interface et être réglées en fonction des besoins au cours d’une phase de test ou d’évaluation. Une fois que l’anomalie atteint les objectifs de performance, l’anomalie avec le nouveau seuil ou les nouveaux paramètres peut être promue en production simplement en cliquant sur un bouton. Les anomalies SOC-ML pour Microsoft Sentinel vous permettent de tirer parti des anomalies sans avoir à effectuer le travail difficile.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert la façon dont SOC-ML vous aide à détecter les anomalies dans Microsoft Sentinel.

- Découvrez comment [afficher, créer, gérer et affiner les règles d’anomalie](work-with-anomaly-rules.md).
- En savoir plus sur les [autres types de règles d’analyse](detect-threats-built-in.md).
