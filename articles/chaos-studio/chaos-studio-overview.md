---
title: Qu’est-ce qu’Azure Chaos Studio ?
description: Découvrez Azure Chaos Studio, un service Azure qui vous permet de mesurer, de comprendre et de créer une résilience d’application et de service pour les incidents réels à l’aide de l’ingénierie du chaos pour injecter des erreurs sur votre service, puis surveiller la façon dont le service répond aux interruptions.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-overview,ignite-fall-2021
ms.openlocfilehash: 4df530e20733e5f24db930e88bb30894293416a7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399330"
---
# <a name="what-is-azure-chaos-studio-preview"></a>Qu’est-ce qu’Azure Chaos Studio (préversion) ?

Azure Chaos Studio est un service géré pour améliorer la résilience en injectant des erreurs dans vos applications Azure. L’exécution d’expériences d’injection d’erreurs contrôlées contre vos applications, une pratique connue sous le nom d’ingénierie du chaos, vous aide à mesurer, comprendre et améliorer la résilience par rapport aux incidents réels, tels qu’une panne de région ou des défaillances d’application provoquant une utilisation élevée du processeur sur une machine virtuelle.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Videos/Azure-Chaos-Studio-Curious-to-control-chaos/player]

## <a name="why-should-i-use-chaos-studio"></a>Pourquoi utiliser Chaos Studio ?

Que vous développiez une nouvelle application qui sera hébergée sur Azure, que vous migriez une application existante vers Azure ou que vous utilisez une application qui s’exécute déjà sur Azure, il est important de valider et d’améliorer la résilience de votre application. La résilience est la capacité d’un système à gérer et à récupérer des interruptions. Les interruptions de la disponibilité de votre application peuvent entraîner des erreurs et des échecs pour les utilisateurs, qui peuvent à leur tour avoir des conséquences négatives sur votre entreprise ou votre mission.

Lors de l’exécution d’une application dans le cloud, éviter ces conséquences négatives vous oblige à vérifier que votre application répond efficacement à des perturbations qui peuvent être causées par un service dont vous dépendez, des interruptions causées par une défaillance du service lui-même ou même des perturbations des outils et des processus de réponse aux incidents. L’expérimentation du chaos vous permet de vérifier que votre application hébergée dans le cloud résiste aux défaillances.

## <a name="when-would-i-use-chaos-studio"></a>Quand utiliser Chaos Studio ?

L’ingénierie du chaos peut être utilisée pour un large éventail de scénarios de validation de résilience. Ces scénarios couvrent l’ensemble du cycle de vie du développement et des opérations de service et peuvent être catégorisés comme un *décalage vers la droite*, où le scénario est mieux validé dans un environnement de production ou de pré-production, ou un *décalage vers la gauche*,où le scénario peut être validé dans un environnement de développement ou un environnement de test partagé. En général, les scénarios de décalage vers la droite doivent être réalisés avec un trafic client réel ou une charge simulée, tandis que les scénarios de décalage vers la gauche peuvent être effectués sans trafic client réel. Voici quelques scénarios courants dans lesquels l’ingénierie de chaos peut être appliquée :
* Reproduction d’un incident qui a affecté votre application pour mieux comprendre le mode d’échec ou vérification que les éléments de réparation après incident empêchent l’incident de se reproduire.
* Exécution des « journées du jeu » : chargement, mise à l’échelle, performances et validation de résilience d’un service en vue d’un événement ou d’une saison utilisateur importants.
* Exécution d’exercices de continuité d’activité/récupération d’urgence (BCDR) pour vous assurer que si votre application a été affectée par un sinistre majeur, elle peut être récupérée rapidement et les données critiques sont conservées.
* Exécution d’exercices haute disponibilité pour tester la résilience des applications contre les défaillances spécifiques telles que les interruptions de la région, les erreurs de configuration du réseau, les événements à forte sollicitation ou les problèmes de voisins bruyants.
* Développement des points de référence des performances d’application.
* Planification des besoins en capacité pour les environnements de production.
* Exécution de tests de contrainte ou de tests de charge.
* Le fait de garantir la migration des services à partir d’un environnement local ou d’un autre environnement cloud reste résilient aux défaillances connues.
* Renforcement de la confiance dans les services basés sur les architectures natives cloud.
* Validation de l’utilisation des outils de site dynamiques, des données d’observation et des processus d’appel, comme prévu dans des conditions inattendues.

Pour la plupart de ces scénarios, vous créez d’abord la résilience à l’aide d’expériences de chaos ad hoc, puis vous validez en permanence que les nouveaux déploiements ne régressent pas la résilience en utilisant les expériences de chaos comme porte de déploiement dans votre pipeline CI/CD.

## <a name="how-does-chaos-studio-work"></a>Comment Chaos Studio fonctionne-t-il ?

Chaos Studio vous permet d’orchestrer l’injection d’erreurs sur vos ressources Azure de façon sécurisée et contrôlée. Les expériences de chaos sont au cœur de Chaos Studio. Une expérience de chaos est une ressource Azure qui décrit les erreurs qui doivent être exécutées et les ressources sur lesquelles ces erreurs doivent être exécutées. Les erreurs peuvent être organisées en parallèle ou séquentiellement, en fonction de vos besoins. Chaos Studio prend en charge deux types d’erreurs : les défaillances *directes de service* qui s’exécutent directement sur une ressource Azure sans aucune installation ni instrumentation (par exemple, le redémarrage d’un cache Azure pour les clusters Redis ou l’ajout de latence réseau aux pods AKS) et les erreurs *basées sur l’agent*, qui s’exécutent sur des machines virtuelles ou des groupes de machines virtuelles identiques pour exécuter des défaillances internes (par exemple, la sollicitation de la mémoire virtuelle ou élimination d’un processus). Chaque erreur contient des paramètres spécifiques que vous pouvez contrôler, par exemple, le processus à supprimer, ou la sollicitation de mémoire à générer.

Quand vous créez une expérience de chaos, vous définissez une ou plusieurs *étapes* qui s’exécutent de manière séquentielle, chaque étape contenant une ou plusieurs *branches* qui s’exécutent en parallèle au cours de l’étape, et chaque branche contenant une ou plusieurs *actions*, telles que l’injection d’une erreur ou l’attente d’une certaine durée. Enfin, vous organisez les ressources (*cibles*) sur lesquelles chaque erreur sera exécutée dans des groupes appelés sélecteurs, afin de pouvoir référencer facilement un groupe de ressources dans chaque action.

![Diagramme montrant la disposition d’une expérience de chaos.](images/chaos-experiment.png)

Une expérience de chaos est une ressource Azure qui réside dans un abonnement et un groupe de ressources. Vous pouvez utiliser le Portail Azure ou l’API REST de Chaos Studio pour créer, mettre à jour, démarrer, annuler et afficher l’état d’une expérience.

## <a name="next-steps"></a>Étapes suivantes
Commencez à créer et à exécuter des expériences de chaos pour améliorer la résilience des applications avec Chaos Studio à l’aide des liens ci-dessous.
- [Créer et exécuter votre première expérience](chaos-studio-tutorial-service-direct-portal.md)
- [En savoir plus sur l’ingénierie du chaos](chaos-studio-chaos-engineering-overview.md)
