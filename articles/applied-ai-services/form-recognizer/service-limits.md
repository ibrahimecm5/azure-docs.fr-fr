---
title: Quotas et limites du module de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Aide-mémoire, description détaillée et bonnes pratiques concernant les quotas et limites du service Azure Form Recognizer
services: cognitive-services
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: vikurpad
ms.openlocfilehash: 8a1b57571acdbfd8dce29e45218cfd096b0906f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478994"
---
# <a name="form-recognizer-service-quotas-and-limits"></a>Quotas et limites du service Form Recognizer

Cet article contient un aide-mémoire et la **description détaillée** des quotas et des limites du service Azure Form Recognizer pour tous les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/form-recognizer/). Il présente également les meilleures pratiques pour éviter la limitation des demandes. 

Pour l’utilisation avec le [Kit de développement logiciel (SDK) Form Recognizer](quickstarts/try-v3-csharp-sdk.md), l'[API REST Form Recognizer](quickstarts/try-v3-rest-api.md), [Form Recognizer Studio](quickstarts/try-v3-form-recognizer-studio.md) et l’[Outil d’étiquetage des exemples](https://fott-2-1.azurewebsites.net/).

| Quota | Gratuit (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Limite des demandes simultanées** | 1 | 15 (valeur par défaut) |
| Réglable | Non <sup>2</sup> | Oui<sup>2</sup> |
| **Limite du modèle compose** | 5 | 100 (valeur par défaut) |


<sup>1</sup> Pour le niveau tarifaire **gratuit (F0)** , consultez également les allocations mensuelles sur la [page de tarification](https://azure.microsoft.com/pricing/details/form-recognizer/).
<sup>2</sup> Consultez les [meilleures pratiques](#example-of-a-workload-pattern-best-practice), et les [instructions d’ajustement](#create-and-submit-support-request).

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Description détaillée, ajustement de quota et meilleures pratiques
Avant de demander une augmentation de quota (le cas échéant), assurez-vous qu’elle est nécessaire. Le service Form Recognizer utilise la mise à l’échelle automatique pour mettre les ressources de calcul requises en mode « à la demande » et limiter le coût pour les clients, supprimer les privilèges d'accès des ressources non utilisées en ne conservant pas une quantité excessive de capacité matérielle. Chaque fois que votre application reçoit un code de réponse 429 (« Trop de demandes ») alors que votre charge de travail s’inscrit dans les limites définies (voir [Référence rapide sur les quotas et limites](#form-recognizer-service-quotas-and-limits)), l’explication la plus probable est que le service est en train de mettre à l’échelle votre demande mais n’a pas encore atteint l’échelle requise et que, par conséquent, il ne dispose pas dans l’immédiat de ressources suffisantes pour traiter la demande. Cet état est généralement temporaire et ne doit pas durer longtemps.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Meilleures pratiques générales pour atténuer la limitation lors de la mise à l’échelle automatique
Pour réduire les problèmes liés à la limitation (code de réponse 429), nous vous recommandons d’utiliser les techniques suivantes :
- Implémentez la logique de nouvelle tentative dans votre application.
- Évitez les variations nettes de la charge de travail. Augmentez la charge de travail graduellement. <br/>
*Exemple.* Votre application utilise Form Recognizer et votre charge de travail actuelle est de 10 TPS (transactions par seconde). À la seconde suivante, vous augmentez la charge à 40 TPS (soit quatre fois plus). Le service commence immédiatement la mise à l’échelle pour répondre à la nouvelle charge. Toutefois, comme il est probable qu’il ne puisse pas le faire en une seconde, certaines demandes reçoivent le code de réponse 429.

Les sections suivantes décrivent des cas spécifiques d’ajustement des quotas.
Accéder à [Form Recognizer : augmentation de la limite de demandes simultanées](#create-and-submit-support-request)

### <a name="increasing-transactions-per-second-request-limit"></a>Augmentation de la mimite de demandes de transactions par seconde
Par défaut, le nombre de demandes simultanées est limité à 15 transactions par seconde pour une ressource Form Recognizer. Pour le niveau tarifaire standard, vous pouvez augmenter ce nombre. Avant de soumettre la demande, assurez-vous d’avoir bien compris le contenu de [cette section](#detailed-description-quota-adjustment-and-best-practices), ainsi que les [meilleures pratiques](#example-of-a-workload-pattern-best-practice).

L’amélioration de la limite de demandes simultanées n’affecte **pas** directement vos coûts. Le service Form Recognizer utilise le modèle « Payez uniquement pour ce que vous utilisez ». La limite définit la hauteur à laquelle le service peut mettre à l’échelle avant de commencer à limiter vos demandes.

La valeur existante du paramètre Limite de demandes simultanées n’est **pas visible** via le portail Azure, des outils en ligne de commande ou des demandes d’API. Pour vérifier la valeur existante, créez une demande de support Azure.

#### <a name="have-the-required-information-ready"></a>Munissez-vous des informations requises :
- ID de ressource Form Recognizer
- Région

- **Comment obtenir des informations (modèle de base)**  :
  - Accédez au [portail Azure](https://portal.azure.com/).
  - Sélectionner la ressource Form Recognizer pour laquelle vous souhaitez augmenter la limite des transactions
  - Sélectionnez *Propriétés* (groupe *Gestion des ressources*).
  - Copiez et enregistrez les valeurs des champs suivants :
    - **ID de ressource**
    - **Emplacement** (région de votre point de terminaison)

#### <a name="create-and-submit-support-request"></a>Créer et soumettre une demande de support
Initiez l’augmentation de la limite de transactions par seconde (TPS) pour votre ressource en envoyant la demande de support :

- Vérifiez que vous disposez des [informations requises](#have-the-required-information-ready).
- Accédez au [portail Azure](https://portal.azure.com/).
- Sélectionner la ressource Form Recognizer pour laquelle vous souhaitez augmenter la limite des TPS
- Sélectionnez *Nouvelle demande de support* (groupe *Support et dépannage*)
- Une nouvelle fenêtre s’affiche, qui contient des informations renseignées automatiquement sur votre abonnement Azure et la ressource Azure.
- Entrer le *Résumé* (comme « Augmenter la limite des TPS Form Recognizer »)
- Dans *Type de problème*, sélectionner « Quota ou validation d’utilisation »
- Cliquez sur *Suivant : Solutions*
- Poursuivre avec la création de la demande.
- Sous l’onglet *Détails*, dans le champ *Description*, entrez :
  - une note indiquant que la demande concerne un quota **Form Recognizer**
  - Fournir une prévision TPS pour laquelle vous souhaitez effectuer une mise à l’échelle    
  - les informations sur la ressource Azure que vous [collectées avant](#have-the-required-information-ready) ;
  - complétez les informations requises, puis cliquez sur le bouton *Créer* sous l’onglet *Vérifier + créer* ;
  - notez le numéro de demande de support dans les notifications du portail Azure. Vous serez bientôt contacté pour un traitement supplémentaire.

## <a name="example-of-a-workload-pattern-best-practice"></a>Exemple de meilleure pratique pour un modèle de charge de travail
Cet exemple présente l’approche que nous recommandons de suivre pour atténuer la limitation possible des demandes en raison d’une [mise à l’échelle automatique en cours](#detailed-description-quota-adjustment-and-best-practices). Il ne s’agit pas d’une « recette exacte », mais d’un modèle que nous invitons à suivre et à ajuster en fonction des besoins.

Supposons qu’une ressource Form Recognizer a la limite par défaut définie. Démarrez la charge de travail pour soumettre vos demandes d’analyse. Si vous constatez des limitations fréquentes avec le code de réponse 429, commencez par effectuer une sauvegarde sur la demande de réponse d’analyse GET, puis réessayez en utilisant le modèle 2-3-5-8. En général, il est recommandé de ne pas appeler la réponse d’analyse GET plus d’une fois toutes les 2 secondes pour une requête POST correspondante. 

Si vous constatez que vous êtes limité sur le nombre de demandes POST pour les documents en cours d’envoi, envisagez d’ajouter un délai entre les demandes. Si votre charge de travail nécessite un niveau de traitement simultané plus élevé, vous devrez créer une demande de support pour augmenter vos limites de service sur les transactions par seconde.

En règle générale, il est fortement recommandé de tester la charge de travail et les modèles de charge de travail avant de passer à la production.

