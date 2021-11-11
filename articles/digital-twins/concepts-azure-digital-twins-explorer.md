---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Découvrez les fonctionnalités et l’objectif d’Azure Digital Twins Explorer et quand il peut être utile pour visualiser des modèles numériques, des jumeaux et des graphiques.
author: baanders
ms.author: baanders
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bbcc693123f84d5cf789f53c8961f648ce2e90bf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504243"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer (préversion)

Cet article contient des informations sur **Azure Digital Twins Explorer**, notamment ses cas d’usage et une vue d’ensemble de ses fonctionnalités. Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).

Azure Digital Twins Explorer est un outil de développement qui vous aide à visualiser et à interagir avec les données de votre instance Azure Digital Twins, notamment vos [modèles](concepts-models.md) et votre [graphe de jumeaux](concepts-twins-graph.md). 

>[!NOTE]
>Cet outil est actuellement en **préversion publique**.

Voici une vue de la fenêtre de l’Explorateur, présentant des modèles et des jumeaux qui ont été renseignés pour un exemple de graphique :

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer montrant des exemples de modèles et de jumeaux" lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

L’interface visuelle est un bon outil pour explorer et comprendre la forme de votre ensemble graphique et modèle. Elle vous permet également de faire des modifications précises, sur place vers des jumeaux et des relations individuels.

## <a name="when-to-use"></a>Quand l’utiliser

Azure Digital Twins Explorer est un outil visuel conçu pour les utilisateurs qui souhaitent explorer leur graphe de jumeaux et modifier les jumeaux et les relations dans le contexte de leur graphe.

Les développeurs trouveront cet outil particulièrement utile dans les scénarios suivants :
* **Exploration** : utilisez l’Explorateur pour en savoir plus sur Azure Digital Twins et la façon dont il représente votre environnement réel. Importez des exemples de modèles et de graphes que vous pouvez afficher et modifier pour vous familiariser avec le service. Pour connaître les étapes à suivre pour commencer à utiliser Azure Digital Twins Explorer, consultez [Bien démarrer avec Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md).
* **Développement**  utilisez l’Explorateur pour afficher et valider votre graphique jumeau. Vous pouvez également l’utiliser pour examiner des propriétés spécifiques des modèles, des jumeaux et des relations. Apportez des modifications sur le vif à votre graphique et à ses données. Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

L’objectif principal de l’Explorateur est de vous aider à visualiser et à comprendre votre graphe, et à le mettre à jour en fonction des besoins. Pour les solutions à grande échelle et pour le travail qui doit être répété ou automatisé, utilisez plutôt les [API et les kits SDK](./concepts-apis-sdks.md) pour interagir avec votre instance par le biais de code.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>Fonctionnalités et fonctions

Azure Digital Twins Explorer est organisé en panneaux, chacun offrant un ensemble de fonctionnalités différent pour l’exploration et la gestion de vos modèles, jumeaux et relations.

Les sections de l’Explorateur sont les suivantes :
* **Query Explorer** : exécutez des requêtes sur le graphe de jumeaux, et observez les résultats visuels dans le panneau **Twin Graph**.
* **Modèles** : affichez une liste de vos modèles et effectuez des actions de modèle, telles que l’ajout, la suppression et l’affichage des détails du modèle.
* **Jumeaux** : affichez une liste de vos jumeaux et de leurs relations associées.
* **Twin Graph** : visualisez vos jumeaux et relations sous la forme d’un graphe de jumeaux personnalisable. Créez et supprimez des jumeaux et des relations, et affichez ou modifiez leurs propriétés.
* **Model Graph** : visualisez vos modèles et leurs interconnexions sous la forme d’un graphe de modèle personnalisable.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer mettant en évidence chacun des panneaux décrits ci-dessus." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

Pour obtenir des instructions détaillées sur l’utilisation de chaque fonctionnalité, consultez [Utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

## <a name="how-to-contribute"></a>Marche à suivre pour contribuer

Azure Digital Twins Explorer est un outil **open source** qui accueille toute contribution au code et à la documentation. L’application hébergée est déployée régulièrement à partir d’un dépôt de code source dans GitHub.

Pour voir le code source de l’outil et lire des instructions détaillées sur la façon de contribuer au code, visitez son dépôt GitHub : [digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer).

Pour voir les instructions permettant de contribuer à cette documentation, consultez le [Guide du contributeur Microsoft Docs](/contribute/).

## <a name="other-considerations"></a>Autres considérations

### <a name="region-support"></a>Prise en charge de la région

Azure Digital Twins Explorer peut être utilisé avec toutes les instances d’Azure Digital Twins dans toutes les [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Toutefois, pendant la préversion publique, les données peuvent être envoyées pour traitement dans des régions différentes de celle où l’instance est hébergée. Pour éviter que des données soient routées de cette façon dans des situations où la souveraineté des données est un problème, vous pouvez télécharger le [code open source](#how-to-contribute) pour créer une version hébergée localement de l’Explorateur sur votre propre machine.

### <a name="billing"></a>Facturation

Azure Digital Twins Explorer est un outil gratuit qui permet d’interagir avec le service Azure Digital Twins. Bien que l’outil lui-même soit gratuit, des coûts peuvent être facturés pendant son utilisation lorsque des requêtes sont envoyées au service Azure Digital Twins, conformément aux règles tarifaires suivantes : [Tarification Azure Digital Twins](https://azure.microsoft.com/pricing/details/digital-twins/).

## <a name="next-steps"></a>Étapes suivantes 

Découvrez comment utiliser les fonctionnalités d’Azure Digital Twins Explorer en détail : [Utiliser Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).