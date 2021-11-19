---
title: Vue d’ensemble de la résolution des problèmes des pools de disques Azure (préversion)
description: Résolution des problèmes liés aux pools de disques Azure. Découvrez les codes d’échec courants et comment les résoudre.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 878cec7021ebcd7c3b63ae4af1f29a0793977280
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277276"
---
# <a name="troubleshoot-azure-disk-pools-preview"></a>Résolution des problèmes des pools de disques Azure (préversion)

Cet article répertorie certains codes d’échec courants liés aux pools de disques Azure (préversion). Il fournit également des résolutions possibles et certaines clarifications sur les états des pools de disques.

## <a name="disk-pool-status"></a>État du pool de disques

Les pools de disques et les cibles iSCSI présentent chacun quatre états : **Inconnu**, **En cours d’exécution**, **En cours de mise à jour** et **Arrêté (libéré)** .

**Inconnu** signifie que la ressource est dans un état incorrect ou inconnu. Pour tenter une récupération, effectuez une opération de mise à jour sur la ressource (telle que l’ajout ou la suppression de disques/numéros d’unités logiques) ou supprimez et redéployez votre pool de disques.

**En cours d’exécution**, la ressource est en cours d’exécution et est dans un état sain.

**En cours de mise à jour** signifie que la ressource fait l’objet d’une mise à jour. Cela se produit généralement lors du déploiement ou de l’application d’une mise à jour comme l’ajout de disques ou de numéros d’unités logiques.

**Arrêté (libéré)** signifie que la ressource est arrêtée et que ses ressources sous-jacentes ont été libérées. Vous pouvez redémarrer la ressource pour récupérer votre pool de disques ou votre cible iSCSI.

## <a name="common-failure-codes-when-deploying-a-disk-pool"></a>Codes d’erreur courants lors du déploiement d’un pool de disques
 
|Code  |Description  |
|---------|---------|
|UnexpectedError     |Se produit généralement lorsqu’une erreur principale irrécupérable se produit. Recommencez le déploiement. Si le problème persiste, contactez le support Azure et fournissez l’ID de suivi du message d’erreur.         |
|DeploymentFailureZonalAllocationFailed     |Cela se produit quand Azure ne dispose plus de capacité pour approvisionner une machine virtuelle dans la région/zone spécifiée. Réessayez le déploiement à un autre moment.         |
|DeploymentFailureQuotaExceeded     |L’abonnement utilisé pour déployer le pool de disques est en dehors du quota de cœurs de machines virtuelles dans cette région. Vous pouvez [demander une augmentation des limites de quota des processeurs virtuels de chaque série de machines virtuelles Azure](../azure-portal/supportability/per-vm-quota-requests.md) pour la série Dsv3.         |
|DeploymentFailurePolicyViolation     |Une stratégie sur l’abonnement a empêché le déploiement des ressources Azure nécessaires à la prise en charge d’un pool de disques. Consultez l’erreur pour plus d’informations.         |
|DeploymentTimeout     |Cela se produit lorsque le déploiement de l’infrastructure de pools de disques est bloqué et ne se termine pas dans le délai imparti. Recommencez le déploiement. Si le problème persiste, contactez le support Azure et fournissez l’ID de suivi du message d’erreur.         |
|GoalStateApplicationTimeoutError     |Se produit lorsque l’infrastructure du pool de disques ne répond plus au fournisseur de ressources. Confirmez que vous répondez aux [conditions préalables de mise en réseau](disks-pools-deploy.md#prerequisites), puis recommencez le déploiement. Si le problème persiste, contactez le support Azure et fournissez l’ID de suivi de l’erreur.         |
|OngoingOperationInProgress     |Une opération est en cours sur le pool de disques. Attendez la fin de l’opération, puis réessayez de relancer le déploiement.         |

## <a name="common-failure-codes-when-enabling-iscsi-on-disk-pools"></a>Codes d’erreur courants lors de l’activation d’iSCSI sur les pools de disques

|Code  |Description  |
|---------|---------|
|GoalStateApplicationError     |Se produit lorsque la configuration de la cible iSCSI n’est pas valide et ne peut pas être appliquée au pool de disques. Recommencez le déploiement. Si le problème persiste, contactez le support Azure et fournissez l’ID de suivi de l’erreur.         |
|GoalStateApplicationTimeoutError     |Se produit lorsque l’infrastructure du pool de disques ne répond plus au fournisseur de ressources. Confirmez que vous répondez aux [conditions préalables de mise en réseau](disks-pools-deploy.md#prerequisites), puis recommencez le déploiement. Si le problème persiste, contactez le support Azure et fournissez l’ID de suivi de l’erreur.         |
|OngoingOperationInProgress     |Une opération est en cours sur le pool de disques. Attendez la fin de l’opération, puis réessayez de relancer le déploiement.         |

## <a name="next-steps"></a>Étapes suivantes

[Gérer un pool de disques (préversion)](disks-pools-manage.md)
