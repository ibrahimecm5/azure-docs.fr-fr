---
title: Échec de pipeline et message d’erreur
description: Comprendre comment l’état d’échec de pipeline et le message d’erreur sont déterminés
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 746b8d0b3b24901469f46090adf25b02f786dcef
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063991"
---
# <a name="understanding-pipeline-failure"></a>Description de l’échec de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="error-handling"></a>Gestion des erreurs

Une orchestration Azure Data Factory et Synapse autorise une logique conditionnelle et permet à l’utilisateur d’emprunter différents chemins en fonction des résultats d’une activité précédente. L’utilisation de différents chemins permet aux utilisateurs de créer des pipelines robustes et d’incorporer la gestion des erreurs dans la logique ETL/ELT. Au total, nous autorisons quatre chemins conditionnels :

* En cas de réussite (chemin par défaut)
* En cas d’échec
* Une fois l’opération terminée
* En cas d’omission

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-1-four-branches.png" alt-text="Capture d’écran montrant les quatre branches d’une activité.":::

### <a name="common-error-handling-mechanism"></a>Mécanisme de gestion des erreurs courantes

#### <a name="try-catch-block"></a>Bloc Try-Catch

Dans cette approche, le client définit la logique métier et spécifie uniquement le chemin _En cas d’échec_ pour intercepter toute erreur de l’activité précédente. Cette approche entraîne la réussite du pipeline si le chemin _En cas d’échec_ réussit.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-2-try-catch-definition.png" alt-text="Capture d’écran montrant la définition et le résultat d’un bloc Try-Catch.":::

#### <a name="do-if-else-block"></a>Bloc Do-If-Else

Dans cette approche, le client définit la logique métier et spécifie les chemins _En cas d’échec_ et _En cas de réussite_. Cette approche entraîne l’échec du pipeline même si le chemin _En cas d’échec_ réussit.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-3-do-if-else-definition.png" alt-text="Capture d’écran montrant la définition et le résultat du bloc Do-If-Else.":::

#### <a name="do-if-skip-else-block"></a>Bloc Do-If-Skip-Else

Dans cette approche, le client définit la logique métier et spécifie les chemins _En cas d’échec_ et _En cas de réussite_, avec une activité _En cas d’omission_ factice jointe. Cette approche entraîne la réussite du pipeline si le chemin _En cas d’échec_ réussit.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-4-do-if-skip-else-definition.png" alt-text="Capture d’écran montrant la définition et le résultat du bloc Do-If-Skip-Else.":::

### <a name="summary-table"></a>Tableau récapitulatif

Approche | Définit | Quand l’activité réussit, le pipeline global indique | Quand l’activité échoue, le pipeline global indique
---------------------------- | ------------------- | ------------------- | -------------------
[Try-Catch](#try-catch-block) | Chemin _En cas d’échec_ uniquement | Opération réussie |  Opération réussie
[Do-If-Else](#do-if-else-block) | Chemin _En cas d’échec_ + chemin _En cas de réussite_ | Succès |  Échec
[Do-If-Skip-Else](#do-if-skip-else-block) |  Chemin _En cas d’échec_ + chemin _En cas de réussite_ (avec une activité _En cas d’omission factice_ à la fin) | Opération réussie |  Opération réussie

### <a name="how-pipeline-failure-are-determined"></a>Comment l’échec de pipeline est déterminé

Différents mécanismes de gestion des erreurs peuvent conduire à un état différent pour le pipeline : si certains pipelines échouent, d’autres réussissent. Nous déterminons la réussite et l’échec des pipelines comme ceci :

* Évaluation du résultat de toutes les activités au niveau feuille. Si une activité au niveau feuille a été ignorée, nous évaluons son activité parente à la place.
* Le résultat du pipeline est réussi si et seulement si tous les nœuds évalués réussissent.

En supposant que l’activité _En cas d’échec_ et l’activité _En cas d’échec factice_ réussissent :

* Dans l’approche [Try-Catch](#try-catch-block),

  * Quand l’activité précédente réussit : le nœud _En cas d’échec_ est ignoré et son nœud parent réussit ; le pipeline global réussit
  * Quand l’activité précédente échoue : le nœud _En cas d’échec_ est activé ; le pipeline global réussit

* Dans l’approche [Do-If-Else](#do-if-else-block),

  * Quand l’activité précédente réussit : le nœud _En cas de réussite_ réussit et le nœud _En cas d’échec_ est ignoré (et son nœud parent réussit) ; le pipeline global réussit
  * Quand l’activité précédente échoue : le nœud _En cas de réussite_ est ignoré et son nœud parent échoue ; le pipeline global échoue

* Dans l’approche [Do-If-Skip-Else](#do-if-skip-else-block),

  * Quand l’activité précédente réussit : le nœud _En cas d’omission factice_ est ignoré et son nœud parent _En cas de réussite_ réussit ; l’activité de l’autre nœud, _En cas d’échec_, est ignorée et son nœud parent réussit ; le pipeline global réussit
  * Quand l’activité précédente échoue : le nœud _En cas d’échec_ réussit et _En cas d’omission factice_ réussit ; le pipeline global réussit

## <a name="next-steps"></a>Étapes suivantes

[Métriques et alertes Data Factory](monitor-metrics-alerts.md)

[Surveiller visuellement](monitor-visually.md#alerts)
