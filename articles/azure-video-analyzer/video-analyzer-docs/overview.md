---
title: Qu’est-ce qu’Azure Video Analyzer
description: Cette rubrique fournit une vue d’ensemble d’Azure Video Analyzer
ms.topic: overview
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b72abcd0f3df246e441d68643456ee7134522728
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563147"
---
# <a name="what-is-azure-video-analyzer-preview"></a>Qu’est-ce qu’Azure Video Analyzer ? (préversion)
 
Azure Video Analyzer fournit une plateforme pour créer des applications vidéo intelligentes qui s’étendent à la périphérie et au cloud. La plateforme se compose d’un module IoT Edge et d’un service Azure. Elle offre la possibilité de capturer, d’enregistrer et d’analyser des vidéos en direct, ainsi que de publier les résultats, à savoir la vidéo et les insights de celle-ci, vers la périphérie ou le cloud.

Le module E de Video Analyzer peut être utilisé avec d’autres modules Azure IoT Edge tels que Stream Analytics, Cognitive Services et d’autres services cloud Azure comme Event Hub et Cognitive Services pour créer de puissantes applications hybrides (autrement dit, Edge + cloud). Ce module de Edge extensible s’intègre en toute transparence à divers modules d’intelligence artificielle Edge tels que les conteneurs Azure Cognitive Services ou les modules Edge personnalisés générés à l’aide de modèles de Machine Learning open source et de vos données d’apprentissage. En vous appuyant sur la plateforme Video Analyzer, vous pouvez analyser la vidéo en direct sans vous soucier de la complexité de la création, de l’exploitation et de la maintenance d’un système complexe.

Outre l’analyse de la vidéo en direct, le module Edge vous permet d’enregistrer une vidéo localement en périphérie ou dans le cloud, et de publier des insights de la vidéo sur des services Azure (en périphérie et/ou dans le cloud). Si la vidéo et les insights de la vidéo sont enregistrés dans le cloud, le service cloud Video Analyzer permet de les gérer.

Le service cloud Video Analyzer peut donc être utilisé pour améliorer les solutions IoT avec des fonctionnalités de [système de gestion vidéo](https://en.wikipedia.org/wiki/Video_management_system), telles que l’enregistrement, la lecture et l’exportation (génération de fichiers vidéo qui peuvent être partagés en externe). Il peut également être utilisé pour créer une solution native Cloud avec les mêmes fonctionnalités, comme illustré dans le diagramme ci-dessous, avec des caméras se connectant directement au cloud.

## <a name="accelerate-iot-solutions-development"></a>Accélérer le développement de solutions IoT 

Les solutions IoT qui combinent l’analytique vidéo avec des signaux d’autres capteurs IoT et/ou des données métier peuvent vous aider à automatiser ou semi-automatiser des décisions métier, et ainsi améliorer la productivité. Video Analyzer vous permet de créer des solutions de ce type plus rapidement. Vous pouvez vous concentrer sur la création des modules d’analyse vidéo et de la logique propre à votre entreprise, et laisser le soin à la plateforme de masquer les complexités liées à la gestion et à l’exécution d’un pipeline vidéo.

Avec Video Analyzer, vous pouvez continuer à utiliser vos [caméras de télésurveillance](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) avec vos [systèmes de gestion vidéo](https://en.wikipedia.org/wiki/Video_management_system) existants et créer des applications d’analytique vidéo de manière indépendante. Video Analyzer peut être utilisé avec des kits de développement logiciel (SDK) de vision par ordinateur pour créer des solutions IoT de pointe. Le diagramme ci-dessous illustre ceci.

![Développer des solutions IoT avec Video Analyzer](./media/overview/product-diagram.svg)

### <a name="concepts"></a>Concepts

* [Pipeline](pipeline.md)
* [Video Analyzer sans enregistrement vidéo](analyze-live-video-without-recording.md)
* [Enregistrement de vidéo](video-recording.md)


## <a name="compliance-privacy-and-security"></a>Conformité, confidentialité et sécurité

Rappelez-vous que vous devez respecter toutes les lois applicables dans le cadre de votre utilisation de Video Analyzer, et que vous n’êtes pas autorisé à l’utiliser ou à utiliser tout autre service Azure d’une façon qui porte atteinte aux droits d’autrui ou qui soit préjudiciable pour autrui.

Pour pouvoir traiter une vidéo avec Video Analyzer, vous devez avoir tous les droits appropriés pour utiliser la vidéo, y compris, lorsque la loi l’exige, tous les consentements nécessaires des personnes (le cas échéant) qui apparaissant dans la vidéo ou l’image, pour utiliser, traiter et stocker leurs données dans Video Analyzer et Azure. Certaines juridictions peuvent imposer des exigences légales spéciales pour la collecte, le traitement en ligne et le stockage de certaines catégories de données, comme les données biométriques. Avant d’utiliser Video Analyzer et Azure pour le traitement et le stockage de données soumises à des exigences légales spéciales, vous devez veiller à respecter toutes les exigences légales applicables.

Pour en savoir plus sur la conformité, la confidentialité et la sécurité dans Video Analyzer, visitez le [Centre de confidentialité](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx) Microsoft. Pour connaître les obligations de confidentialité de Microsoft et les bonnes pratiques de gestion et de conservation de vos données, y compris savoir comment supprimer vos données, consultez la [Déclaration de confidentialité](https://privacy.microsoft.com/PrivacyStatement), les [Conditions d’utilisation du produit](https://www.microsoft.com/licensing/terms/welcome/welcomepage) et l’[avenant de la protection des données de produits et services de Microsoft](https://www.microsoft.com/licensing/docs/view/Microsoft-Products-and-Services-Data-Protection-Addendum-DPA) (« DPA »). En utilisant Video Analyzer, vous acceptez de respecter les Conditions d’utilisation du produit, le DPA et la Déclaration de confidentialité.

## <a name="next-steps"></a>Étapes suivantes

* Suivez l’article [Démarrage rapide : Bien démarrer avec Azure Video Analyzer](get-started-detect-motion-emit-events.md) pour voir comment exécuter la détection de mouvement sur une vidéo en direct.
* Passer en revue la [terminologie](terminology.md).
