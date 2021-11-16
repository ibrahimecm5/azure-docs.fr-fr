---
title: Modèle de coût pour Azure NetApp Files | Microsoft Docs
description: Décrit le modèle de coût d’Azure NetApp Files pour faciliter la gestion des dépenses liées à ce service.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: b-juche
ms.openlocfilehash: 56091fb0c6ddfa205fc60f6aef06397a0314369e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053157"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modèle de coût pour Azure NetApp Files 

Une bonne compréhension du modèle de coût d’Azure NetApp Files vous aidera à gérer vos dépenses liées à ce service. 

Pour connaître le modèle de coût spécifique à la réplication interrégion, consultez la section [Modèle de coût pour la réplication interrégion](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Calcul de la consommation de capacité

Le service Azure NetApp Files est facturé en fonction de la capacité de stockage provisionnée, qui est allouée en créant des pools de capacité. Les pools de capacité sont facturés tous les mois selon un coût défini par Gio alloué par heure. L’allocation des pools de capacité est mesurée toutes les heures.  

Les pools de capacité doivent avoir au moins 4 Tio et peuvent être augmentés ou réduits par intervalles de 1 Tio. Les pools de capacité contiennent des volumes dont la taille est comprise entre un minimum de 100 Gio et un maximum de 100 Tio. Les volumes sont des quotas attribués qui sont soustraits de la taille provisionnée du pool de capacité. Pour un volume actif, le calcul de la consommation de capacité par rapport au quota concerne la capacité logique (effective), qu’il s’agisse de données du système de fichiers actif ou de données d’instantanés. Pour plus d’informations, consultez [Fonctionnement des instantanés Azure NetApp Files](snapshots-introduction.md). 

### <a name="pricing-examples"></a>Exemples de tarification

Cette section présente des exemples pour vous aider à comprendre le modèle de coût d’Azure NetApp Files.

#### <a name="example-1-one-month-cost-with-static-versus-dynamic-capacity-pool-provisioning"></a>Exemple 1 : coût d’un mois avec comparaison du provisionnement statique et dynamique du pool de capacité 

Si vos exigences en termes de taille de pool de capacité fluctuent (par exemple en raison de besoins variables de capacité ou de performances), envisagez de [Redimensionner dynamiquement vos volumes et pools de capacité](azure-netapp-files-resize-capacity-pools-or-volumes.md) pour équilibrer les coûts en fonction des besoins de capacité et de performances.

Par exemple, vous utilisez la capacité Premium 24 heures (1 jour) à 10 Tio, 96 heures (4 jours) à 24 Tio, quatre fois 6 heures (1 jour) à 5 Tio, 480 heures (20 jours) à 6 Tio et les heures restantes du mois à 0 Tio. Un profil de déploiement de la consommation cloud dynamique est différent d’un profil de consommation locale statique traditionnel : 

[ ![Graphique à barres qui compare le provisionnement dynamique et statique du pool de capacité.](../media/azure-netapp-files/cost-model-example-one-capacity.png) ](../media/azure-netapp-files/cost-model-example-one-capacity.png#lightbox)

Quand les coûts sont facturés à 0,000403 $ par Gio/heure ([tarification selon la région](https://azure.microsoft.com/pricing/details/netapp/)), la répartition des coûts mensuels ressemble à ceci :

*Provisionnement statique sur Premium (capacité/performances maximales)*

* 24 Tio x 720 heures x 0,000403 $ par Gio/heure = 7 130,97 $ par mois (237,70 $ par jour) 

*Provisionnement dynamique à l’aide du redimensionnement du pool de volume et de capacité* 

* 10 Tio x 24 heures x 0,000403 $ par Gio/heure = 99,04 $
* 24 Tio x 96 heures x 0,000403 $ par Gio/heure = 950,80 $
* 6 Tio x 480 heures x 0,000403 $ par Gio/heure = 1 188,50 $
* Total = **2 238,33 $**

[ ![Graphique à barres montrant la comparaison entre le modèle de coût de niveau de service statique et dynamique.](../media/azure-netapp-files/cost-model-example-one-pricing.png) ](../media/azure-netapp-files/cost-model-example-one-pricing.png#lightbox)

Ce scénario représente une économie mensuelle de 4 892,64 $ par rapport au provisionnement statique.

#### <a name="example-2-one-month-cost-with-and-without-dynamic-service-level-change"></a>Exemple 2 : coût d’un mois avec et sans modification du niveau de service dynamique

Si vos exigences en matière de taille de pool de capacité restent les mêmes, mais que les exigences en matière de performances fluctuent, envisagez de [modifier dynamiquement le niveau de service d’un volume](dynamic-change-volume-service-level.md). Vous pouvez provisionner et déprovisionner des pools de capacité de différents types tout au long du mois, en fournissant des performances juste-à-temps et en réduisant les coûts pendant les périodes où les performances ne sont pas nécessaires. 

Imaginez un scénario dans lequel la capacité requise est constante à 24 Tio. Toutefois, vos besoins de performances fluctuent entre 384 heures (16 jours) au niveau de service Standard, 120 heures (5 jours) au niveau de service Premium, 168 heures (7 jours) au niveau de service Ultra, puis de nouveau à 48 heures (2 jours) au niveau de service Standard. Dans ce scénario, un profil de déploiement de la consommation cloud dynamique est différent par rapport à un profil de consommation locale statique traditionnel : 

[ ![Graphique à barres montrant le provisionnement avec et sans modification du niveau de service dynamique.](../media/azure-netapp-files/cost-model-example-two-capacity.png) ](../media/azure-netapp-files/cost-model-example-two-capacity.png#lightbox)

Dans ce cas, quand les coûts sont facturés respectivement 0,000202 $ par Gio/heure (Standard), 0,000403 $ par Gio/heure (Premium) et 0,000538 $ par Gio/heure (Ultra) ([tarification en fonction de la région](https://azure.microsoft.com/pricing/details/netapp/)), les coûts mensuels se répartissent comme suit : 

*Provisionnement statique sur un niveau de service Ultra (performances maximales)*

* 24 Tio x 720 heures x 0,000538 $ par Gio/heure = 9 519,76 $ par mois (317,33 $ par jour) 
 
*Provisionnement dynamique à l’aide de la modification du niveau de service dynamique*

* 24 Tio x 384 heures x 0,000202 $ par Gio/heure = 1 901,31 $  
* 24 Tio x 120 heures x 0,000403 $ par Gio/heure = 1 188,50 $  
* 24 Tio x 168 heures x 0,000538 $ par Gio/heure = 2 221,28 $  
* 24 Tio x 48 heures x 0,000202 $ par Gio/heure = 238,29 $ 
* Total = **5 554,37 $** 

[ ![Graphique à barres montrant la comparaison du modèle de coût de niveau de service statique et dynamique.](../media/azure-netapp-files/cost-model-example-two-pricing.png) ](../media/azure-netapp-files/cost-model-example-two-pricing.png#lightbox)

Ce scénario représente une économie mensuelle de 3 965,39 $ par rapport au provisionnement statique.

## <a name="capacity-consumption-of-snapshots"></a>Consommation de capacité des captures instantanées 

La consommation de capacité des captures instantanées dans Azure NetApp Files est facturée au niveau du quota du volume parent.  Par conséquent, elle applique le même taux de facturation que le pool de capacité auquel appartient le volume.  Toutefois, contrairement au volume actif, la consommation des captures instantanées est mesurée sur la base de la capacité incrémentielle consommée.  Les captures instantanées Azure NetApp Files sont différentielles par nature. Selon le taux de modification des données, les captures instantanées consomment souvent une capacité sensiblement inférieure à la capacité logique du volume actif. Par exemple, supposons que vous disposiez d’une capture instantanée d’un volume de 500 Gio contenant uniquement 10 Gio de données différentielles. 

La consommation de capacité comptabilisée dans le quota de volume pour le système de fichiers actif et l’instantané serait de 510 Gio, et non 1 000 Gio. En règle générale, il est recommandé de disposer d’une capacité de 20 % pour conserver une semaine de données d’instantané (en fonction de la fréquence des instantanés et des taux quotidiens de modification en mode bloc des applications). 

Le diagramme suivant illustre ces concepts. 

* Prenons un pool de capacité d’une capacité provisionnée de 40 Tio. Il comporte trois volumes :    
    * Le volume 1 se voit attribuer un quota de 20 Tio et présente une consommation de 13 Tio (12 Tio de données actives, 1 Tio d’instantanés).
    * Le volume 2 se voit attribuer un quota de 1 Tio et présente une consommation de 450 Gio.
    * Le volume 3 se voit attribuer un quota de 14 Tio, mais présente une consommation de 8,8 Tio (8 Tio de données actives, 800 Gio d’instantanés).   
* Le pool de capacité est limité à 40 Tio de capacité (quantité provisionnée). Une capacité de 22,25 Tio est consommée (13 Tio, 450 Gio et 8,8 Tio de quota des volumes 1, 2 et 3). La capacité restante du pool s’élève à 17,75 Tio.   

[ ![Diagramme montrant le pool de capacités comportant trois volumes.](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png) ](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* [Tarification Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Niveaux de service pour Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Modèle de coût pour la réplication inter-région](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [Présentation du quota de volume](volume-quota-introduction.md)
* [Surveiller la capacité d'un volume](monitor-volume-capacity.md)
* [Redimensionner le pool de capacité ou un volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [Gérer la facturation en utilisant des étiquettes](manage-billing-tags.md)
* [Questions fréquentes (FAQ) sur la gestion de la capacité](faq-capacity-management.md)
* [Calculatrice de prix d’Azure NetApp Files](https://azure.microsoft.com/pricing/calculator/?service=netapp)
