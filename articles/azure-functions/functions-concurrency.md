---
title: Accès concurrentiel dans Azure Functions
description: Vue d’ensemble de la fonctionnalité de concurrence dynamique dans Azure Functions.
author: cachai2
ms.topic: conceptual
ms.date: 9/24/2021
ms.author: cachai
ms.openlocfilehash: 21702bf5bdc8eeee014305c106006a18640c001f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847657"
---
# <a name="concurrency-in-azure-functions"></a>Accès concurrentiel dans Azure Functions

Cet article décrit les comportements d’accès concurrentiel des déclencheurs pilotés par les événements dans Azure Functions. Il décrit également un nouveau modèle dynamique pour l’optimisation des comportements d’accès concurrentiel. 

>[!NOTE]
>Le modèle de concurrence dynamique est actuellement en préversion. La prise en charge de l’accès concurrentiel dynamique est limitée à des extensions de liaison spécifiques.

Le modèle d’hébergement pour les fonctions permet l’exécution simultanée de plusieurs appels de fonction sur une instance de calcul unique. Par exemple, considérez un cas où vous avez trois fonctions différentes dans votre application de fonction, qui est mise à l’échelle et exécutée sur plusieurs instances. Dans ce scénario, chaque fonction traite les appels sur chaque instance de machine virtuelle sur laquelle votre application de fonction s’exécute. Les appels de fonction sur une instance unique partagent les mêmes ressources de calcul de machine virtuelle, telles que la mémoire, le processeur et les connexions. Lorsque votre application est hébergée dans un plan dynamique (Consommation ou Premium), la plateforme met à l’échelle le nombre d’instances de l’application de fonction en fonction du nombre d’événements entrants. Pour plus d’informations, consultez [Mise à l’échelle pilotée par les événements](./Event-Driven-Scaling.md)). Lorsque vous hébergez vos fonctions dans un plan dédié (App Service), vous configurez manuellement vos instances ou [configurez un schéma de mise à l’échelle automatique](dedicated-plan.md#scaling).

Étant donné que plusieurs appels de fonction peuvent s’exécuter simultanément sur chaque instance, chaque fonction doit avoir un moyen de limiter le nombre d’appels simultanés qu’elle traite à un moment donné.

## <a name="static-concurrency"></a>Concurrence statique

La plupart des déclencheurs prennent en charge un modèle de configuration statique au niveau de l’hôte, qui est utilisé pour spécifier l’accès concurrentiel par instance pour ce type de déclencheur. Par exemple, le [déclencheur Service Bus](./functions-bindings-service-bus-trigger.md) fournit des paramètres `MaxConcurrentCalls` et `MaxConcurrentSessions` dans le [fichier host.json](functions-host-json.md). Ces paramètres contrôlent ensemble le nombre maximal de messages que chaque fonction traite simultanément sur chaque instance. D’autres types de déclencheurs intègrent des mécanismes pour les appels d’équilibrage de charge entre les instances. Par exemple, Event Hubs et Azure Cosmos DB utilisent tous les deux un schéma basé sur les partitions. 

Pour les types de déclencheurs qui prennent en charge la configuration de l’accès concurrentiel, il existe un comportement par défaut, que vous pouvez choisir de remplacer dans le fichier host.json de votre application de fonction. Ces paramètres, qui s’appliquent à toutes les instances en cours d’exécution, vous permettent de contrôler la concurrence maximale pour vos fonctions sur chaque instance. Par exemple, lorsque votre fonction est gourmande en processeur ou en ressources, vous pouvez choisir de limiter l’accès concurrentiel pour assurer l’intégrité des instances. De même, lorsque votre fonction effectue des requêtes auprès d’un service en aval qui est limité, vous devez également envisager de limiter la concurrence. 

Bien que ces configurations d’accès concurrentiel vous permettent de contrôler certains comportements de déclencheur, comme la limitation de vos fonctions, il peut être difficile de déterminer les valeurs optimales pour ces paramètres. En règle générale, vous devez obtenir des valeurs acceptables en procédant par des essais et des tests de charge. Même lorsque vous déterminez un ensemble de valeurs qui fonctionnent pour un profil de charge particulier, le nombre d’événements arrivant de vos services connectés peut changer d’un jour à l’autre. Cette variabilité signifie que votre application peut souvent s’exécuter avec des valeurs non optimales. Par exemple, votre application de fonction peut traiter des charges utiles de messages particulièrement exigeantes le dernier jour de la semaine, ce qui vous oblige à limiter la concurrence. Toutefois, pendant le reste de la semaine, les charges utiles de message sont plus simples, ce qui signifie que vous pouvez utiliser un niveau de concurrence plus élevé. 

Dans l’idéal, nous voulons que le système autorise les instances à traiter autant de travail qu’elles le permettent, tout en maintenant l’intégrité et les latences de chaque instance, ce pour quoi l’accès concurrentiel dynamique est conçu.

## <a name="dynamic-concurrency-preview"></a>Accès concurrentiel dynamique (préversion)

Functions fournit désormais un modèle de concurrence dynamique qui simplifie la configuration de l’accès concurrentiel pour toutes les applications de fonction s’exécutant dans le même plan. 

> [!NOTE]
> L’accès concurrentiel dynamique n’est actuellement pris en charge que pour les déclencheurs de Blob Azure, de File d’attente Azure et de Service Bus. Il exige que vous utilisiez les versions répertoriées dans la [section relative à la prise en charge des extensions ci-dessous](#extension-support).

### <a name="benefits"></a>Avantages

L’utilisation de la concurrence dynamique offre les avantages suivants : 

- **Configuration simplifiée** : vous n’avez plus besoin de déterminer manuellement les paramètres de concurrence par déclenchement. Le système apprend les valeurs optimales pour votre charge de travail au fil du temps. 
- **Ajustements dynamiques** : l’accès concurrentiel est ajusté dynamiquement en temps réel, ce qui permet au système de s’adapter à la modification des modèles de charge dans le temps. 
- **Protection d’intégrité de l’instance** : le runtime limite l’accès concurrentiel à des niveaux qu’une instance d’application de fonction peut gérer confortablement. Cela empêche l’application de se surcharger elle-même en prenant plus de travail qu’elle ne le devrait. 
- **Débit amélioré** : le débit global est amélioré, car les instances individuelles ne prennent pas plus de travail qu’elles ne peuvent traiter rapidement. Cela permet d’équilibrer la charge de travail de façon plus efficace entre les instances. Pour les fonctions qui peuvent gérer des charges plus élevées, l’accès concurrentiel peut être augmenté à des valeurs qui dépassent les valeurs de configuration par défaut, ce qui produit un débit plus élevé.

### <a name="dynamic-concurrency-configuration"></a>Configuration de la concurrence dynamique

L’accès concurrentiel dynamique peut être activé au niveau de l’hôte dans le fichier host.json. Quand la fonction est activée, toutes les extensions de liaison utilisées par votre application de fonction qui prennent en charge l’accès concurrentiel dynamique ajustent la concurrence de manière dynamique selon les besoins. Les paramètres de concurrence dynamique remplacent tous les paramètres de concurrence configurés manuellement pour les déclencheurs qui prennent en charge l’accès concurrentiel dynamique. 

Par défaut, la concurrence dynamique est désactivée. Avec la concurrence dynamique activée, la concurrence commence à 1 pour chaque fonction et est ajustée à une valeur optimale, qui est déterminée par l’hôte.

Vous pouvez activer l’accès concurrentiel dynamique dans votre application de fonction en ajoutant les paramètres suivants dans votre fichier host.json : 

```json
    { 
        "version": "2.0", 
        "concurrency": { 
            "dynamicConcurrencyEnabled": true, 
            "snapshotPersistenceEnabled": true 
        } 
    } 
```

 Quand `SnapshotPersistenceEnabled` est `true`, ce qui correspond à la valeur par défaut, les valeurs d’accès concurrentiel apprises sont régulièrement conservées dans le stockage. Par conséquent, les nouvelles instances commencent à partir de ces valeurs au lieu de commencer à partir de 1 et de recommencer l’apprentissage. 

### <a name="concurrency-manager"></a>Gestionnaire d’accès concurrentiel 

En arrière-plan, lorsque l’accès concurrentiel dynamique est activé, un processus de gestionnaire d’accès concurrentiel s’exécute en arrière-plan. Ce gestionnaire surveille en permanence les mesures d’intégrité de l’instance, comme l’utilisation du processeur et des threads, et modifie les limitations en fonction des besoins. Quand une ou plusieurs limitations sont activées, la concurrence des fonctions est ajustée jusqu’à ce que l’hôte soit de nouveau sain. Lorsque les limitations sont désactivées, la concurrence est autorisée à augmenter. Diverses méthodes heuristiques sont utilisées pour ajuster intelligemment la concurrence en fonction des besoins selon ces limitations. Au fil du temps, la concurrence pour chaque fonction se stabilise à un niveau particulier. 

Les niveaux de concurrence sont gérés pour chaque fonction individuelle. Par conséquent, le système équilibre les fonctions gourmandes en ressources qui requièrent un faible niveau de simultanéité et les fonctions plus légères qui peuvent gérer une concurrence plus élevée. L’équilibre de la concurrence pour chaque fonction permet de maintenir l’intégrité globale de l’instance d’application de fonction.  

Lorsque l’accès concurrentiel dynamique est activé, vous verrez les décisions de concurrence dynamique dans vos journaux. Par exemple, vous verrez des journaux lorsque différentes limitations sont activées et chaque fois que la concurrence est ajustée à la hausse ou à la baisse pour chaque fonction. Ces journaux sont écrits sous la catégorie **Host.Concurrency** dans la table des traces. 

### <a name="extension-support"></a>Extensions prises en charge 

La concurrence dynamique est activée pour une application de fonction au niveau de l’hôte, et toutes les extensions qui prennent en charge la concurrence dynamique s’exécutent dans ce mode. La concurrence dynamique nécessite une collaboration entre l’hôte et les extensions de déclencheur individuelles. Pour la préversion, seules les versions répertoriées des extensions suivantes prennent en charge la concurrence dynamique.

#### <a name="azure-queues"></a>Files d'attente Azure

Le déclencheur stockage de File d’attente Azure possède sa propre boucle d’interrogation des messages. Lors de l’utilisation d’une configuration statique, la concurrence est régie par les options de configuration `BatchSize`/`NewBatchThreshold`. Lorsque vous utilisez un accès concurrentiel dynamique, ces valeurs de configuration sont ignorées. L’accès concurrentiel dynamique étant intégré à la boucle de messages, le nombre de messages extraits par itération est ajusté de manière dynamique. Lorsque des limitations sont activées (l’hôte est surchargé), le traitement des messages est suspendu jusqu’à ce que les limitations soient désactivées. Lorsque les limitations sont désactivées, la concurrence augmente.

Pour utiliser l’accès concurrentiel dynamique pour les Files d’attente, vous devez utiliser la [version 5.x](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) de l’extension de stockage.

#### <a name="azure-blobs"></a>Objets blob Azure

En interne, le déclencheur stockage Blob Azure utilise la même infrastructure que le déclencheur de File d’attente Azure. Lorsque des blobs nouveaux ou mis à jour doivent être traités, les messages sont écrits dans une file d’attente de contrôle managée de la plateforme, et la file d’attente est traitée à l’aide de la même logique que celle utilisée pour le déclencheur de File d’attente. Lorsque la concurrence dynamique est activée, la concurrence pour le traitement de cette file d’attente de contrôle est gérée de façon dynamique.

Pour utiliser l’accès concurrentiel dynamique pour les Blobs, vous devez utiliser la [version 5.x](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) de l’extension de stockage.


#### <a name="service-bus"></a>Service Bus 

Le déclencheur Service Bus prend actuellement en charge trois modèles d’exécution. La concurrence dynamique affecte ces modèles d’exécution comme suit :
 
- **Traitement de la rubrique/file d’attente à distribution unique** : chaque appel de votre fonction traite un message unique. Lors de l’utilisation de la configuration statique, la concurrence est régie par l’option de configuration MaxConcurrentCalls. Lors de l’utilisation de la concurrence dynamique, cette valeur de configuration est ignorée et la concurrence est ajustée dynamiquement. 
- **Traitement de la rubrique/file d’attente à distribution unique basée sur la session** : chaque appel de votre fonction traite un message unique. En fonction du nombre de sessions actives pour votre rubrique/file d’attente, chaque instance loue une ou plusieurs sessions. Les messages de chaque session sont traités en série, afin de garantir l’ordre dans une session. Lorsque la concurrence dynamique n’est pas utilisée, la concurrence est régie par le paramètre `MaxConcurrentSessions`. Avec la concurrence dynamique activée, `MaxConcurrentSessions` est ignoré et le nombre de sessions que chaque instance traite est ajusté dynamiquement. 
- **Traitement par lots** : chaque appel de votre fonction traite un lot de messages, régi par le paramètre `MaxMessageCount`. Étant donné que les appels de lot sont en série, la concurrence pour votre fonction déclenchée par lot est toujours 1 et la concurrence dynamique ne s’applique pas. 

Pour permettre à votre déclencheur Service Bus d’utiliser la concurrence dynamique, vous devez utiliser la [version 5](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) de l’extension Service Bus. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](event-driven-scaling.md)
