---
title: Limites d’Azure Data Box Heavy | Microsoft Docs
description: Décrit les limites du système et les tailles recommandées pour les connexions et les composants de Microsoft Azure Data Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 06/28/2021
ms.author: alkohli
ms.openlocfilehash: fedd7417f6f199289bfd5c6e11a1cf1bd8c98a82
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038351"
---
# <a name="azure-data-box-heavy-limits"></a>Limites d’Azure Data Box Heavy

Tenez compte de ces limites quand vous déployez et utilisez votre appareil Azure Data Box Heavy. Le tableau suivant décrit ces limites pour Data Box.


## <a name="data-box-heavy-service-limits"></a>Limites du service Azure Data Box Heavy

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-heavy-limits"></a>Limites de Data Box Heavy

- Le service Data Box Heavy peut stocker jusqu’à 1 milliard de fichiers par nœud.
- Data Box Heavy prend en charge un maximum de 512 conteneurs ou partages par nœud dans le cloud. Les répertoires de niveau supérieur au sein du partage utilisateur deviennent des conteneurs ou des partages de fichiers Azure dans le cloud. 

## <a name="azure-storage-limits"></a>Limites de Stockage Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Mises en garde relatives au chargement de données

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="azure-storage-account-size-limits"></a>Limites de la taille du compte de stockage Azure

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Limites de taille des objets Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Conventions de nommage des objets blob de blocs,des objets blob de pages et des fichiers Azure

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
