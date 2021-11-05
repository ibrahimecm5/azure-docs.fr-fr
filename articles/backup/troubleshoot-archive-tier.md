---
title: Résolution des problèmes du niveau archive
description: Découvrez comment résoudre les erreurs du niveau archive pour la Sauvegarde Azure.
ms.topic: troubleshooting
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 560c8900e5d0c06dc436ec1222583db58f7cdda3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096514"
---
# <a name="troubleshooting-recovery-point-archive-using-archive-tier"></a>Résolution des problèmes d’archive des points de récupération à l’aide du niveau archive

Cet article fournit des détails pour résoudre les problèmes liés aux codes d’erreur qui s’affichent quand un point de récupération ne peut pas être déplacé vers l’archive.

## <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Message d’erreur** : Le type de point de récupération ne peut pas être déplacé vers l’archive

**Description** : ce code d’erreur s’affiche quand le type de point de récupération sélectionné ne peut pas être déplacé vers l’archive.

**Action recommandée** : vérifiez l’éligibilité du point de récupération. Consultez [Charges de travail prises en charge](archive-tier-support.md#supported-workloads).

## <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Message d’erreur** : Un point de récupération avec des dépendances actives pour la restauration ne peut pas être déplacé vers l’archive

**Description** : le point de récupération sélectionné a des dépendances actives et ne peut donc pas être déplacé vers l’archive.

**Action recommandée** : vérifiez l’éligibilité du point de récupération. Consultez [Charges de travail prises en charge](archive-tier-support.md#supported-workloads).

## <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers l’archive, car la durée de vie passée dans le niveau standard du coffre est inférieure au minimum nécessaire

**Description** : le point de récupération doit rester au niveau standard pendant au moins trois mois pour les machines virtuelles Azure et 45 jours pour SQL Server sur des machines virtuelles Azure

**Action recommandée** : vérifiez l’éligibilité du point de récupération. Consultez [Charges de travail prises en charge](archive-tier-support.md#supported-workloads).

## <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Message d’erreur** : La durée de vie restante du point de récupération est inférieure au minimum nécessaire.

**Description** : la durée de vie minimale nécessaire pour qu’un point de récupération puisse être déplacé vers l’archive est de six mois.

**Action recommandée** : vérifiez l’éligibilité du point de récupération. Consultez [Charges de travail prises en charge](archive-tier-support.md#supported-workloads).

## <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers l’archive, car il est déjà dans le niveau archive

**Description** : le point de récupération sélectionné figure déjà dans l’archive. Il n’est donc pas possible de le déplacer vers l’archive.

## <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Message d’erreur** : Le type de source de données n’est pas éligible pour l’API Recommandation.

**Description**: l’API Recommandation s’applique uniquement aux machines virtuelles Azure. Elle ne s’applique pas au type de source de donnée sélectionné.

## <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Message d’erreur** : Le point de récupération est déjà réhydraté. La réalimentation n’est pas autorisée sur ce point de récupération.

**Description**: le point de récupération sélectionné est déjà réhydraté.

## <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers l’archive.

**Description** : le point de récupération sélectionné ne peut pas être déplacé vers l’archive.

## <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Message d’erreur** : Le point de récupération d’archive n’est pas réhydraté. Réessayez la restauration une fois la réalimentation terminée sur le point de récupération Archive.

**Description** : le point de récupération n’est pas réhydraté. Essayez de restaurer après avoir réalimenté le point de récupération.

## <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Message d’erreur** : La réhydratation est prise en charge seulement pour les points de récupération d’archive

**Description** : la réhydratation n’est pas autorisée pour le point de récupération sélectionné.

## <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Message d’erreur** : La réhydratation est déjà en cours pour le point de récupération d’archive.

**Description**: la réhydratation du point de récupération sélectionné est déjà en cours.

## <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Message d’erreur** : Impossible de déplacer le point de récupération vers le niveau Archive en raison d’une durée de conservation insuffisante spécifiée dans la stratégie.

**Action recommandée** : mettez à jour la stratégie sur l’élément protégé avec le paramètre de conservation approprié et réessayez.

## <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Message d’erreur** : Nous essayons encore de déterminer si ce point de récupération peut être déplacé.

**Description** : l’état de préparation au déplacement du point de récupération n’est pas encore déterminé.

**Action recommandée** : revérifiez dans un moment.
