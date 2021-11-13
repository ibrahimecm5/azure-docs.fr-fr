---
title: Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?
description: Azure Hybrid Benefit est un avantage qui vous permet d’apporter vos licences locales SQL Server et Windows Server basées sur les cœurs avec Software Assurance (ou abonnement) active sur Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 8fb630db1d14fe7af1008979eb5b72ee62287d6a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235693"
---
# <a name="what-is-centrally-managed-azure-hybrid-benefit"></a>Qu’est-ce qu’Azure Hybrid Benefit managé de manière centralisée ?

Azure Hybrid Benefit est un avantage en termes de licences qui vous permet de réduire considérablement les coûts d’exécution de vos charges de travail dans le cloud. Il fonctionne en vous permettant d’utiliser sur Azure vos licences locales Windows Server et SQL Server avec Software Assurance. Pour plus d’informations, consultez [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Vous pouvez gérer de manière centralisée votre Azure Hybrid Benefit pour SQL Server sur l’étendue d’un abonnement Azure entier ou d’un compte de facturation global. Voici son fonctionnement général :

1. Commencez par vérifier que toutes vos machines virtuelles SQL Server sont visibles par vous et par Azure en activant l’inscription automatique des images SQL Server installées automatiquement avec l’extension IaaS. Pour plus d’informations, consultez [Inscrire plusieurs machines virtuelles SQL dans Azure avec l’extension SQL IaaS Agent](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).
1. Sous **Gestion des coûts + facturation** dans le portail Azure, vous (en tant qu’administrateur de facturation) choisissez l’étendue et le nombre de licences éligibles que vous souhaitez attribuer pour couvrir les ressources dans l’étendue.  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="Capture d’écran montrant la définition d’une étendue et l’attribution de licences." lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

- Pour chaque heure d’exécution des ressources dans l’étendue définie, Azure leur attribue automatiquement les licences et applique les remises tarifaires appropriées. Différentes ressources peuvent être couvertes chaque heure.
- Toute utilisation dépassant le nombre de licences attribuées est facturée au tarif habituel du paiement à l’utilisation.
- Lorsque vous choisissez de gérer l’avantage en attribuant des licences au niveau de l’étendue, vous ne pouvez plus gérer de manière individuelle les ressources qui se trouvent dans l’étendue.

L’option initiale de gestion de niveau ressource pour activer Azure Hybrid Benefit reste disponible pour SQL Server et est actuellement la seule option possible pour Windows Server. Cela nécessite qu’un rôle DevOps sélectionne l’avantage pour chaque ressource (comme une machine virtuelle SQL Database ou Windows Server) qu’il crée ou gère. Une remise est alors appliquée au coût horaire de la ressource. Pour plus d'informations, voir [Avantage d’Azure Hybrid pour Windows Server](../../azure-sql/azure-hybrid-benefit.md).

L’activation de la gestion centralisée d’Azure Hybrid Benefit pour SQL Server sur l’étendue de l’abonnement ou du compte est actuellement en préversion. Cette option est disponible uniquement pour les clients Entreprise. Nous prévoyons d’étendre cette possibilité aux clients qui utilisent Windows Server et d’autres produits.

## <a name="qualifying-sql-server-licenses"></a>Éligibilité des licences SQL Server

Les licences cœur SQL Server Entreprise et Standard avec la Software Assurance active sont éligibles à cet avantage. Les abonnements avec une licence cœur SQL Server y sont également éligibles.

## <a name="qualifying-azure-resources"></a>Éligibilité des ressources Azure

La gestion centralisée d’Azure Hybrid Benefit sur une étendue couvre les ressources Azure SQL courantes suivantes :

- BASES DE DONNÉES SQL
- Instances managées SQL
- Pools élastiques SQL
- SQL Server sur machines virtuelles Azure

La gestion de niveau ressource d’Azure Hybrid Benefit peut également couvrir toutes les ressources citées ci-dessus. C’est aujourd’hui la seule option possible pour couvrir les ressources suivantes :

- Hôtes dédiés Azure
- Azure Data Factory SQL Server Integration Services (SSIS)

## <a name="centralized-scope-level-management-advantages"></a>Avantages de la gestion centralisée sur une étendue

Vous bénéficiez des avantages suivants :

- **Approche plus simple avec plus de contrôle** : l’administrateur de facturation attribue directement les licences disponibles à une ou plusieurs étendues Azure. L’approche initiale, à grande échelle, implique une utilisation d’Azure Hybrid Benefit coordonnée entre de multiples ressources et propriétaires DevOps.
- **Moyen facile d’optimiser les coûts** : un administrateur peut superviser l’utilisation d’Azure Hybrid Benefit et ajuster directement les licences attribuées sur Azure. Imaginons qu’un administrateur trouve une opportunité de faire davantage d’économies en attribuant plus de licences sur Azure. Il en parle alors au service achats pour vérifier la disponibilité des licences. Ensemble, ils décident qu’ils peuvent aisément attribuer des licences sur Azure pour commencer à faire des économies.
- **Méthode plus efficace de gestion des coûts durant les pics d’utilisation** : vous pouvez facilement effectuer un scale-up de la même ressource ou ajouter des ressources supplémentaires de manière temporaire, pendant les périodes de forte utilisation. Vous n’avez pas besoin d’affecter plus de licences SQL Server (par exemple, pour les périodes de clôture ou la hausse des achats de fin d’année). Pour les pics de charge de travail de courte durée, les coûts de paiement à l’utilisation correspondant à la capacité supplémentaire seront parfois inférieurs au coût d’acquisition d’autres licences pour appliquer Azure Hybrid Benefit à la capacité. Gérer l’avantage au niveau d’une étendue plutôt qu’au niveau d’une ressource vous aide à déterminer l’option la plus intéressante sur la base de l’utilisation globale.
- **Séparation claire des tâches pour garantir la conformité** : dans le modèle Azure Hybrid Benefit de niveau ressource, les propriétaires de ressources peuvent choisir d’utiliser Azure Hybrid Benefit sans vérifier la disponibilité des licences (auprès du service achats) ou l’utilisation globale des licences SQL (y compris localement). La gestion de niveau étendue d’Azure Hybrid Benefit permet d’éviter les cas où des propriétaires de ressources choisissent Azure Hybrid Benefit alors qu’il n’y a plus de licences disponibles. Les administrateurs de facturation qui gèrent Azure Hybrid Benefit de manière centralisée peuvent aisément vérifier avec les services achats et de gestion des ressources logicielles combien il reste de licences disponibles à attribuer sur Azure. Ce point est illustré dans le diagramme suivant.

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="Diagramme montrant la séparation des tâches." border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>Attribution de licences aux ressources Azure

Les licences SQL Server Entreprise (cœur) et SQL Server Standard (cœur) avec la Software Assurance activée sont éligibles, mais, comme cela est décrit dans les [Conditions générales des produits](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS), des taux de conversion différents s’appliquent lorsque vous les apportez sur Azure avec Azure Hybrid Benefit.

Souvenez-vous de cette règle importante : une seule licence SQL Server Édition Entreprise a la même couverture que _quatre_ licences SQL Server Édition Standard, pour tous les types de ressources Azure SQL éligibles.

Pour expliquer comment cela fonctionne, nous utiliserons le terme NCL ou _normalized core license_ (licence cœur normalisée). Dans la logique de la règle ci-dessus, une licence cœur SQL Server Standard donne lieu à une seule NCL. Une licence cœur SQL Server Entreprise donne lieu à quatre NCL. Par exemple, si vous attribuez quatre licences cœur SQL Server Entreprise et sept licences cœur SQL Server Standard, la couverture totale et l’opportunité de remise Azure Hybrid Benefit équivalent à 23 NCL (4\*4 + 7\*1).

Le tableau suivant indique le nombre de NCL dont vous avez besoin pour bénéficier d’une remise complète sur les coûts de licence SQL Server de différents types de ressources. La gestion de niveau étendue d’Azure Hybrid Benefit applique strictement les règles des conditions générales des produits, résumées ci-dessous.

| **Azure Data Service** | **Niveau de service** | **Nombre requis de NCL** |
| --- | --- | --- |
| Pool d’instances ou SQL Managed Instance | Critique pour l’entreprise | 4 par vCore |
| Pool d’instances ou SQL Managed Instance | Usage général | 1 par vCore |
| Pool élastique ou SQL Database<sup>1</sup> | Critique pour l’entreprise | 4 par vCore |
| Pool élastique ou SQL Database<sup>1</sup> | Usage général | 1 par vCore |
| Pool élastique ou SQL Database<sup>1</sup> | Hyperscale | 1 par vCore |
| Azure Data Factory SQL Server Integration Services | Enterprise | 4 par vCore |
| Azure Data Factory SQL Server Integration Services | Standard | 1 par vCore |
| Machines virtuelles SQL Server<sup>2</sup> | Enterprise | 4 par vCPU |
| Machines virtuelles SQL Server<sup>2</sup> | Standard | 1 par vCPU |

<sup>1</sup> *Azure Hybrid Benefit n’est pas disponible dans le niveau de calcul serverless d’Azure SQL Database.*

<sup>2</sup> *Sous réserve d’un minimum de quatre licences vCore par machine virtuelle.*

## <a name="ongoing-scope-level-management"></a>Processus régulier de la gestion de niveau étendue

Nous recommandons de gérer Azure Hybrid Benefit de niveau étendue en répétant régulièrement le processus suivant :

- Prenez des renseignements dans votre organisation pour déterminer combien de ressources Azure SQL et de vCore seront utilisés au cours des prochains mois, trimestre ou année.
- Travaillez en collaboration avec vos services achats et de gestion des ressources logicielles pour vérifier si un nombre suffisant de licences cœur SQL avec Software Assurance sont disponibles. L’avantage permet d’utiliser les licences prenant en charge la migration des charges de travail jusqu’à 180 jours, à la fois localement et dans Azure. Par conséquent, ces licences peuvent être comptabilisées comme disponibles.
- Attribuez les licences disponibles nécessaires pour couvrir votre utilisation actuelle _et_ votre utilisation planifiée durant la prochaine période.
- Supervisez l’utilisation des licences attribuées.
  - Si l’utilisation est proche de 100 %, consultez vos collègues dans l’organisation pour connaître la croissance prévue de l’utilisation. S’il y a lieu, vérifiez la disponibilité des licences, puis attribuez des licences supplémentaires à l’étendue.
  - Si l’utilisation atteint 100 %, vous utilisez sans doute plus de ressources que le nombre de licences attribuées. Revenez dans [Créer une expérience d’attribution de licence](create-sql-license-assignments.md) et analysez l’utilisation indiquée par Azure. Passez également en revue les détails de l’utilisation des licences. Ensuite, discutez avec vos collègues afin de vérifier ou d’obtenir des licences avant de les attribuer à l’étendue pour avoir une meilleure couverture.
- Répétez régulièrement le processus proactif.

## <a name="next-steps"></a>Étapes suivantes

- Effectuez le tutoriel [Gérer et optimiser Azure Hybrid Benefit pour SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Découvrez comment [migrer vers Azure Hybrid Benefit géré de manière centralisée](transition-existing.md).
- Passez en revue le [FAQ sur Azure Hybrid Benefit géré de manière centralisée](faq-azure-hybrid-benefit-scope.yml).