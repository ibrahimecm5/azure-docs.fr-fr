---
title: Estimer les coûts
titleSuffix: Azure Cognitive Search
description: Découvrez les événements facturables, le modèle de tarification et des conseils pour gérer le coût de fonctionnement d’un service Recherche cognitive.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 21b12da25ae2baf7e31ad12af7b5c346b64bc3ac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577229"
---
# <a name="estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Estimer et gérer les coûts d’un service Recherche cognitive Azure

Dans cet article, vous découvrirez le modèle de tarification, les événements facturables et des conseils pour gérer le coût de fonctionnement d’un service Recherche cognitive Azure.

## <a name="pricing-model"></a>Modèle de tarification

L’architecture de scalabilité dans Recherche cognitive Azure est basée sur des combinaisons flexibles de réplicas et de partitions afin que vous puissiez faire varier la capacité selon que vous avez besoin d’une plus grande puissance d’interrogation ou d’indexation, et ne payer que ce dont vous avez besoin.

La quantité de ressources utilisées par votre service de recherche, multipliée par le tarif de facturation établi par le niveau de service, détermine le coût de fonctionnement du service. Les coûts et la capacité sont étroitement liés. Lors de l’estimation des coûts, la compréhension de la capacité nécessaire à l’exécution de vos charges de travail d’indexation et d’interrogation vous donne la meilleure idée de ce que seront les coûts prévus.

À des fins de facturation, il existe deux formules simples à connaître :

| Formule | Description |
|---------|-------------|
| **R x P = SU** | Le nombre de réplicas utilisés, multiplié par le nombre de partitions utilisées, est égal à la quantité d’*unités de recherche* (SU) utilisée par un service. Une SU est une unité de ressource et peut être une partition ou un réplica. |
| **SU * tarif de facturation = dépense mensuelle** | Le nombre de SU multiplié par le tarif de facturation du niveau auquel vous avez approvisionné le service est le principal déterminant de votre facture mensuelle globale. Certaines fonctionnalités ou charges de travail ont des dépendances sur d’autres services Azure, ce qui peut augmenter le coût de votre solution au niveau de l’abonnement. La section Événements facturables ci-dessous répertorie les fonctionnalités qui peuvent s’ajouter à votre facture. |

Chaque service commence avec une Unité Recherche (un réplica multiplié par une partition) au minimum. Le montant maximal de tout service est de 36 SU. Ce montant maximal peut être atteint de plusieurs façons : 6 partitions x 6 réplicas, ou 3 partitions x 12 réplicas, par exemple. La capacité utilisée est généralement inférieure à la capacité totale (par exemple, un service de 3 réplicas et 3 partitions facturé avec un montant de 9 SU). Pour connaître les combinaisons valides, consultez l’article [Partition and replica combinations](search-capacity-planning.md#chart) (Combinaisons de partitions et de réplicas).

Le tarif de facturation est un tarif horaire par SU. Le tarif augmente progressivement à chaque niveau. Les niveaux supérieurs proposent des partitions plus volumineuses et plus rapides, ce qui entraîne un tarif horaire globalement plus élevé pour ces niveaux. Vous pouvez consulter les tarifs de chaque niveau sur la page des [détails de tarification](https://azure.microsoft.com/pricing/details/search/).

La plupart des clients mettent seulement une partie de la capacité totale en ligne et gardent le reste en réserve. Pour la facturation, le nombre de partitions et de réplicas que vous mettez en ligne, calculé à l’aide de la formule SU, détermine le tarif horaire qui vous est facturé. 

## <a name="billable-events"></a>Événements facturables

Une solution reposant sur Recherche cognitive Azure peut occasionner des coûts de l’une des manières suivantes :

+ [Coût du service](#service-costs) proprement dit, exécuté 24 h sur 24 et 7 j sur 7 avec une configuration minimale (une partition et un réplica), au taux de base. Vous pouvez y penser comme au coût fixe de fonctionnement du service.

+ Ajout de capacité (réplicas ou partitions), où les coûts augmentent par incréments du tarif facturable. Si la haute disponibilité est une exigence métier, vous aurez besoin de trois réplicas.

+ Frais de bande passante (transfert de données sortant)

+ Services complémentaires nécessaires pour des fonctionnalités spécifiques ou des fonctionnalités premium :

  + Enrichissement par IA à l’aide de compétences facturables (nécessite [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)). L’extraction d’images est également facturable.
  + Base de connaissances (nécessite le service [Stockage Azure](https://azure.microsoft.com/pricing/details/storage/)). Les opérations de stockage effectuées par l’indexeur sont facturables.
  + Enrichissement incrémentiel (nécessite [Stockage Azure](https://azure.microsoft.com/pricing/details/storage/), s’applique à l’enrichissement par IA)
  + Clés gérées par le client et chiffrement double (nécessite [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + Points de terminaison privés pour un modèle sans accès à Internet (nécessite [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/))
  + La recherche sémantique est une fonctionnalité premium des niveaux Standard (consultez la [page des tarifs du service Recherche cognitive](https://azure.microsoft.com/pricing/details/search/) pour connaître les coûts). Vous pouvez [désactiver la recherche sémantique](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch) pour éviter toute utilisation accidentelle.

### <a name="service-costs"></a>Coûts de service

Contrairement à des machines virtuelles ou à d’autres ressources qui peuvent être « mises en pause » pour éviter des frais, un service Recherche cognitive Azure est toujours disponible sur du matériel dédié à votre usage exclusif. En tant que telle, la création d’un service est un événement facturable qui commence lorsque vous créez le service et se termine lorsque vous le supprimez. 

Le coût minimal est la première unité de recherche (une réplique x une partition) au tarif facturable. Ces frais minimaux sont fixes pendant toute la durée de vie du service, car il est impossible d’exécuter le service sur une configuration inférieure à cette dernière. 

Au-delà des frais minimaux, vous pouvez ajouter des réplicas et des partitions indépendants les uns des autres. Les augmentations incrémentielles de capacité via des réplicas et partitions augmentent votre facture selon la formule suivante : **(réplicas x partitions x tarif de facturation)** , où le tarif facturé dépend du niveau tarifaire sélectionné.

Lorsque vous estimez le coût d’une solution de recherche, gardez à l’esprit que la tarification et la capacité ne sont pas linéaires (le fait de doubler la capacité fait plus que doubler le coût au même niveau). En outre, à un moment donné, le passage à un niveau supérieur peut vous offrir des performances supérieures et plus rapides à environ le même prix. Pour plus d’informations et un exemple, consultez [Mettre à niveau vers un niveau Standard S2](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier).

### <a name="bandwidth-charges"></a>Frais liés à la bande passante

L’utilisation d’[indexeurs](search-indexer-overview.md) peut avoir un impact sur la facturation si la source de données Azure se trouve dans une région différente de celle de Recherche cognitive Azure. Dans ce scénario, il pourrait y avoir un coût pour le déplacement des données sortantes de la source de données Azure vers Recherche cognitive Azure. Pour plus d’informations, consultez les pages de tarification de la plateforme de données Azure en question.

Vous pouvez éliminer totalement les frais de sortie de données si vous créez le service Recherche cognitive Azure dans la même région que vos données. Voici quelques informations issues de la [page de tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) :

+ Données entrantes : Microsoft ne facture aucune donnée entrante dans un quelconque service Azure. 

+ Données sortantes :  Les données sortantes font référence aux résultats de la requête. Recherche cognitive ne facture pas les données sortantes, mais des frais de sortie d’Azure sont possibles si les services se trouvent dans des régions différentes.

  Ces frais ne figurent pas sur votre facture Recherche cognitive Azure. Ils sont mentionnés ici, parce que si vous envoyez des résultats à d’autres régions ou à des applications non Azure, vous pourriez voir ces coûts reflétés dans votre facture globale.

### <a name="ai-enrichment-with-cognitive-services"></a>Enrichissement de l’IA avec Cognitive Services

Pour l’[enrichissement par IA](cognitive-search-concept-intro.md) à l’aide de compétences facturables, prévoyez d’[attacher une ressource Azure Cognitive Services facturable](cognitive-search-attach-cognitive-services.md) dans la même région que le service Recherche cognitive Azure, au niveau tarifaire S0 dans le cadre du paiement à l’utilisation. L’attachement de Cognitive Services n’entraîne aucun coût fixe. Vous payez uniquement pour le traitement dont vous avez besoin.

| Opération | Impact sur la facturation |
|-----------|----------------|
| Craquage de document, extraction de texte | Gratuit |
| Craquage de document, extraction d’image | Facturée en fonction du nombre d’images extraites de vos documents. Dans une [configuration d’indexeur](/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** est le paramètre qui déclenche l’extraction d’images. Si **imageAction** est défini sur « none » (valeur par défaut), l’extraction d’images ne vous sera pas facturée. Consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/search/) pour connaître les frais d’extraction d’image. |
| [Compétences intégrées](cognitive-search-predefined-skills.md) basées sur Cognitive Services | Facturées au même tarif que si vous aviez exécuté la tâche directement avec Cognitive Services. Vous pouvez traiter gratuitement 20 documents par indexeur et par jour. Les charges de travail plus importantes ou plus fréquentes nécessitent une clé. |
| [Compétences intégrées](cognitive-search-predefined-skills.md) qui n’ajoutent pas d’enrichissements | Aucun. Les compétences utilitaires non facturables sont les suivantes : Logique conditionnelle, Modélisateur, Fusion de texte et Fractionnement de texte. Aucun impact sur la facturation, aucune obligation de disposer d’une clé Cognitive Services et aucune limite fixée à 20 documents. |
| Compétences personnalisées | Une compétence personnalisée est une fonctionnalité que vous fournissez. Le coût d’utilisation d’une compétence personnalisée dépend entièrement du fait qu’un code personnalisé appelle d’autres services mesurés.  Aucune obligation de disposer d’une clé Cognitive Services et aucune limite fixée à 20 documents pour les compétences personnalisées.|
| [Recherche d’entité personnalisée](cognitive-search-skill-custom-entity-lookup.md) | Mesuré par la Recherche cognitive Azure. Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/search/#pricing). |

> [!TIP]
> L’[enrichissement incrémentiel (préversion)](cognitive-search-incremental-indexing-conceptual.md) réduit le coût du traitement des ensembles de compétences en mettant en cache et en réutilisant les enrichissements non affectés par les changements apportés à un ensemble de compétences. La mise en cache a besoin du Stockage Azure (consultez les [tarifs](https://azure.microsoft.com/pricing/details/storage/blobs/)). Toutefois, le coût cumulé de l’exécution des ensembles de compétences est moindre si les enrichissements existants peuvent être réutilisés.

## <a name="tips-for-managing-costs"></a>Conseils pour la gestion des coûts

L’aide suivante peut vous aider à réduire les coûts ou à les gérer plus efficacement :

+ Créez toutes les ressources dans la même région ou dans le moins de régions possible afin de réduire ou d’éliminer les coûts liés à la bande passante.

+ Regroupez tous les services dans un seul groupe de ressources, tel que Recherche cognitive Azure, Cognitive Services et tout autre service Azure utilisé dans votre solution. Dans le portail Azure, recherchez le groupe de ressources et utilisez les commandes **Cost Management** pour obtenir des informations sur les dépenses réelles et prévues.

+ Envisagez d’utiliser Application web Azure pour votre application frontale afin que les demandes et réponses restent dans les limites du centre de données.

+ Montez en puissance pour les opérations gourmandes en ressources, telles que l’indexation, puis réajustez à la baisse les charges de travail de requête régulières. Commencez avec la configuration minimale pour Recherche cognitive Azure (une unité de stockage composée d’une partition et un réplica), puis surveillez l’activité de l’utilisateur pour identifier des modèles d’utilisation qui indiqueraient un besoin de capacité supplémentaires. Si un modèle est prévisible, vous pouvez peut-être synchroniser l’échelle avec l’activité (vous devez écrire du code pour automatiser ce comportement).

+ La gestion des coûts est intégrée à l’infrastructure Azure. Pour plus d’informations sur le suivi des coûts, des outils et des API, consultez [Facturation et gestion des coûts](../cost-management-billing/cost-management-billing-overview.md).

L'arrêt temporaire d'un service de recherche est impossible. Les ressources dédiées sont toujours opérationnelles et sont allouées pour votre usage exclusif pendant pour la durée de vie de votre service. La suppression d'un service est définitive, et elle entraîne également la suppression des données associées à celui-ci.

En ce qui concerne le service lui-même, le seul moyen de réduire votre facture consiste à réduire le nombre de réplicas et de partitions à un niveau offrant garantissant encore des performances acceptables et la [conformité au SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ou à créer un service de niveau inférieur (les taux horaires du niveau S1 sont inférieurs à ceux des niveaux S2 ou S3). En supposant que vous approvisionniez votre service à la limite inférieure de vos prévisions de charge, si vous dépassez le service, vous pouvez créer un second service de niveau supérieur, régénérer vos index sur ce second service, puis supprimer le premier.

## <a name="next-steps"></a>Étapes suivantes

Vous souhaitez optimiser et réduire vos coûts de cloud ?

> [!div class="nextstepaction"]
> [Démarrer l’analyse des coûts avec Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
