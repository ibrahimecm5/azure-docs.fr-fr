---
title: Stratégies de déploiement bleu-vert dans Azure Spring Cloud
description: Cette rubrique décrit deux approches de déploiements bleu-vert dans Azure Spring Cloud.
author: barbkess
ms.author: barbkess
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 194529a03a493ec22d22260576a678ab07401f4c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056565"
---
# <a name="blue-green-deployment-strategies-in-azure-spring-cloud"></a>Stratégies de déploiement bleu-vert dans Azure Spring Cloud

Cet article décrit la prise en charge de déploiement bleu-vert dans Azure Spring Cloud.

Azure Spring Cloud (niveau Standard et supérieur) autorise deux déploiements pour chaque application, un seul reçoit le trafic de production. Ce modèle est connu sous le nom de déploiement bleu-vert. La prise en charge par Azure Spring Cloud du déploiement bleu-vert, avec un pipeline de [livraison continue (CD)](/devops/deliver/what-is-continuous-delivery) et des tests automatisés rigoureux, permet des déploiements d’applications agiles avec un niveau de confiance élevé.

## <a name="alternating-deployments"></a>Déploiements alternés

Le moyen le plus simple d’implémenter un déploiement bleu-vert avec Azure Spring Cloud consiste à créer deux déploiements fixes et à toujours déployer sur le déploiement qui ne reçoit pas de trafic de production. Avec la tâche [Azure Spring Cloud pour Azure Pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud), vous pouvez déployer de cette façon, juste en affectant à l’indicateur `UseStagingDeployment` la valeur `true`.

Voici comment fonctionne l’approche des déploiements alternés dans la pratique :

Supposons que votre application a deux déploiements : `deployment1` et `deployment2`. Actuellement, `deployment1` est défini en tant que déploiement de production et exécute la version `v3` de l’application.

Ce qui fait de `deployment2` le déploiement de préproduction. Ainsi, lorsque le pipeline de livraison continue (CD) est prêt à être exécuté, il déploie la version suivante de l’application, la version `v4`, sur le déploiement de préproduction `deployment2`.

![Deux déploiements : deployment1 reçoit le trafic de production](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

Une fois que `v4` a démarré sur `deployment2`, vous pouvez exécuter des tests automatisés et manuels sur ce dernier via un point de terminaison de test privé pour garantir que `v4` répond à toutes les attentes.

![V4 est maintenant déployé sur deployment2 et fait l’objet de tests](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

Lorsque vous avez confiance en `v4`, vous pouvez définir `deployment2` en tant que déploiement de production afin qu’il reçoive tout le trafic de production. `v3` continuera à s’exécuter sur `deployment1` au cas où vous découvririez un problème critique nécessitant une restauration.

![V4 sur deployment2 reçoit maintenant le trafic de production](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)

Maintenant, `deployment1` est le déploiement de préproduction. Par conséquent, la prochaine exécution du pipeline de déploiement se déploie sur `deployment1`.

![V5 déployé sur deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

Vous pouvez maintenant tester `V5` sur le point de terminaison de test privé de `deployment1`.

![V5 testé sur deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

Enfin, une fois que `v5` répond à toutes vos attentes, vous définissez `deployment1` à nouveau comme déploiement de production afin que `v5` reçoive tout le trafic de production.

![V5 reçoit le trafic sur deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### <a name="tradeoffs-of-the-alternating-deployments-approach"></a>Compromis de l’approche des déploiements alternés

L’approche des déploiements alternés est simple et rapide, car elle ne nécessite pas la création de nouveaux déploiements. Toutefois, elle présente plusieurs inconvénients, comme décrit dans les sections suivantes.

#### <a name="persistent-staging-deployment"></a>Déploiement de préproduction persistant

Le déploiement de préproduction s’exécute en continu et consomme donc des ressources de l’instance Azure Spring Cloud. Cela double les besoins en ressources de chaque application sur Azure Spring Cloud.

#### <a name="the-approval-race-condition"></a>Condition de concurrence d’approbation

Supposons que dans l’application ci-dessus, le pipeline de mise en production nécessite une approbation manuelle avant que chaque nouvelle version de l’application ne puisse recevoir le trafic de production. Cela crée le risque que, pendant qu’une version (`v6`) attend une approbation manuelle sur le déploiement de préproduction, le pipeline de déploiement s’exécute à nouveau et la remplace par une version plus récente (`v7`). Ensuite, lorsque l’approbation pour `v6` est accordée, le pipeline qui a déployé `v6` définit le déploiement de préproduction comme déploiement de production. Mais maintenant, c’est la version `v7` non approuvée, et non la version `v6` approuvée, qui est déployée sur ce déploiement et qui reçoit le trafic.

![Condition de concurrence d’approbation](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

Vous pouvez éviter la condition de concurrence en vous assurant que le flux de déploiement d’une version ne peut pas commencer tant que le flux de déploiement de toutes les versions précédentes n’est pas terminé ou abandonné. Une autre façon d’éviter la condition de concurrence d’approbation consiste à utiliser l’approche des déploiements nommés décrite ci-dessous.

## <a name="named-deployments"></a>Déploiements nommés

Dans l’approche des déploiements nommés, un nouveau déploiement est créé pour chaque nouvelle version de l’application en cours de déploiement. Une fois l’application testée sur son déploiement sur mesure, ce déploiement est défini comme déploiement de production. Le déploiement contenant la version précédente peut être autorisé à rester suffisamment longtemps pour être certain qu’une restauration ne sera pas nécessaire.

Dans l’illustration ci-dessous, la version `v5` est exécutée sur le déploiement `deployment-v5`. Le nom du déploiement contient maintenant la version parce que le déploiement a été spécialement créé pour cette version. Il n’y a pas d’autres déploiements au départ. Maintenant, pour déployer la version `v6`, le pipeline de déploiement crée un nouveau déploiement `deployment-v6` et y déploie la version de l’application `v6`.

![Déploiement d’une nouvelle version sur un déploiement nommé](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

Il n’y a aucun risque qu’une autre version soit déployée en parallèle. Premièrement, Azure Spring Cloud n’autorise pas la création d’un troisième déploiement alors que deux déploiements existent déjà. Deuxièmement, même s’il était possible d’avoir plus de deux déploiements, chaque déploiement est identifié par la version de l’application qu’il contient. Par conséquent, le pipeline qui orchestre le déploiement de `v6` tenterait uniquement de définir `deployment-v6` comme déploiement de production.

![La nouvelle version reçoit le trafic de production.](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

Une fois que le déploiement créé pour la nouvelle version reçoit le trafic de production, vous devez supprimer le déploiement qui contient la version précédente afin de libérer de l’espace pour les déploiements ultérieurs. Vous voudrez peut-être décaler d’un certain nombre de minutes ou d’heures pour pouvoir revenir à la version précédente si vous rencontrez un problème critique dans la nouvelle version.

![Après une période de sécurité, le déploiement précédent est supprimé.](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### <a name="tradeoffs-of-the-named-deployments-approach"></a>Compromis de l’approche des déploiements nommés

L’approche des déploiements nommés présente les avantages suivants :

* Elle empêche la condition de concurrence d’approbation.
* Elle réduit la consommation des ressources en supprimant le déploiement de préproduction lorsqu’il n’est pas utilisé.

Toutefois, il existe également des inconvénients, comme décrit dans la section suivante.

#### <a name="deployment-pipeline-failures"></a>Échecs du pipeline de déploiement

Entre le moment où un déploiement démarre et le moment où le déploiement de préproduction est supprimé, toutes les tentatives supplémentaires d’exécution du pipeline de déploiement échouent. Le pipeline tente de créer un nouveau déploiement, ce qui génère une erreur, car seuls deux déploiements sont autorisés par application Azure Spring Cloud.

C’est pourquoi l’orchestration de déploiement doit avoir le moyen de réessayer ultérieurement un processus de déploiement qui a échoué, ou le moyen de s’assurer que les flux de déploiement pour chaque version restent en file d’attente jusqu’à ce que le flux soit terminé pour toutes les versions précédentes.

## <a name="next-steps"></a>Étapes suivantes

* [Automatiser les déploiements d’applications dans Azure Spring Cloud](./how-to-cicd.md)
