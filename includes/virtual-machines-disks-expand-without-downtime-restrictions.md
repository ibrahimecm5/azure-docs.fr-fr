---
title: Fichier include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5813ec99666e7e758dd1d58a639211d3d0ca0c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448940"
---
- Disponible uniquement dans la région USA Centre-Ouest.
- Prise en charge uniquement pour les disques de données.
- Les disques de moins de 4 Tio ne peuvent pas être étendus à 4 Tio ou plus sans temps d’arrêt.
    - Si vous augmentez la taille d’un disque à 4 Tio ou plus, il peut être étendu sans temps d’arrêt.
- Installer et utiliser soit :
    - La [dernière version d’Azure CLI](/cli/azure/install-azure-cli)
    - Le [dernier module Azure PowerShell](/powershell/azure/install-az-ps)
    - Le Portail Azure en cas d’accès via [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)
    - Ou un modèle Azure Resource Manager avec une version d’API 2021-04-01 ou plus récente.