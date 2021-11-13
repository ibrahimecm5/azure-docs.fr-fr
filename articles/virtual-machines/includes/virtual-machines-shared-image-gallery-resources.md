---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/13/2021
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ae085270a28bfd27542585d5df6c1dd918943d9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421630"
---
| Ressource | Description|
|----------|------------|
| **Source d’image** | Cette ressource peut être utilisée pour créer une **version d’image** dans une galerie. Une source d’image peut être une machine virtuelle Azure existante qui est [généralisée ou spécialisée](../shared-image-galleries.md#generalized-and-specialized-images), une image managée, un instantané ou une version d’image dans une autre galerie. |
| **Galerie** | Tout comme la Place de marché Azure, une **galerie** est un dépôt permettant de gérer et partager des images et des [applications VM](../vm-applications.md), mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les définitions d’image sont créées dans une galerie et contiennent des informations sur l’image et sur les exigences relatives à son utilisation en interne. Ces informations indiquent, par exemple, si l’image est Windows ou Linux, et comprennent les notes de publication et les exigences de mémoire maximale et minimale. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |