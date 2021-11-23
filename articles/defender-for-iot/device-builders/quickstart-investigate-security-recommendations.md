---
title: Investiguer les recommandations de sécurité
description: Examiner les recommandations de sécurité à l’aide du service de sécurité Defender pour IoT.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 7f4dab5ed0590d0e2658145f17d657beb5af1177
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306127"
---
# <a name="quickstart-investigate-security-recommendations"></a>Démarrage rapide : Investiguer les recommandations de sécurité

L’analyse et l’application en temps voulu des recommandations de Defender pour IoT constituent la meilleure manière d’améliorer l’état de la sécurité et de réduire la surface d’attaque dans votre solution IoT.

Dans ce démarrage rapide, nous allons explorer les informations disponibles dans chaque recommandation de sécurité IoT et expliquer comment explorer et utiliser les détails de chaque recommandation et appareil associé, pour réduire les risques.

C’est parti !

## <a name="investigate-new-recommendations"></a>Examiner de nouvelles recommandations

La liste des recommandations de sécurité IoT Hub affiche toutes les recommandations de sécurité agrégées pour votre IoT Hub.

1. Dans le portail Azure, ouvrez l’**IoT Hub** que vous voulez examiner pour de nouvelles recommandations.

1. Dans le menu **Sécurité**, sélectionnez **Recommandations**. Toutes les recommandations de sécurité pour le hub IoT s’affichent et les recommandations marquées d’un drapeau **Nouveau** sont vos recommandations de ces dernières 24 heures.

1. Sélectionnez et ouvrez une recommandation dans la liste pour ouvrir ses détails et en explorer les spécificités.

## <a name="security-recommendation-details"></a>Détails des recommandations de sécurité

Ouvrez chaque recommandation agrégée pour afficher sa description détaillée, ses étapes de correction et l’identité d’appareil pour chaque appareil qui a déclenché une recommandation. La gravité de la recommandation ainsi que l’accès direct à l’examen s’affichent également à l’aide de l’Analytique des journaux d'activité.

1. Sélectionnez et ouvrez n’importe quelle recommandation à partir d’**IoT Hub** > **Sécurité** > liste **Recommandations**.

1. Examinez la **description**, la **gravité**, les **détails de l’appareil** de tous les appareils en lien avec la recommandation qui ont émis celle-ci au cours de la période d’agrégation.

1. Après avoir vérifié les spécificités de la recommandation, suivez les instructions de l’**étape de correction manuelle** afin d’atténuer et résoudre le problème à l’origine de la recommandation.

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="Appliquer les recommandations de sécurité avec Defender pour IoT" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1. Explorez les détails de la recommandation pour un appareil spécifique en sélectionnant l’appareil voulu dans la page détaillée.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="Examiner les recommandations de sécurité spécifiques pour un appareil avec Defender pour IoT" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1. Si un examen supplémentaire est nécessaire, **examinez la recommandation dans l’Analytique des journaux d’activité** en suivant le lien. 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer des alertes personnalisées...

> [!div class="nextstepaction"]
> [Créer des alertes personnalisées](quickstart-create-custom-alerts.md)
