---
title: Enregistrer une vidéo pour la lire avec Azure Video Analyzer
description: Cet article décrit l’enregistrement d’une vidéo à lire avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 253db9fe0ad067f00647cb195995eaee4951a38a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563945"
---
# <a name="record-video-for-playback"></a>Enregistrer une vidéo à lire

Dans le contexte d’un système de gestion vidéo à base de caméras de surveillance, l’enregistrement vidéo fait référence au processus qui consiste à capturer et enregistrer les données vidéo des caméras pour les afficher via des applications mobiles ou de navigateur. L’enregistrement vidéo peut être classé dans la catégorie de l’enregistrement vidéo en continu et l’enregistrement vidéo basé sur des événements.

## <a name="continuous-video-recording"></a>Enregistrement de vidéo continu

L’enregistrement vidéo continu (CVR) fait référence au processus d’enregistrement vidéo en continu à partir d’une source vidéo. L’enregistrement vidéo continu (CVR) garantit que la durée souhaitée de la vidéo est disponible (conformément à la **[stratégie de conservation](#retention-policy))** à des fins d’analyse et/ou d’audit.

## <a name="event-based-video-recording"></a>Enregistrement de vidéo basé sur les événements

Un enregistrement vidéo basé sur les événements est un enregistrement vidéo qui a été déclenché par un événement. L’événement en question peut provenir du traitement du signal vidéo lui-même (par exemple la détection de mouvement), ou d’une source indépendante (par exemple un capteur d’ouverture de porte). L’enregistrement vidéo basé sur les événements peut permettre d’effectuer des économies de stockage. Toutefois, il nécessite des composants supplémentaires pour générer les événements et déclencher l’enregistrement vidéo. En d’autres termes, l’enregistrement vidéo basé sur les événements (EVR) nécessite des décisions supplémentaires concernant les événements qui doivent déclencher l’enregistrement vidéo et la durée des enregistrements. Voici un exemple de stratégie : enregistrer la vidéo pendant 2 minutes, en commençant 30 secondes avant le début de l’événement.

## <a name="choosing-recording-modes"></a>Choix des modes d’enregistrement

Le choix d’utiliser les solutions CVR ou EVR dépend des objectifs métier. Azure Video Analyzer (AVA) fournit des fonctionnalités de plateforme pour l’enregistrement vidéo continu (CVR) et l’enregistrement vidéo basé sur les événements (EVR). Pour en savoir plus sur le sujet, consultez les articles relatifs aux scénarios d’enregistrement **[CVR](continuous-video-recording.md)** et **[EVR](event-based-video-recording-concept.md)** .

## <a name="retention-policy"></a>Stratégie de rétention

La stratégie de conservation fait référence aux stratégies qui dictent la longueur ou la durée de l’enregistrement vidéo conservé. Les stratégies de conservation vous permettent d’équilibrer les coûts de stockage avec les besoins métier. Dans le cadre d’un scénario CVR, la stratégie de conservation définit le nombre de jours pendant lesquels la vidéo doit être stockée (par exemple, les 7 derniers jours). Pour plus d’informations à ce sujet, consultez l’article **[Gérer la stratégie de conservation](manage-retention-policy.md)** .

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir les scénarios d’enregistrement EVR](event-based-video-recording-concept.md)
- [Découvrir les scénarios d’enregistrement CVR](continuous-video-recording.md)
