---
title: Utiliser la communication simplifiée des nœuds de calcul
description: Découvrez comment le service Azure Batch simplifie la gestion de l’infrastructure de pool Batch et comment s’y abonner.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 9bd53bcab9ccb9403d8d851300ba987a23eacc11
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270808"
---
# <a name="use-simplified-compute-node-communication"></a>Utiliser la communication simplifiée des nœuds de calcul

Un pool Azure Batch contient un ou plusieurs nœuds de calcul qui exécutent des charges de travail spécifiées par l’utilisateur sous la forme de tâches Batch. Pour activer la fonctionnalité Batch et la gestion de l’infrastructure de pool Batch, les nœuds de calcul doivent communiquer avec le service Azure Batch.

Ce document décrit les modifications à venir quant à la façon dont le service Azure Batch communique avec les nœuds de calcul de pool Azure Batch, les modifications de configuration réseau qui peuvent être nécessaires, et comment inclure ou exclure vos comptes Batch dans l’utilisation de la nouvelle fonctionnalité de communication simplifiée des nœuds de calcul pendant la période de préversion publique.

> [!IMPORTANT]
> La prise en charge de la communication simplifiée des nœuds de calcul dans Azure Batch est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il n’est pas obligatoire d’adopter cette fonctionnalité pour le moment. Toutefois, à l’avenir, l’utilisation de la communication simplifiée des nœuds de calcul sera obligatoire pour tous les comptes Batch. À ce moment-là, une notification officielle de mise hors service sera fournie, avec la possibilité de migrer vos pools Batch avant que cela se produise.

## <a name="compute-node-communication-changes"></a>Modifications de la communication des nœuds de calcul

Le service Azure Batch simplifie la gestion de l’infrastructure de pool Batch pour le compte des utilisateurs. La nouvelle méthode de communication réduit la complexité et l’étendue des connexions réseau entrantes et sortantes requises dans les opérations de base.

Les pools Batch dans les comptes qui n’ont pas été activés pour la communication simplifiée des nœuds de calcul requièrent les règles réseau dans les groupes de sécurité réseau (NSG), les routes définies par l’utilisateur (UDR) et les pare-feu suivants lors de la [création d’un pool dans un réseau virtuel](batch-virtual-network.md) :

- Communications entrantes :
  - Ports de destination 29876, 29877 sur TCP à partir de BatchNodeManagement.*région*

- Communications sortantes :
  - Ports de destination 443 sur TCP vers Storage.*région*
  - Port de destination 443 sur TCP vers BatchNodeManagement.*région* pour certaines charges de travail qui requièrent la communication avec le service Batch, telles que les tâches du gestionnaire de travaux

Avec le nouveau modèle, les pools Batch dans les comptes qui utilisent la communication simplifiée des nœuds de calcul requièrent les règles réseau dans les NSG, les UDR et les pare-feu suivants :

- Communications entrantes :
  - Aucun

- Communications sortantes :
  - Ports de destination 443 sur TCP vers BatchNodeManagement.*région*

Les exigences sortantes d’un compte Batch peuvent être découvertes à l’aide de l’[API Lister les points de terminaison des dépendances de réseau sortant](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints). Cette API indique l’ensemble de base des dépendances, en fonction du modèle de communication du pool de comptes Batch. Les charges de travail propres à l’utilisateur peuvent nécessiter des règles supplémentaires telles que l’ouverture du trafic vers d’autres ressources Azure (par exemple Stockage Azure pour Packages d’application, Azure Container Registry, etc.) ou des points de terminaison comme le référentiel de packages Microsoft pour la fonctionnalité de montage de système de fichiers virtuel.  

## <a name="benefits-of-the-new-model"></a>Avantages du nouveau modèle

Les utilisateurs d’Azure Batch qui [choisissent d’adopter le nouveau modèle de communication](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) bénéficient de la simplification des connexions et des règles réseau.

La communication simplifiée des nœuds de calcul permet de réduire les risques de sécurité en éliminant la nécessité d’ouvrir des ports pour les communications entrantes à partir d’Internet. Une seule règle de trafic sortant vers une étiquette de service connue est requise pour l’opération de base de référence.

Le nouveau modèle fournit également un contrôle plus précis de l’exfiltration des données, puisque les communications sortantes vers Storage.*région* ne sont plus nécessaires. Vous pouvez verrouiller explicitement les communications sortantes vers Stockage Azure si nécessaire pour votre flux de travail (par exemple les comptes de stockage AppPackage, d’autres comptes de stockage pour les fichiers de ressources ou les fichiers de sortie, ou d’autres scénarios similaires).

Même si vos charges de travail ne sont pas affectées actuellement par les modifications (comme décrit dans la section suivante), vous pouvez toujours [choisir d’utiliser la communication simplifiée des nœuds de calcul](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) dès maintenant. Cela garantira que vos charges de travail Batch seront prêtes pour toute amélioration future activée par ce modèle.

## <a name="scope-of-impact"></a>Étendue de l’impact

Dans de nombreux cas, ce nouveau modèle de communication n’affecte pas directement vos charges de travail Batch. Toutefois, la communication simplifiée des nœuds de calcul aura un impact dans les cas suivants :

- Les utilisateurs qui spécifient un réseau virtuel dans le cadre de la création d’un pool Batch et effectuent l’une des opérations suivantes, ou les deux :
   - Désactivation explicite des règles de trafic réseau sortant qui sont incompatibles avec la communication simplifiée des nœuds de calcul
   - Utilisation de routes définies par l’utilisateur et de règles de pare-feu qui sont incompatibles avec la communication simplifiée des nœuds de calcul
- Les utilisateurs qui activent des pare-feu logiciels sur des nœuds de calcul et désactivent explicitement le trafic sortant dans les règles de pare-feu logiciel qui sont incompatibles avec la communication simplifiée des nœuds de calcul.

Si l’un de ces cas vous concerne, et que vous souhaitez adopter la préversion, suivez les étapes décrites dans la section suivante pour vous assurer que vos charges de travail Batch peuvent toujours fonctionner sous le nouveau modèle.

### <a name="required-network-configuration-changes"></a>Modifications la configuration réseau nécessaires

Pour les utilisateurs concernés, les étapes suivantes sont nécessaires pour migrer vers le nouveau modèle de communication :

1. Vérifiez que votre configuration réseau telle qu’applicable aux pools Batch (NSG, UDR, pare-feu, etc.) comprend une union des modèles (autrement dit, les règles de réseau avant et après la communication simplifiée des nœuds de calcul). Au minimum, ces règles doivent être les suivantes :
   - Communications entrantes :
     - Ports de destination 29876, 29877 sur TCP à partir de BatchNodeManagement.*région*
   - Communications sortantes :
     - Ports de destination 443 sur TCP vers Storage.*région*
     - Ports de destination 443 sur TCP vers BatchNodeManagement.*région*
1. Si votre flux de travail nécessite des scénarios d’entrée ou de sortie supplémentaires, vous devrez vous assurer que vos règles reflètent ces exigences.
1. [Adoptez la communication simplifiée des nœuds de calcul](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication), comme décrit ci-dessous.
1. Utilisez l’une des options suivantes pour mettre à jour vos charges de travail afin d’utiliser le nouveau modèle de communication. Quelle que soit la méthode utilisée, gardez à l’esprit que les pools sans adresses IP publiques ne sont pas affectés et ne peuvent pas utiliser actuellement la communication simplifiée des nœuds de calcul. Consultez la section [Limitations actuelles](#current-limitations).
   1. Créez de nouveaux pools et vérifiez qu’ils fonctionnent correctement. Migrez votre charge de travail vers les nouveaux pools et supprimez les pools précédents.
   1. Redimensionnez tous les pools existants sur zéro nœud, puis réeffectuez un scale-out.
1. Après avoir confirmé que tous les pools précédents ont été supprimés ou ont été mis à l’échelle à zéro puis ont subi un scale-out, interrogez l’[API Lister les points de terminaison des dépendances de réseau sortant](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) pour confirmer qu’il n’existe aucune règle de trafic sortant vers Stockage Azure pour la région (à l’exclusion des éventuels comptes de stockage automatique s’ils sont liés à votre compte Batch).
1. Modifiez au minimum toutes les configurations réseau applicables aux règles de communication simplifiée des nœuds de calcul (notez toutes les règles supplémentaires nécessaires, comme indiqué ci-dessus) :
   - Communications entrantes :
     - Aucun
   - Communications sortantes :
     - Ports de destination 443 sur TCP vers BatchNodeManagement.*région*

Si vous suivez ces étapes, mais que vous souhaitez ultérieurement cesser d’utiliser la communication simplifiée des nœuds de calcul, vous devrez effectuer les opérations suivantes :

1. [Renoncer à la communication simplifiée des nœuds de calcul](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication), comme décrit ci-dessous.
1. Migrer votre charge de travail vers de nouveaux pools, ou redimensionner les pools existants et réeffectuer un scale-out (voir l’étape 4 ci-dessus).
1. Vérifier que tous vos pools n’utilisent plus la communication simplifiée des nœuds de calcul à l’aide de l’[API Lister les points de terminaison des dépendances de réseau sortant](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints). Vous devez voir une règle de trafic sortant vers Stockage Azure pour la région (indépendamment des comptes de stockage automatique liés à votre compte Batch).

## <a name="opt-your-batch-account-in-or-out-of-simplified-compute-node-communication"></a>Adopter ou renoncer à la communication simplifiée des nœuds de calcul pour votre compte Batch

Pour adopter ou renoncer à la communication simplifiée des nœuds de calcul pour un compte Batch, [créez une demande de support dans le portail Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!IMPORTANT]
> Lorsque vous adoptez (ou renoncez à) la communication simplifiée des nœuds de calcul, la modification n’affecte que le comportement ultérieur. Tous les pools Batch contenant des nœuds de calcul non nuls qui ont été créés avant la demande ne sont pas affectés et utilisent le modèle qui était actif avant la demande. Pour plus d’informations sur la façon de migrer des pools existants avant d’adopter ou de renoncer à cette fonctionnalité, consultez les étapes de migration.

Utilisez les options suivantes lors de la création de votre demande.

:::image type="content" source="media/simplified-compute-node-communication/support-request-opt-in.png" alt-text="Capture d’écran d’une demande de support ayant pour objet l’adoption de la communication simplifiée des nœuds de calcul.":::

1. Pour **Type de problème**, sélectionnez **Technique**.
1. Pour **Type de service**, sélectionnez **Service Batch**.
1. Pour **Ressource**, sélectionnez le compte Batch concerné par cette demande.
1. Pour **Synthèse** :
   - Pour adopter la fonctionnalité, tapez « Activer la communication simplifiée des nœuds de calcul ».
   - Pour renoncer à la fonctionnalité, tapez « Désactiver la communication simplifiée des nœuds de calcul ».
1. Pour **Type de problème**, sélectionnez **Comptes Batch**.
1. Pour **Sous-type de problème**, sélectionnez **Autres problèmes avec les comptes Batch**.
1. Sélectionnez **Suivant**, puis à nouveau **Suivant** pour accéder à la page **Détails supplémentaires**.
1. Dans **Détails supplémentaires**, vous pouvez éventuellement spécifier que vous souhaitez activer tous les comptes Batch dans votre abonnement ou dans plusieurs abonnements. Dans ce cas, veillez à inclure ici les ID d’abonnement.
1. Effectuez les autres sélections requises dans la page, puis sélectionnez **Suivant**.
1. Passez en revue les détails de votre demande, puis sélectionnez **Créer** pour envoyer votre demande de support.

Une fois votre demande soumise, vous serez averti dès que la fonctionnalité aura été (dés)activée pour le compte.

## <a name="current-limitations"></a>Limites actuelles

Voici les limitations connues pour les comptes qui adoptent la communication simplifiée des nœuds de calcul :

- La [création de pools sans adresses IP publiques](batch-pool-no-public-ip-address.md) n’est actuellement pas prise en charge pour les comptes qui ont adopté la fonctionnalité.
- Les pools créés précédemment sans adresses IP publiques n’utiliseront pas la communication simplifiée des nœuds de calcul, même si la fonctionnalité a été activée pour le compte Batch.
- Les [comptes Batch privés](private-connectivity.md) peuvent adopter la communication simplifiée des nœuds de calcul, mais les pools Batch créés par ces comptes Batch doivent avoir des adresses IP publiques afin d’utiliser la communication simplifiée des nœuds de calcul.
- Les pools de configuration de service cloud ne sont actuellement pas pris en charge pour la communication simplifiée des nœuds de calcul, et sont généralement dépréciés. Nous vous recommandons d’utiliser Configuration de la machine virtuelle pour vos pools Batch. Pour plus d’informations, consultez [Migrer la configuration des pools Batch des Services cloud vers une machine virtuelle](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [pools dans les réseaux virtuels](batch-virtual-network.md).
- Découvrez comment [créer un pool Batch avec des adresses IP publiques spécifiées](create-pool-public-ip.md).
