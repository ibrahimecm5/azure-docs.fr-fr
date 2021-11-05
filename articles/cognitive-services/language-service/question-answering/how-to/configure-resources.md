---
title: Configurer le service de réponses aux questions
description: Ce document décrit les configurations avancées des ressources compatibles avec les réponses aux questions personnalisées.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2eae7e38422aa597c9b093dbefaef553cb084d09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097553"
---
# <a name="configure-custom-question-answering-enabled-resources"></a>Configurer des ressources avec réponses aux questions personnalisées

Vous pouvez configurer les réponses aux questions pour utiliser une autre ressource de Recherche cognitive.

## <a name="change-cognitive-search-resource"></a>Modifier les ressources de Recherche cognitive

> [!WARNING]
> Si vous modifiez le service Azure Search associé à votre ressource de langue, vous perdez l’accès à tous les projets/bases de connaissances déjà présents dans celui-ci. Veillez à exporter les projets existants avant de modifier le service Azure Search.

Si vous créez une ressource de langue et ses dépendances (par exemple, la recherche) via le Portail Azure, un service de recherche est créé pour vous et lié à la ressource de langue. Une fois ces ressources créées, vous pouvez mettre à jour la ressource de recherche sous l’onglet **Configuration**.

1.  Accédez à votre ressource linguistique sur le Portail Azure.

2.  Sélectionnez **Fonctionnalités**, puis sélectionnez le service Azure Recherche cognitive que vous souhaitez lier à votre ressource de langue.

> [!div class="mx-imgBorder"]
> ![Ajouter QnA à Analyse de texte](../media/configure-resources/update-custom-feature.png)

3.  Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

* [Chiffrer des données au repos](./encrypt-data-at-rest.md)
