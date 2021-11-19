---
title: Fonctionnalité de mise à l’échelle automatique pour le service FHIR des API Azure Healthcare
description: Cet article décrit la fonctionnalité de mise à l’échelle automatique pour le service FHIR des API de santé Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: zxue
ms.openlocfilehash: 382a3ccee6f14c4d95e74bb40a8d899868754926
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814637"
---
# <a name="fhir-service-autoscale"></a>Mise à l’échelle automatique du service FHIR

Le service FHIR dans les API de santé Azure est un service géré qui permet aux clients de conserver des données de santé conformes à FHIR et d’interagir avec eux en toute sécurité via le point de terminaison du service API. Le service FHIR fournit la capacité de mise à l’échelle automatique intégrée pour répondre à différentes charges de travail.  

## <a name="what-is-fhir-service-autoscale"></a>Qu’est-ce que la mise à l’échelle automatique du service FHIR ?   

La fonctionnalité de mise à l’échelle automatique pour le service FHIR est conçue pour fournir automatiquement une évolutivité de service optimisée pour répondre aux demandes des clients lorsqu’ils effectuent des transactions de données dans des charges de travail cohérentes ou différentes à tout moment. Elle est disponible dans toutes les régions où le service FHIR est pris en charge. Gardez à l’esprit que la fonctionnalité de mise à l’échelle automatique est soumise aux ressources disponibles dans les régions Azure.   

## <a name="how-does-fhir-service-autoscale-work"></a>Comment fonctionne la mise à l’échelle automatique du service FHIR ?  

Lorsque les charges de travail des transactions sont élevées, la fonctionnalité de mise à l’échelle automatique augmente automatiquement les ressources de calcul. Lorsque les charges de travail des transactions sont faibles, les ressources informatiques sont réduites en conséquence.  

La fonctionnalité de mise à l’échelle automatique ajuste automatiquement les ressources de calcul pour optimiser l’évolutivité globale du service. Que vous effectuiez des requêtes de lecture qui incluent des requêtes simples comme l’obtention d’informations sur les patients à l’aide d’un ID de patient et des requêtes avancées comme l’obtention de toutes les `DiagnosticReport` ressources pour les patients dont le nom est « Sarah », ou que vous créez ou mettez à jour des ressources FHIR, la fonctionnalité de mise à l’échelle automatique gère la dynamique et la complexité de l’allocation

La fonctionnalité de mise à l’échelle automatique fait partie du service géré et ne nécessite aucune action de la part des clients. Toutefois, les clients sont encouragés à partager leurs commentaires afin d’améliorer la fonctionnalité. Les clients peuvent également générer un ticket de support pour résoudre les problèmes d’évolutivité qu’ils ont pu rencontrer.  

### <a name="what-is-the-cost-of-the-fhir-service-autoscale"></a>Quel est le coût de la mise à l’échelle automatique du service FHIR ?  

La fonctionnalité de mise à l’échelle automatique n’entraîne aucun coût supplémentaire pour les clients en fonction du nouveau modèle de facturation d’API.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris la fonctionnalité de mise à l’échelle automatique du service FHIR dans les API de santé Azure. pour plus d’informations sur les fonctionnalités prises en charge par le service FHIR, consultez

>[!div class="nextstepaction"]
>[Fonctionnalités prises en charge de FHIR](fhir-features-supported.md)
