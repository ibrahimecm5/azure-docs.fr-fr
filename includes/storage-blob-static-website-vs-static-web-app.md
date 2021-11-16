---
title: Fichier include
description: Fichier include
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 11/04/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 74ee76424ccbbdcaaebb4652acbe42bf569ac86d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096950"
---
Les sites web statiques présentent des limitations. Par exemple, si vous souhaitez configurer des en-têtes, vous devez utiliser Azure CDN (Content Delivery Network). La fonctionnalité de site web statique n’offre aucun moyen de configurer des en-têtes. Par ailleurs, AuthN et AuthZ ne sont pas pris en charge. 

Si ces fonctionnalités sont importantes pour votre scénario, envisagez d’utiliser [Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/). Ce service constitue une excellente alternative aux sites web statiques. Il convient également quand vous n’avez pas besoin d’un serveur web pour afficher du contenu. Vous pouvez configurer des en-têtes, et AuthN et AuthZ sont entièrement pris en charge. Azure Static Web Apps offre également un workflow CI/CD (intégration continue et livraison continue) complètement managé, de la source GitHub au déploiement global.
