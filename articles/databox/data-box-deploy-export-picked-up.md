---
title: Tutoriel sur le renvoi d’Azure Data Box dans une commande d’exportation | Microsoft Docs
description: Découvrez comment expédier votre Azure Data Box à Microsoft une fois la commande d’exportation terminée
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.openlocfilehash: 544454e4022da67db50fb194d7aea72a6bd716bd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449966"
---
# <a name="tutorial-return-azure-data-box"></a>Tutoriel : Retourner un appareil Azure Data Box

Ce tutoriel explique comment retourner Azure Data Box et comment les données sont effacées une fois que l’appareil est réceptionné au centre de données Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
>
> * Prérequis
> * Préparer l’expédition
> * Expédier la Data Box à Microsoft
> * Effacer les données de la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

* Vous avez suivi le [Tutoriel : Copier des données à partir d’Azure Data Box](data-box-deploy-export-copy-data.md).
* Les tâches de copie sont terminées. La préparation de l’expédition ne peut pas s’effectuer si les tâches de copie sont en cours d’exécution.

## <a name="prepare-to-ship"></a>Préparer l’expédition

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil.

## <a name="ship-data-box-back"></a>Renvoyer la Data Box

Vérifiez que la copie des données à partir de l’appareil a été effectuée et que l’exécution de la **Préparation de l’expédition** a réussi.

La procédure varie en fonction de la région où vous expédiez l’appareil. Dans de nombreux pays ou régions, vous pouvez utiliser l’expédition managée par Microsoft ou l’expédition automanagée.

### <a name="microsoft-managed-shipping"></a>Expédition gérée par Microsoft

Suivez les instructions applicables à la région à partir de laquelle vous effectuez l’expédition si celle-ci est gérée par Microsoft.

## <a name="us--canada"></a>[États-Unis et Canada](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-eu)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**Si vous réexpédiez vers des centres de données situés en Allemagne ou en Suisse,** vous pouvez également [utiliser l’expédition autogérée](#self-managed-shipping).

## <a name="uk"></a>[Royaume-Uni](#tab/in-uk)

[!INCLUDE [data-box-shipping-in-uk](../../includes/data-box-shipping-in-uk.md)]

## <a name="australia"></a>[Australie](#tab/in-australia)

[!INCLUDE [data-box-shipping-in-australia](../../includes/data-box-shipping-in-australia.md)]

## <a name="japan"></a>[Japon](#tab/in-japan)

[!INCLUDE [data-box-shipping-in-japan](../../includes/data-box-shipping-in-japan.md)]

## <a name="singapore"></a>[Singapour](#tab/in-singapore)

[!INCLUDE [data-box-shipping-in-singapore](../../includes/data-box-shipping-in-singapore.md)]

## <a name="hong-kong"></a>[Hong Kong (R.A.S.)](#tab/in-hk)

[!INCLUDE [data-box-shipping-in-hk](../../includes/data-box-shipping-in-hk.md)]

## <a name="korea"></a>[Corée](#tab/in-korea)

[!INCLUDE [data-box-shipping-in-korea](../../includes/data-box-shipping-in-korea.md)]

## <a name="s-africa"></a>[Afrique du Sud](#tab/in-sa)

[!INCLUDE [data-box-shipping-in-sa](../../includes/data-box-shipping-in-sa.md)]

## <a name="uae"></a>[Émirats Arabes Unis](#tab/in-uae)

[!INCLUDE [data-box-shipping-in-uae](../../includes/data-box-shipping-in-uae.md)]

### <a name="self-managed-shipping"></a>Expédition autogérée

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

---

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box

Une fois que l’appareil a atteint le centre de données Azure, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant les sujets suivants :

> [!div class="checklist"]
> * Prérequis
> * Préparer l’expédition
> * Expédier la Data Box à Microsoft
> * Effacer les données de la Data Box

Passez à l’article suivant pour découvrir comment gérer votre Data Box.

> [!div class="nextstepaction"]
> [Gérer Data Box par le biais du portail Azure](./data-box-portal-admin.md)
