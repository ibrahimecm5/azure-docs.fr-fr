---
title: Limites du service
titleSuffix: Azure Digital Twins
description: Graphique présentant les limites du service Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 09/02/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: b6bb1bb2626697c97b5603db9f07a08f8cc1e980
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132034"
---
# <a name="azure-digital-twins-service-limits"></a>Limites du service Azure Digital Twins

Les sections suivantes décrivent les limites du service Azure Digital Twins.

> [!NOTE]
> Certaines zones de ce service ont des limites ajustables. Cela est représenté dans les tableaux ci-dessous avec la colonne *Ajustable ?* . Quand la limite peut être réglée, la valeur *Ajustable ?* est *Oui*.
>
> Si votre entreprise nécessite de déclencher une limite ou un quota ajustable au-delà de la limite par défaut, vous pouvez demander des ressources supplémentaires en [ouvrant un ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Limites par type

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Gestion des limites

Quand une limite est atteinte, toute requête au-delà de cette valeur est bloquée par le service, ce qui génère une réponse d’erreur 429 par cette requête.

Pour gérer l’étranglement, voici quelques recommandations relatives aux limites.
* **Utilisez la logique de nouvelle tentative.** Les [kits SDK Azure Digital Twins](concepts-apis-sdks.md) implémentent la logique de nouvelle tentative pour les requêtes ayant échoué. Par conséquent, si vous travaillez avec un SDK fourni, cette fonctionnalité est déjà intégrée. Sinon, envisagez d’implémenter une logique de nouvelle tentative dans votre propre application. Le service renvoie un en-tête `Retry-After` dans la réponse d’échec, que vous pouvez utiliser pour déterminer la durée d’attente avant une nouvelle tentative.
* **Utilisez des seuils et des notifications pour signaler les limites.** Certaines des limites de service pour Azure Digital Twins ont des [métriques](troubleshoot-metrics.md) correspondantes qui peuvent être utilisées pour suivre l’utilisation dans ces domaines. Pour configurer des seuils et une alerte sur n’importe quelle métrique lorsqu’un seuil est proche, consultez les instructions dans [Résolution des problèmes : alertes](troubleshoot-alerts.md). Pour configurer des notifications pour d’autres limites où les métriques ne sont pas fournies, envisagez d’implémenter cette logique dans votre propre code d’application.
* **Déployez à grande échelle sur plusieurs instances.** Évitez d’avoir recours à un point de défaillance unique. Au lieu d’un seul grand graphique pour l’ensemble de votre déploiement, envisagez de répartir des sous-ensembles de jumeaux de manière logique (p. ex. : par région ou locataire) sur plusieurs instances. 

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la version actuelle d’Azure Digital Twins dans la vue d’ensemble du service :
* [Qu’est-ce qu’Azure Digital Twins ?](overview.md)
