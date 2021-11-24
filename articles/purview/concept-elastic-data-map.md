---
title: Mappage de données élastique
description: Cet article explique les concepts du mappage de données élastique dans Azure Purview
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 08/18/2021
ms.custom: template-concept
ms.openlocfilehash: fd667abab0802d6d11fc855a620bf51f8419b978
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555761"
---
# <a name="elastic-data-map-in-azure-purview"></a>Mappage de données élastique dans Azure Purview

Azure Purview Data Map fournit la base de la découverte des données et de la gouvernance des données. Il capture les métadonnées relatives aux données d’entreprise présentes dans les environnements d’analyse, de logiciel en tant que service (SaaS) et d’exploitation dans des environnements hybrides, locaux et à plusieurs clouds. Le mappage des données Purview est automatiquement mis à jour avec le système intégré de classification et d’analyse automatisé. Avec l’interface utilisateur, les développeurs peuvent interagir via le programme avec le mappage de données, à l’aide des API open source Apache Atlas.

## <a name="elastic-data-map"></a>Mappage de données élastique

Tous les comptes Azure Purview ont un mappage de données qui peut croître de manière élastique à partir d’une unité de capacité. Ils sont mis à l’échelle en fonction de la charge de la demande dans la fenêtre d’élasticité ([Vérifiez les limites actuelles](how-to-manage-quotas.md)). Ces limites doivent couvrir la plupart des paysages de données. Toutefois, si vous avez besoin d’une capacité supérieure, [vous pouvez créer un ticket de support](#request-capacity).

## <a name="data-map-capacity-unit"></a>Unité de capacité du mappage de données

Le mappage de données élastiques est fourni avec les composants de débit et de stockage des opérations qui sont représentés en tant qu’unité de capacité (CU). Par défaut, tous les comptes Azure Purview sont fournis avec une unité de capacité et augmentent de manière élastique en fonction de l’utilisation. Chaque unité de capacité de mappage de données comprend un débit de 25 opérations/s et une limite de 2 Go de stockage de métadonnées.  

### <a name="operations"></a>Operations

Les opérations sont la mesure du débit du mappage de données Purview. Elles incluent les opérations de création, de lecture, d’écriture, de mise à jour et de suppression sur les métadonnées stockées dans le mappage de données. Voici quelques exemples d’opérations :

- Créer une ressource dans le mappage de données
- Ajouter une relation à un élément multimédia, tel qu’un propriétaire, un gestionnaire, un parent, une traçabilité, etc.
- Modifier un élément multimédia pour ajouter des métadonnées d’entreprise telles que la description, le terme de glossaire, etc.
- Recherche par mot clé avec des résultats dans la page des résultats de recherche.

### <a name="storage"></a>Stockage

Le stockage est le deuxième composant du mappage de données et comprend des métadonnées techniques, commerciales, opérationnelles et sémantiques.

Les métadonnées techniques incluent le schéma, le type de données, les colonnes, etc., qui sont découvertes à partir de l’[analyse](concept-scans-and-ingestion.md) Purview. Les métadonnées commerciales comprennent l’automatisation (par exemple, la promotion de jeux de données Power BI ou les descriptions de tables SQL) et le balisage manuel des descriptions, des termes du glossaire, etc. Les exemples de métadonnées sémantiques incluent le mappage de collection avec des sources de données ou des classifications. Les métadonnées opérationnelles incluent l’état de l’activité d’exécution de la copie de la fabrique de données et les temps d’exécution.

## <a name="work-with-elastic-data-map"></a>Utiliser le mappage de données élastique

- **Mappage de données élastique avec mise à l’échelle automatique** : vous commencerez avec un mappage de données d’une unité de capacité pouvant être mise à l’échelle automatiquement en fonction de la charge. Pour la plupart des organisations, cette fonctionnalité débouche sur plus d’économies et un coût de démarrage des projets de gouvernance des données faible. Cette fonctionnalité aura un impact sur la tarification.

- **Analyse et ingestion améliorées** : vous pouvez suivre et contrôler la population des ressources de données, ainsi que la classification et la traçabilité à la fois dans les processus d’analyse et d’ingestion. Cette fonctionnalité aura un impact sur la tarification.

## <a name="scenario"></a>Scénario

Claudia est administratrice Azure chez Contoso et elle souhaite approvisionner un nouveau compte Azure Purview depuis le portail Azure. Lors de l’approvisionnement, elle ne connaît pas la taille requise du mappage de données Purview à prendre en charge dans l’état futur de la plateforme. Toutefois, elle sait que le mappage de données Purview est facturé par unités de capacité, qui sont affectées par le débit du stockage et des opérations. Elle souhaite approvisionner le plus petit mappage de données pour réduire le coût et augmenter la taille du mappage de données de manière élastique en fonction de la consommation.  

Claudia peut créer un compte Purview avec la taille de mappage de données par défaut d’une unité de capacité qui peut être mise à l’échelle automatiquement. La fonctionnalité de mise à l’échelle automatique permet également d’ajuster la capacité en fonction de pics de données intermittents ou planifiés pendant des périodes données. Claudia suit les étapes suivantes de l’expérience d’approvisionnement pour finaliser la configuration du réseau et terminer l’approvisionnement.  

Dans la page des métriques d’Azure Monitor, Claudia peut voir la consommation du stockage du mappage de données et le débit des opérations. Elle peut aussi configurer une alerte lorsque le débit du stockage ou des opérations atteint une certaine limite, afin de surveiller la consommation et la facturation du nouveau compte Purview.  

## <a name="data-map-billing"></a>Facturation du mappage de données

Les clients sont facturés par unité de capacité (25 opérations/s et 2 Go) et la facturation supplémentaire est basée sur la consommation de chaque unité de capacité supplémentaire cumulée jusqu’à une heure. Les opérations de mappage de données sont mises à l’échelle par incréments de 25 opérations/s et le stockage des métadonnées évolue par incréments de 2 Go. Le mappage de données Purview peut être mis à l’échelle automatiquement dans la fenêtre d’élasticité ([Vérifiez les limites actuelles](how-to-manage-quotas.md)). Toutefois, pour obtenir le niveau suivant de fenêtre d’élasticité, vous devez créer un ticket de support.

Les unités de capacité du mappage de données sont fournies avec un plafond de débit et de stockage des opérations. Si le stockage dépasse l’unité de capacité actuelle, les clients sont facturés pour l’unité de capacité suivante, même si le débit des opérations n’est pas utilisé. Le tableau ci-dessous répertorie les plages d’unités de capacité du mappage de données. Contactez le support si l’unité de capacité du mappage de données dépasse 100 unités de capacité.

|Unité de capacité du mappage de données  |Débit en opérations/s   |Capacité de stockage en Go|
|----------|-----------|------------|
|1    |25      |2     |
|2    |50      |4     |
|3    |75      |6     |
|4    |100     |8     |
|5    |125     |10    |
|6    |150     |12   |
|7    |175      |14     |
|8    |200     |16    |
|9    |225      |18    |
|10    |250     |20    |
|..   |..      |..     |
|100    |2 500     |200   |

### <a name="billing-examples"></a>Exemples de facturation

- Le débit des opérations du mappage de données Purview pour l’heure donnée est inférieur ou égal à 25 opérations/s et la taille de stockage est de 1 Go. Les clients sont facturés pour une unité de capacité.

- Le débit des opérations du mappage de données Purview pour l’heure donnée est inférieur ou égal à 25 opérations/s et la taille de stockage est de 3 Go. Les clients sont facturés pour deux unités de capacité.

- Le débit des opérations du mappage de données Purview pour l’heure donnée est de 50 opérations/s et la taille de stockage est de 1 Go. Les clients sont facturés pour deux unités de capacité.

- Le débit des opérations du mappage de données Purview pour l’heure donnée est de 50 opérations/s et la taille de stockage est de 5 Go. Les clients sont facturés pour trois unités de capacité.

- Le débit des opérations du mappage de données Purview pour l’heure donnée est de 250 opérations/s et la taille de stockage est de 5 Go. Les clients sont facturés pour 10 unités de capacité.

### <a name="detailed-billing-example"></a>Exemple de facturation détaillée

L’exemple de facturation du mappage de données ci-dessous montre un mappage de données avec un stockage de métadonnées croissant et des opérations variables par seconde sur une période de six heures, de 12h 00 à 18h00. La ligne rouge dans le graphique correspond à une consommation d’opérations par seconde et la ligne en pointillés bleue correspond à la consommation de stockage des métadonnées dans cette fenêtre de six heures :

:::image type="content" source="./media/concept-elastic-data-map/operations-and-metadata.png" alt-text="Graphique illustrant le nombre d’opérations et la croissance des métadonnées au fil du temps.":::

Chaque unité de capacité de mappage de données prend en charge 25 opérations par seconde et 2 Go de stockage de métadonnées. Le mappage de données est facturé sur base horaire. Vous êtes facturé pour le nombre maximal d’unités de capacité de mappage de données nécessaires dans cette heure. Parfois, vous avez besoin de plus d’opérations par seconde, ce qui augmente le nombre d’unités de capacité nécessaires dans cette heure. À d’autres moments, l’utilisation de vos opérations/seconde peut être bas, mais vous aurez peut-être encore besoin d’un volume important de stockage des métadonnées. Le stockage des métadonnées détermine le nombre d’unités de capacité dont vous avez besoin dans l’heure.

Le tableau ci-dessous montre le nombre maximal d’opérations/seconde et le stockage de métadonnées utilisé par heure pour cet exemple de facturation : 

:::image type="content" source="./media/concept-elastic-data-map/billing-table.png" alt-text="Tableau illustrant le nombre maximal d’opérations et la croissance des métadonnées au fil du temps.":::

En fonction des opérations de mappage de données/seconde et de la consommation du stockage de métadonnées au cours de cette période, ce mappage de données est facturé 20 heures d’unités de capacité sur cette période de six heures (1 + 3 + 4 + 3 + 6 + 3) :

:::image type="content" source="./media/concept-elastic-data-map/billing-capacity-hours.png" alt-text="Tableau illustrant le nombre d’heures CU dans le temps.":::

>[!Important]
>Le mappage de données Purview peut être mis à l’échelle automatiquement dans la fenêtre d’élasticité ([Vérifiez les limites actuelles](how-to-manage-quotas.md)). Pour obtenir le niveau suivant de fenêtre d’élasticité, vous devez créer un ticket de support.

## <a name="request-capacity"></a>Demande de capacité

Si vous utilisez des jeux de données très volumineux ou un environnement massif et que vous avez besoin de plus de capacité pour votre mappage de données élastique, vous pouvez demander une plus grande capacité de la fenêtre d’élasticité en [créant un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Sélectionnez **Limites du service et des abonnements (quota)** et suivez les instructions à l’écran en choisissant le compte Purview pour lequel vous souhaitez demander une plus grande capacité.

:::image type="content" source="./media/concept-elastic-data-map/increase-limit.png" alt-text="Capture d’écran montrant la création d’un cas de support, avec les options d’augmentation de la limite sélectionnées.":::

Dans la description, fournissez autant d’informations pertinentes que vous le pouvez concernant votre environnement et la capacité supplémentaire que vous souhaitez demander.

## <a name="monitoring-the-elastic-data-map"></a>Surveillance de la carte de données élastique

Les unités de _capacité_ de la carte de données de métriques et la taille de stockage de la _carte de données_ peuvent être surveillées pour comprendre la taille de l’espace de données et la facturation.

1. Allez sur le [portail Azure](https://portal.azure.com) et accédez à la page des **comptes Purview** et sélectionnez votre _compte Azure Purview_

2. Cliquez sur **Vue d'ensemble** et faites défiler vers le bas pour observer la section **Surveillance** des unités de _capacité de carte de données_ et de la _taille de stockage de carte de données_ sur différentes périodes de temps

    :::image type="content" source="./media/concept-elastic-data-map/data-map-metrics.png" alt-text="Capture d’écran du menu montrant la page de vue d’ensemble des métriques de carte de données élastique.":::

3. Pour obtenir des paramètres supplémentaires, accédez à **Analyse--> Mesures** pour observer les **unités de capacité de la carte de données** et la **Taille du Stockage de mappage des données**.

    :::image type="content" source="./media/concept-elastic-data-map/elastic-data-map-metrics.png" alt-text="Capture d’écran du menu montrant les mesures.":::

4. Cliquez sur **Unités de capacité de la carte de données** pour afficher l’utilisation de l’unité de capacité de mappage de données au cours des dernières 24 heures. Notez que le fait de pointer la souris sur le graphique linéaire indique les unités de capacité de la carte de données consommées à ce moment précis, le jour précis.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-default.png" alt-text="Capture d’écran du menu montrant les unités de capacité de la carte de données consommées sur 24 heures.":::

5. Cliquez sur **Heure locale : 24 dernières heures (automatique-1 heure)** en haut à droite de l’écran pour modifier l’intervalle de temps affiché pour le graphique.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-custom.png" alt-text="Capture d’écran du menu montrant les unités de capacité de la carte de données consommées sur une plage horaire personnalisée.":::

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-time-range.png" alt-text="Capture d’écran du menu montrant les unités de capacité de la carte de données consommées sur une plage horaire de 3 jours.":::

6. Personnalisez le type de graphique en cliquant sur l’option comme indiqué ci-dessous.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-capacity-graph-type.png" alt-text="Capture d’écran du menu montrant les options permettant de modifier le type de graphique.":::

7. Cliquez sur le **nouveau graphique** pour ajouter le graphique du tableau de taille du stockage de la carte de données.

    :::image type="content" source="./media/concept-elastic-data-map/data-map-storage-size.png" alt-text="Capture d’écran du menu montrant la taille de stockage de la carte de données utilisée.":::

## <a name="summary"></a>Résumé

Avec le mappage de données élastique, Purview fournit une solution à faible coût pour les clients qui en sont au début de leur parcours de gouvernance des données.
Le mappage de données Purview croître de manière élastique avec le modèle de paiement à l’accès à partir de 1 unité de capacité.
Les clients n’ont pas à se soucier de choisir la bonne taille de mappage de données pour leur patrimoine de données au moment de la mise en service et de gérer les migrations de plateformes en raison de limites de taille.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un compte Azure Purview](create-catalog-portal.md)
- [Tarification de Purview](https://azure.microsoft.com/pricing/details/azure-purview/)
