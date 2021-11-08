---
title: Tutoriel sur le renvoi d’Azure Data Box | Microsoft Docs
description: Dans ce tutoriel, découvrez comment retourner l’appareil Azure Data Box, notamment préparer l’expédition de Data Box, vérifier le chargement de données et à effacer les données de Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 95cce7e79ea8eaf3a542b52ac5c04b954ed56e0e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469100"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutoriel : Retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Retourner l’appareil Data Box et vérifier le chargement des données dans Azure

::: zone-end

::: zone target="docs"

Ce didacticiel vous explique comment retourner l’appareil Azure Data Box et vérifier le chargement des données dans Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
>
> * Prérequis
> * Préparer l’expédition
> * Expédier la Data Box à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données de la Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

* Vous avez suivi le [Tutoriel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-deploy-copy-data.md).
* Les travaux de copie sont terminés et il n’y a pas d’erreurs dans la page **Connexion et copie**. La **préparation de l’expédition** ne peut pas s’exécuter si des travaux de copie sont en cours ou s’il y a des erreurs dans la page **Connexion et copie**.

## <a name="prepare-to-ship"></a>Préparer l’expédition

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Une fois la copie des données terminée, vous pouvez préparer et expédier l’appareil. Quand l’appareil arrive au centre de données Azure, les données sont automatiquement chargées vers Azure.

## <a name="prepare-to-ship"></a>Préparer l’expédition

Avant de préparer l’expédition, assurez-vous que les travaux de copie sont terminés.

1. Accédez à la page **Préparation de l'expédition** dans l'interface utilisateur web locale et commencez la préparation de l'expédition.
2. Mettez l’appareil hors tension à partir de l’interface utilisateur web locale. Débranchez les câbles de l’appareil.

Les étapes suivantes sont déterminées par l’endroit où vous retournez l’appareil.

::: zone-end


## <a name="ship-data-box-back"></a>Renvoyer la Data Box

Vérifiez que la copie des données sur l’appareil a été effectuée et que l’exécution de la **préparation de l’expédition** a réussi. 

En fonction de la région où vous expédiez l’appareil, la procédure est différente. Dans de nombreux pays ou régions, vous pouvez utiliser l’expédition managée par Microsoft ou l’expédition automanagée.

### <a name="microsoft-managed-shipping"></a>Expédition gérée par Microsoft

Suivez les instructions applicables à la région à partir de laquelle vous effectuez l’expédition si celle-ci est gérée par Microsoft.

## <a name="us--canada"></a>[États-Unis et Canada](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-europe)

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Effacer les données de la Data Box
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end


::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

