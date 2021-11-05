---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c6f83c1590057b8b46b19f3100947bd838666792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022294"
---
- Les instantanés incrémentiels ne peuvent actuellement pas être transférés d'un abonnement à un autre.
- À un moment donné, vous ne pouvez actuellement générer que des URI SAS comprenant jusqu'à cinq instantanés d'une famille d'instantanés particulière.
- Vous ne pouvez pas créer d'instantané incrémentiel pour un disque particulier en dehors de l'abonnement associé à ce disque.
- Vous pouvez créer jusqu'à sept instantanés incrémentiels par disque toutes les cinq minutes.
- Au total, 200 instantanés incrémentiels peuvent être créés pour un même disque.
- Vous ne pouvez pas obtenir les modifications entre les captures instantanées prises avant et après le changement de la taille du disque parent au-delà de la limite de 4 To. Par exemple, vous avez pris une capture instantanée incrémentielle `snapshot-a` lorsque la taille d’un disque était de 2 To. Vous avez maintenant augmenté la taille du disque à 6 To, puis vous avez pris une autre capture instantanée incrémentielle `snapshot-b`. Vous ne pouvez pas récupérer les modifications entre `snapshot-a` et `snapshot-b`. Vous devez télécharger à nouveau la copie complète de `snapshot-b` créé après le redimensionnement. Par la suite, vous pourrez obtenir les modifications entre `snapshot-b` et les captures instantanées créées après `snapshot-b`.
