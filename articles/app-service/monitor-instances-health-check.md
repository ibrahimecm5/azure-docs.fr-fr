---
title: Superviser l’intégrité des instances App Service
description: Découvrez comment superviser l’intégrité des instances App Service en utilisant le contrôle d’intégrité.
keywords: service d’application Azure, application Web, contrôle de l’intégrité, itinéraire de trafic, instances intègres, chemin d’accès, surveillance, suppression des instances défectueuses, instances défectueuses, suppressions des Workers
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 59e3d0f8e71de6333b3978dd9d40b51206a8a2a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462345"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Superviser des instances App Service à l’aide du contrôle d’intégrité

Cet article utilise le contrôle d’intégrité dans le portail Azure pour superviser des instances App Service. Le contrôle d’intégrité augmente la disponibilité de votre application en réacheminant les requêtes en dehors des instances défectueuses et en remplaçant les instances si elles demeurent défectueuses. Votre [plan App Service](./overview-hosting-plans.md) doit être mis à l’échelle vers deux instances ou plus pour utiliser pleinement le contrôle d’intégrité. Le chemin du contrôle d’intégrité doit vérifier les composants critiques de votre application. Par exemple, si votre application dépend d’une base de données et d’un système de messagerie, le point de terminaison de contrôle d’intégrité doit se connecter à ces composants. Si l’application ne peut pas se connecter à un composant critique, le chemin doit retourner un code de réponse de niveau 500 pour indiquer que l’application n’est pas saine.

![Échec du contrôle d’intégrité][1]

## <a name="what-app-service-does-with-health-checks"></a>Que fait App Service avec les contrôles d’intégrité ?

- Quand un chemin est donné sur votre application, le contrôle d’intégrité effectue un test ping de ce chemin sur toutes les instances de votre application App Service par intervalles de 1 minute.
- Si une instance ne répond pas par un code d’état compris entre 200 et 299 (compris) à l’issue de deux requêtes ou plus, ou ne parvient pas à répondre à la commande ping, le système détermine qu’elle n’est pas saine et la supprime.
- Après cette suppression, le contrôle d’intégrité continue d’effectuer un test ping sur l’instance qui n’est pas saine. Si l’instance commence à répondre avec un code d’état sain (200-299), elle est retournée à l’équilibreur de charge.
- Si une instance n’est pas saine pendant une heure, elle sera remplacée par une nouvelle instance.
- De plus, dans le cadre d’un scale-up ou d’un scale-out, App Service effectue un test ping sur le chemin de contrôle d’intégrité pour vérifier que de nouvelles instances sont prêtes.

> [!NOTE]
>- Le contrôle d’intégrité ne suit pas les redirections 302. Une instance au plus sera remplacée chaque heure, avec un maximum de trois instances par jour et par plan App Service.
>- Remarque : Si votre contrôle d’intégrité attribue l’état `Waiting for health check response`, la vérification est susceptible d’échouer en raison d’un code d’état HTTP 307, ce qui peut se produire si vous avez activé la redirection HTTPS, mais avez désactivé `HTTPS Only`.

## <a name="enable-health-check"></a>Activer le contrôle d’intégrité

![Navigation du contrôle d’intégrité dans le portail Azure][3]

- Pour activer le contrôle d’intégrité, accédez au portail Azure et sélectionnez votre application App Service.
- Sous **Supervision**, sélectionnez **Contrôle d’intégrité**.
- Sélectionnez **Activer** et fournissez un chemin d’URL valide pour votre application, par exemple `/health` ou `/api/health`.
- Cliquez sur **Enregistrer**.

> [!CAUTION]
> Les modifications apportées à la configuration du contrôle d’intégrité entraînent le redémarrage de votre application. Pour réduire l’impact sur les applications de production, nous vous recommandons de [configurer des emplacements de préproduction](deploy-staging-slots.md) et de basculer vers des emplacements de production.
>

### <a name="configuration"></a>Configuration

En plus de la configuration des options de contrôle d’intégrité, vous pouvez aussi configurer les [paramètres d’application](configure-common.md) suivants :

| Nom du paramètre d’application | Valeurs autorisées | Description |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Nombre requis de demandes ayant échoué pour qu’une instance soit considérée comme défectueuse et supprimée de l’équilibreur de charge. Par exemple, quand la valeur est `2`, vos instances sont supprimées à l’issue de `2` échecs de test ping. (La valeur par défaut est `10`) |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0 - 100 | Par défaut, pas plus de la moitié des instances seront exclues de l’équilibreur de charge en une fois pour éviter de surcharger les instances intègres restantes. Par exemple, si un plan App Service est mis à l’échelle vers quatre instances et que trois d’entre elles ne sont pas intègres, seules deux seront exclues. Les deux autres instances (une saine et une non saine) continuent de recevoir des requêtes. Dans le pire des cas, où aucune instance n’est saine, aucune ne sera exclue. <br /> Pour remplacer ce comportement, définissez le paramètre d’application sur une valeur comprise entre `0` et `100`. Plus la valeur est élevée, plus le nombre d’instances défectueuses supprimées est élevé (la valeur par défaut est `50`). |

#### <a name="authentication-and-security"></a>Authentification et sécurité

Le contrôle d’intégrité s’intègre aux [fonctionnalités d’authentification et d’autorisation](overview-authentication-authorization.md) d’App Service. Aucun paramètre supplémentaire n’est nécessaire si ces fonctionnalités de sécurité sont activées.

Si vous utilisez votre propre système d’authentification, le chemin du contrôle d’intégrité doit autoriser l’accès anonyme. Pour sécuriser le point de terminaison du contrôle d’intégrité, vous devez d’abord utiliser des fonctionnalités comme des [restrictions d’adresse IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), des [certificats clients](app-service-ip-restrictions.md#set-an-ip-address-based-rule) ou un réseau virtuel pour restreindre l’accès à l’application. Vous pouvez sécuriser le point de terminaison du contrôle d’intégrité en exigeant que la propriété `User-Agent` de la requête entrante corresponde à `HealthCheck/1.0`. La propriété User-Agent ne peut pas être falsifiée, car la requête a déjà été sécurisée par les fonctionnalités de sécurité précédentes.

## <a name="monitoring"></a>Surveillance

Après avoir fourni le chemin de contrôle d’intégrité de votre application, vous pouvez superviser l’intégrité de votre site à l’aide d’Azure Monitor. Dans le panneau **Contrôle d’intégrité** du portail, cliquez sur **Métriques** dans la barre d’outils supérieure. Un nouveau panneau s’ouvre, dans lequel vous pouvez voir l’état d’intégrité historique du site et créer une nouvelle règle d’alerte. Pour plus d’informations sur la surveillance de vos sites, [consultez le guide sur Azure Monitor](web-sites-monitor.md).

## <a name="limitations"></a>Limites

- Le contrôle d’intégrité ne doit pas être activé sur les sites de fonctions Premium. En raison de la mise à l’échelle rapide des Fonctions Premium, les demandes de contrôle d’intégrité peuvent entraîner des fluctuations inutiles dans le trafic HTTP. Les fonctions Premium ont leurs propres sondes d’intégrité interne qui sont utilisées pour informer les décisions de mise à l’échelle.
- Le contrôle d’intégrité peut être activé pour les Plans App Service **Gratuits** et **Partagés** de sorte que vous pouvez obtenir des métriques concernant l’intégrité du site et définir des alertes. Cependant, comme les sites **Gratuits** et **Partagés** ne peuvent pas être mis à l’échelle, les instances défectueuses ne seront pas remplacées. Vous devez effectuer une mise à l’échelle vers le niveau de **Base** ou une version ultérieure, afin de pouvoir effectuer une progression jusqu’à 2 instances ou plus et tirer parti de tous les avantages du contrôle d’intégrité. Cette option est recommandée pour les applications de production, car elle augmente la disponibilité et les performances de votre application.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>Que se passe-t-il si mon application s’exécute sur une seule instance ?

Si votre application n’est mise à l’échelle que sur une seule instance et devient défectueuse, elle ne sera pas supprimée de l’équilibreur de charge, car cela entraînerait l’arrêt complet de votre application. Montez jusqu’à deux instances ou plus pour profiter du réacheminement du contrôle d’intégrité. Si votre application s’exécute sur une instance unique, vous pouvez toujours utiliser la fonctionnalité de [surveillance](#monitoring) du contrôle d’intégrité pour effectuer le suivi de l’intégrité de votre application.
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>Pourquoi la demande de contrôle d’intégrité ne s’ouvre-t-elle pas dans mes journaux frontaux ?

La demande de contrôle d’intégrité étant envoyée en interne à votre site, la demande n’apparaît pas dans [les journaux frontaux](troubleshoot-diagnostic-logs.md#enable-web-server-logging). Cela signifie également que la demande aura une origine de `127.0.0.1` car elle est envoyée en interne. Vous pouvez ajouter des instructions de journal dans votre code de contrôle d’intégrité pour conserver les journaux lorsque votre chemin d’accès de contrôle d’intégrité est interrogé.

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>Les demandes de contrôle d’intégrité sont-elles envoyées via HTTP ou HTTPS ?

Sur Windows App Service, les demandes de vérification d’intégrité sont envoyées via HTTPS quand [HTTPS uniquement](configure-ssl-bindings.md#enforce-https) est activé sur le site. Dans le cas contraire, elles seront envoyées via HTTP. Sur Linux App Service, les demandes de vérification d’intégrité sont uniquement envoyées sur HTTP et ne peuvent pas être envoyées sur HTTP **S** à l’heure actuelle.

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>Que se passe-t-il si j’ai plusieurs applications sur le même Plan App Service ?

Les instances défectueuses seront toujours supprimées de la rotation de l’équilibreur de charge, quelles que soient les autres applications du plan App Service (jusqu’au pourcentage spécifié dans [`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration)). Quand une application sur une instance reste défectueuse pendant plus d’une heure, l’instance est remplacée uniquement si toutes les autres applications avec contrôle d’intégrité activé sont également défectueuses. Les applications qui n’ont pas de contrôle d’intégrité activé ne sont pas prises en compte. 

#### <a name="example"></a>Exemple 

Imaginez que vous avez deux applications (ou une application avec un emplacement) avec le contrôle d’intégrité activé, appelées Application A et application B. Elles se trouvent sur le même plan App Service et le Plan va jusqu’à 4 instances. Si l’application A devient défectueuse sur deux instances, l’équilibreur de charge cessera d’envoyer des demandes à l’application A sur ces deux instances. Les demandes seront toujours acheminées vers l’application B sur les instances en supposant que l’application B est intègre. Si l’application A reste défectueuse pendant plus d’une heure sur ces deux instances, ces instances seront remplacées uniquement si l’application B est **également** défectueuse sur ces instances. Si l’application B est intègre, l’instance n’est pas remplacée.

![Diagramme visuel expliquant l’exemple de scénario ci-dessus.][2]

> [!NOTE]
> S’il existe un autre site ou un autre emplacement du Plan (Site C) sans contrôle d’intégrité activé, celui-ci ne sera pas pris en compte pour le remplacement de l’instance.

### <a name="what-if-all-my-instances-are-unhealthy"></a>Que se passe-t-il si aucune de mes instances n’est intègre ?

Dans le cas où toutes les instances de votre application sont défectueuses, App Service supprime les instances de l’équilibreur de charge jusqu’au pourcentage spécifié dans `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`. Dans ce scénario, l’exécution de toutes les instances d’application défectueuses à partir de la rotation de l’équilibreur de charge entraînerait une panne de votre application.

### <a name="does-health-check-work-on-app-service-environments"></a>Le contrôle d’intégrité fonctionne-t-il sur les environnements App Service ?

Oui, dans les environnements App Service (ASE), la plateforme effectue un test Ping sur vos instances sur le chemin d’accès spécifié et supprime les instances défectueuses de l’équilibreur de charge, de sorte que les requêtes ne sont pas acheminées vers elles. Toutefois, ces instances non saines ne seront pas remplacées par de nouvelles instances si elles ne restent non saines pendant 1 heure.

## <a name="next-steps"></a>Étapes suivantes
- [Créez une alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [Créez une alerte de journal d’activité pour surveiller tous les échecs d’opérations de diminution et d’augmentation de la taille des instances de la mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [Informations de référence sur les variables d’environnement et les paramètres d’application](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
