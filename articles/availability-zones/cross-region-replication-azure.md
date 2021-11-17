---
title: Réplication inter-région dans Azure
description: Découvrez la réplication inter-région dans Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 31b1d0b7c4bcc498ba8490fd4edd5c6d13e76cfb
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858624"
---
# <a name="cross-region-replication-in-azure-business-continuity-and-disaster-recovery"></a>Réplication inter-région dans Azure : continuité d’activité et reprise d’activité

De nombreuses organisations ont besoin de la haute disponibilité fournie par les zones de disponibilité, qui sont également prises en charge, et d’une protection contre les phénomènes à grande échelle et les sinistres régionaux. Comme indiqué dans la [vue d’ensemble](overview.md) de la résilience des régions et des zones de disponibilité, les régions Azure sont conçues pour offrir une protection contre les sinistres locaux avec les zones de disponibilité. Cependant, elles peuvent également protéger contre les sinistres affectant une région ou une grande zone géographique avec la récupération d’urgence. Elles utilisent pour cela une autre région qui exploite la *réplication inter-région*.

## <a name="cross-region-replication"></a>Réplication entre régions

Pour veiller à ce que les clients soient pris en charge dans le monde entier, Azure gère plusieurs zones géographiques. Ces démarcations discrètes définissent la limite de la récupération d’urgence et de la résidence des données dans une ou plusieurs régions Azure. 

La réplication inter-région constitue l’un des piliers importants de la stratégie de continuité d’activité et de reprise d’activité d’Azure. Elle repose sur la réplication synchrone des applications et des données existantes en utilisant des zones de disponibilité de la région primaire Azure dans une optique de haute disponibilité. Elle réplique de façon asynchrone ces applications et données dans d’autres régions Azure pour la protection de la récupération d’urgence. 

![Image illustrant la haute disponibilité par réplication asynchrone des applications et données dans d’autres régions Azure pour la protection de la récupération d’urgence.](./media/cross-region-replication.png)

Certains services Azure tirent parti de la réplication inter-région pour assurer la continuité d’activité et la protection contre la perte de données. Azure fournit plusieurs [solutions de stockage](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) qui exploitent la réplication inter-région pour garantir la disponibilité des données. Par exemple, le [stockage géo-redondant (GRS, Geo-Redundant Storage) Azure](../storage/common/storage-redundancy.md#geo-redundant-storage) réplique automatiquement les données dans une région secondaire. Cette approche garantit la durabilité des données même si la région primaire n’est pas récupérable.

Tous les services Azure ne répliquent pas automatiquement les données. Tous ne se retirent pas non plus automatiquement d’une région défaillante pour effectuer une réplication croisée vers une autre région compatible. Dans ces scénarios, la récupération et la réplication doivent être configurées par le client. Ces exemples constituent des illustrations du *modèle de responsabilité partagée*. Il s’agit d’un pilier fondamental de votre stratégie de récupération d’urgence. Pour plus d’informations sur le modèle de responsabilité partagée et sur la continuité d’activité et reprise d’activité dans Azure, consultez [Gestion de la continuité d’activité dans Azure](business-continuity-management-program.md).

La responsabilité partagée devient le point central de votre prise de décision stratégique en matière de récupération d’urgence. Azure n’impose pas le recours à la réplication inter-région. Par ailleurs, il est possible d’utiliser des services pour créer de la résilience sans réplication croisée vers une autre région compatible. Toutefois, nous vous recommandons vivement de configurer vos services essentiels sur plusieurs régions pour tirer parti de [l’isolation](../security/fundamentals/isolation-choices.md) et améliorer la [disponibilité](overview.md). 

Dans le cas des applications qui prennent en charge plusieurs régions actives, nous vous conseillons d’utiliser plusieurs régions compatibles disponibles. Cette pratique garantit une disponibilité optimale pour les applications et un temps de récupération réduit lorsqu’un événement affecte la disponibilité. Dans la mesure du possible, concevez votre application de manière à obtenir une [résilience maximale](/azure/architecture/framework/resiliency/overview) et à faciliter la [récupération d’urgence](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="benefits-of-cross-region-replication"></a>Avantages de la réplication inter-région

L’architecture de la réplication inter-région des services et données peut être déterminée pour chaque service. Vous devez nécessairement adopter une approche de type analyse coût-avantage en fonction des exigences stratégiques et opérationnelles de votre organisation. Les avantages principaux et indirects de la réplication inter-région méritent d’être développés, car ils sont complexes et vastes. Les avantages sont les suivants :

- **Séquence de récupération régionale** : si une panne se produit à l’échelle d’une zone géographique, la priorité est donnée à la récupération d’une région pour chaque ensemble de régions compatible. Les applications déployées sur des ensembles de régions compatibles sont assurées de bénéficier d’une région prioritaire pour la récupération. Si une application est déployée dans plusieurs régions, pour lesquelles la réplication inter-région n’est pas activée, la récupération peut être retardée.
- **Mise à jour séquentielle** : les mises à jour planifiées du système Azure des régions compatibles sont échelonnées par ordre chronologique pour réduire le temps d’arrêt, l’impact des bogues et les défaillances logiques dans le rare cas d’une mise à jour défectueuse.
- **Isolation physique** : Azure s’efforce de garantir une distance minimale de 300 miles (483 kilomètres) entre les centres de données des régions compatibles, bien que ce ne soit pas possible dans toutes les zones géographiques. La séparation des centres de données réduit la probabilité que des catastrophes naturelles, des conflits civils, des pannes de courant ou des pannes de réseau physique puissent affecter plusieurs régions. L’isolation est soumise aux contraintes de chaque zone géographique (taille, disponibilité de l’alimentation et de l’infrastructure réseau, réglementations, etc.).
- **Résidence de données** : les régions se trouvent dans la même zone géographique que l’ensemble compatible associé (à l’exception des régions Brésil Sud et Singapour) pour répondre aux exigences de résidence des données à des fins de compétence fiscale et légale.

## <a name="azure-cross-region-replication-pairings-for-all-geographies"></a>Couplages de réplication inter-région Azure pour toutes les zones géographiques

Dans le cadre de la réplication inter-région, les régions sont couplées en fonction de leur proximité et d’autres facteurs.

**Paires régionales Azure**

| Geography | Paire régionale A | Paire régionale B |
| --- | --- | --- |
| Asie-Pacifique |Asie Est (Hong Kong, R.A.S.) | Asie Sud-Est (Singapour) |
| Australie |Australie Est |Sud-Australie Est |
| Australie |Centre de l’Australie |Centre de l’Australie 2\* |
| Brésil |Brésil Sud |États-Unis - partie centrale méridionale |
| Brésil |Brésil Sud-Est\* |Brésil Sud |
| Canada |Centre du Canada |Est du Canada |
| Chine |Chine du Nord |Chine orientale|
| Chine |Chine Nord 2 |Chine orientale 2|
| Europe |Europe Nord (Irlande) |Europe Ouest (Pays-Bas) |
| France |France Centre|France Sud\*|
| Allemagne |Allemagne Centre-Ouest |Allemagne Nord\* |
| Inde |Inde centrale |Inde Sud |
| Inde |Inde Ouest |Inde Sud |
| Japon |Japon Est |OuJapon Est |
| Corée du Sud |Centre de la Corée |Corée du Sud |
| Amérique du Nord |USA Est |USA Ouest |
| Amérique du Nord |USA Est 2 |USA Centre |
| Amérique du Nord |Centre-Nord des États-Unis |États-Unis - partie centrale méridionale |
| Amérique du Nord |USA Ouest 2 |Centre-USA Ouest |
| Norvège | Norvège Est | Norvège Ouest\* |
| Afrique du Sud | Afrique du Sud Nord |Afrique du Sud Ouest\* |
| Suisse | Suisse Nord |Suisse Ouest\* |
| Royaume-Uni |Ouest du Royaume-Uni |Sud du Royaume-Uni |
| Émirats Arabes Unis | Émirats arabes unis Nord | Émirats arabes unis Centre\* |
| Ministère de la défense des États-Unis |Est des États-Unis – US DoD\* |Centre des États-Unis – US DoD\* |
| Gouvernement américain |Gouvernement des États-Unis – Arizona\* |Gouvernement des États-Unis – Texas\* |
| Gouvernement américain |US Gov Iowa\* |Gouvernement américain - Virginie\* |
| Gouvernement américain |Gouvernement américain - Virginie\* |Gouvernement des États-Unis – Texas\* |

(\*) Certaines régions sont soumises à une restriction d’accès pour la prise en charge de scénarios client spécifiques, par exemple la récupération d’urgence dans le pays. Ces régions ne sont disponibles que sur demande en [créant une demande de support dans le portail Azure](https://portal.azure.com/#blade/Microsoft\_Azure\_Support/HelpAndSupportBlade/newsupportrequest).

> [!IMPORTANT]
> - La région Inde Ouest est jumelée dans une seule direction. La région secondaire de la région Inde Ouest est Inde Sud, mais la région secondaire de la région Inde Sud est Inde Centre.
> - La région Brésil Sud est unique, car elle est couplée avec une région située en dehors de sa zone géographique. La région secondaire de la région Brésil Sud est USA Centre Sud. La région secondaire de la région USA Centre Sud n’est pas Brésil Sud.

## <a name="next-steps"></a>Étapes suivantes

- [Régions prenant en charge les zones de disponibilité dans Azure](az-region.md)
- [Modèles de démarrage rapide](https://aka.ms/azqs)
