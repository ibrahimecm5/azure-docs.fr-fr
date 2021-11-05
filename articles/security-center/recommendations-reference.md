---
title: Tableau de référence pour toutes les recommandations de Microsoft Defender pour le cloud
description: Cet article liste les recommandations de sécurité de Defender pour le cloud qui vous permettent de durcir la sécurité et de protéger vos ressources.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 09/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 34f970884c97105a3831734cf672376e17f43a98
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014464"
---
# <a name="security-recommendations---a-reference-guide"></a>Recommandations de sécurité - Guide de référence

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article liste les recommandations que vous pouvez voir dans Microsoft Defender pour le cloud. Les recommandations présentées dans votre environnement dépendent des ressources que vous protégez et de votre configuration personnalisée.

Les recommandations de Defender pour le cloud sont basées sur le [Benchmark de sécurité Azure](../security/benchmarks/introduction.md). Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. Ce benchmark, largement respecté et centré sur le cloud, est basé sur les contrôles du [CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) et du [NIST (National Institute of Standards and Technology)](https://www.nist.gov/).

Pour en savoir plus sur la façon de répondre à ces recommandations, consultez [Appliquer les recommandations de Microsoft Defender pour le cloud](implement-security-recommendations.md).

Votre degré de sécurisation est basé sur le nombre de recommandations Defender pour le cloud que vous avez appliquées. Pour déterminer quelles recommandations doivent être suivies en premier, examinez la gravité de chacune d’entre elles et son impact potentiel sur votre degré de sécurisation.

> [!TIP]
> Si la description d’une recommandation indique « aucune stratégie associée », cela est généralement dû au fait que cette recommandation est dépendante d’une autre recommandation et de _sa_ stratégie. Par exemple, la recommandation « Les échecs d’intégrité d’Endpoint Protection doivent être corrigés... », s’appuie sur la recommandation qui vérifie si une solution Endpoint Protection est _installée_ en premier lieu (« La solution Endpoint Protection doit être installée... »). La recommandation sous-jacente _est associée_ à une stratégie.
> Limiter les stratégies à la recommandation de base simplifie la gestion des stratégies.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Recommandations AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Recommandations relatives au calcul

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Recommandations relatives aux conteneurs

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Recommandations relatives aux données

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Recommandations relatives à IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Suggestions IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Recommandations pour la mise en réseau

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Recommandations dépréciées

|Recommandation|Description et stratégie associée|severity|
|----|----|----|
|L'accès à App Services doit être limité|Limitez l'accès à App Services en modifiant la configuration du réseau pour empêcher le trafic entrant provenant de plages trop larges.<br>(Stratégie associée : [Préversion] : L’accès à App Services doit être limité)|Élevé|
|Les règles relatives aux applications web doivent être renforcées sur les groupes de sécurité réseau IaaS|Renforcez le groupe de sécurité réseau (NSG) des machines virtuelles qui exécutent des applications web lorsque les règles NSG sont trop permissives en ce qui concerne les ports des applications web.<br>(Stratégie associée : Les règles de groupe de sécurité réseau pour les applications web IaaS doivent être renforcées)|Élevé|
|Des stratégies de sécurité de pod doivent être définies pour réduire le vecteur d’attaque en supprimant les privilèges d’application inutiles (préversion)|Définissez des stratégies de sécurité de pod pour réduire le vecteur d’attaque en supprimant les privilèges d’application inutiles. Il est recommandé de configurer des stratégies de sécurité de pod afin que les pods ne puissent accéder qu’aux ressources auxquelles ils sont autorisés à accéder.<br>(Stratégie associée : [Préversion] : Des stratégies de sécurité de pods doivent être définies sur les services Kubernetes)|Moyenne|
|Installer Azure Security Center pour le module de sécurité IoT afin d’obtenir une meilleure visibilité sur vos appareils IoT|Installez Azure Security Center pour le module de sécurité IoT afin d’obtenir une meilleure visibilité sur vos appareils IoT.|Faible|
|Vos machines doivent être redémarrées pour appliquer les mises à jour système|Redémarrez vos machines pour appliquer les mises à jour système et protéger la machine contre les vulnérabilités. (Stratégie associée : Les mises à jour système doivent être installées sur vos machines)|Moyenne|
| L’agent d’analyse doit être installé sur vos machines|Cette action installe un agent de supervision sur les machines virtuelles sélectionnées. Sélectionnez un espace de travail vers lequel l’agent doit envoyer ses rapports. (Aucune stratégie associée)|Élevé|
||||

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations, consultez les articles suivants :

- [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).
- [Consulter vos recommandations de sécurité](review-security-recommendations.md)
