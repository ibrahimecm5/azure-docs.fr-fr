---
title: Fichier include
description: Fichier include
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 10/20/2021
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: b5d51a00a35fa34236a62c48f1ee01402ca2f1c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521036"
---
Le Calcul Azure propose des tailles de machines virtuelles qui sont isolées pour un type de matériel spécifique et dédiées à un seul et même client. Les tailles isolées vivent et fonctionnent sur une génération de matériel spécifique et seront déconseillées lors de sa mise hors service.

Les tailles de machines virtuelles sont les mieux adaptées aux charges de travail qui nécessitent un niveau élevé d’isolation par rapport aux charges de travail des autres clients pour des raisons de conformité et d’exigences réglementaires.  L’utilisation d’une taille isolée garantit que votre machine virtuelle sera la seule en cours d’exécution sur cette instance de serveur spécifique. 


En outre, comme les machines virtuelles de taille isolée sont volumineuses, les clients peuvent choisir de subdiviser les ressources de ces machines virtuelles à l’aide de la [prise en charge des machines virtuelles imbriquées par Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Les offres actuelles de machines virtuelles isolées sont les suivantes :
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_E104i_v5
* Standard_E104is_v5
* Standard_E104id_v5
* Standard_E104ids_v5
* Standard_F72s_v2
* Standard_M128ms
* Standard_DC8_v2


> [!NOTE]
> Les tailles de machines virtuelles isolées ont une durée de vie matérielle limitée. Pour plus d’informations, voir ci-dessous.

## <a name="deprecation-of-isolated-vm-sizes"></a>Dépréciation des tailles de machines virtuelles isolées

Les tailles de machines virtuelles isolées ont une durée de vie matérielle limitée. Azure publiera des rappels 12 mois avant la date officielle de dépréciation des tailles et fournira une offre isolée mise à jour à votre intention.

| Taille | Date de mise hors service de l’isolation | 
| --- | --- |
| Standard_DS15_v2  | 15 mai 2021      |
| Standard_D15_v2   | 15 mai 2021      |
| Standard_G5       | 15 février 2022 |
| Standard_GS5      | 15 février 2022 |
| Standard_E64i_v3  | 15 février 2022 |
| Standard_E64is_v3 | 15 février 2022 |
| Standard_DC8_v2   | 15 février 2022 |


## <a name="faq"></a>Questions fréquentes (FAQ)
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>Q : La taille sera-t-elle mise hors service ou cela concerne-t-il seulement sa fonctionnalité « isolation » ?
**R** : Actuellement, seule la fonctionnalité d'isolation des tailles de machines virtuelles est mise hors service. Les tailles isolées déconseillées continueront d'exister sous une forme non isolée. Si l’isolation n’est pas nécessaire, aucune action n’est requise et la machine virtuelle continue de fonctionner comme prévu.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q : Y a-t-il un temps d’arrêt lorsque ma machine virtuelle arrive sur un matériel non isolé ?
**R** : Si l’isolation n’est pas nécessaire, aucune action n’est requise et il n’y aura pas de temps d’arrêt. En revanche, si une isolation est nécessaire, notre annonce inclura la taille de remplacement recommandée. La sélection de la taille de remplacement obligera nos clients à redimensionner leurs machines virtuelles.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Q : Existe-t-il un écart de coût pour le déplacement vers une machine virtuelle non isolée ?
**R** : Non

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q : Quand les autres tailles isolées vont-elles être mises hors service ?
**R** : Nous enverrons des rappels 12 mois avant la dépréciation officielle de la taille isolée. Notre dernière annonce comprend la mise hors service de la fonctionnalité d'isolation de Standard_G5, Standard_GS5, Standard_E64i_v3 et Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q : Je suis un client Azure Service Fabric qui s’appuie sur les niveaux de durabilité Argent ou Or. Ce changement a-t-il un impact sur moi ?
**R** : Non. Les garanties fournies par les [niveaux de durabilité](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de Service Fabric continuent de fonctionner même après ce changement. Si vous avez besoin d’une isolation matérielle physique pour d’autres raisons, vous devrez peut-être effectuer l’une des actions décrites ci-dessus. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Q : Quelles sont les étapes majeures de la mise hors service de l’isolation D15_v2 ou DS15_v2 ? 
**R** : 
 
| Date | Action |
|---|---| 
| 15 mai 2020<sup>1</sup> | Annonce de la mise hors service de l'isolation de D/DS15_v2| 
| 15 mai 2021 | Garantie d’isolation D/DS15_v2 retirée| 

<sup>1</sup> Les clients existants utilisant ces tailles recevront un e-mail d'annonce avec des instructions détaillées sur les étapes suivantes.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>Q : Quels sont les jalons pour la mise hors service de l'isolation de G5, Gs5, E64i_v3 et E64is_v3 ? 
**R** : 
 
| Date | Action |
|---|---|
| 15 février 2021<sup>1</sup> | Annonce de la mise hors service de l'isolation de G5/GS5/E64i_v3/E64is_v3 |
| 28 février 2022 | Mise hors service de la garantie d'isolation de G5/GS5/E64i_v3/E64is_v3 |

<sup>1</sup> Les clients existants utilisant ces tailles recevront un e-mail d'annonce avec des instructions détaillées sur les étapes suivantes.  

## <a name="next-steps"></a>Étapes suivantes

Les clients peuvent également choisir de subdiviser les ressources de ces machines virtuelles isolées à l’aide de la [prise en charge d’Azure pour les machines virtuelles imbriquées](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
