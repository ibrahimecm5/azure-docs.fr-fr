---
title: Guide de référence sur les limites et la configuration
description: Guide de référence sur les limites et informations de configuration relatives à Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 11/02/2021
ms.openlocfilehash: 68d587888a915ed317b6a31f9000d0086adda21b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283483"
---
# <a name="limits-and-configuration-reference-for-azure-logic-apps"></a>Limites et références de configuration pour Azure Logic Apps

> Pour Power Automate, consultez [Limites et configuration dans Power Automate](/power-automate/limits-and-config).

Cet article décrit les limites et informations de configuration relatives à Azure Logic Apps et aux ressources associées. Pour créer des workflows d’application logique, vous choisissez le type de ressource d’**application logique** en fonction de votre scénario, les spécifications de la solution, les fonctionnalités souhaitées et l’environnement dans lequel vous voulez exécuter vos workflows.

> [!NOTE]
> De nombreuses limites sont identiques dans les environnements disponibles où Azure Logic Apps s’exécute, mais les différences sont indiquées là où elles existent. 

Le tableau suivant résume brièvement les différences entre le type de ressource d’**application logique (Consommation)** d’origine et le type de ressource d’**application logique (Standard)** . Vous apprendrez également comment l’environnement *monolocataire* est comparé aux environnements *mutualisés* et de *service d’intégration (ISE)* pour le déploiement, l’hébergement et l’exécution de vos workflows d’application logique.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## <a name="workflow-definition-limits"></a>Limites relatives aux définitions de workflow

Les tableaux suivants répertorient les valeurs possibles pour une définition de workflow unique :

| Nom | Limite | Notes |
| ---- | ----- | ----- |
| Flux de travail par région et par abonnement | 1 000 workflows ||
| Déclencheurs par flux de travail | 10 déclencheurs | Cette limite s'applique uniquement lorsque vous travaillez sur la définition de workflow JSON, que ce soit en mode Code ou dans un modèle Azure Resource Manager (ARM), et non sur le concepteur. |
| Actions par flux de travail | 500 actions | Pour étendre cette limite, vous pouvez au besoin utiliser des workflows imbriqués. |
| Profondeur d'imbrication des actions | 8 actions | Pour étendre cette limite, vous pouvez au besoin utiliser des workflows imbriqués. |
| Déclencheur ou action - Longueur maximale du nom | 80 caractères ||
| Déclencheur ou action - Taille maximale des entrées ou des sorties | 104 857 600 octets <br>(105 Mo) | Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Action - Taille maximale des entrées et sorties combinées | 209 715 200 octets <br>(210 Mo) | Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Limite de caractères de l'expression | 8 192 caractères ||
| `description` - Longueur maximale | 256 caractères ||
| `parameters` - Nombre maximal d'éléments | 50 paramètres ||
| `outputs` - Nombre maximal d'éléments | 10 sorties ||
| `trackedProperties` - Taille maximale | 8 000 caractères ||
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>Durée d’exécution et limites de conservation de l’historique

Le tableau suivant répertorie les valeurs possibles pour une exécution de workflow unique :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Conservation de l’historique des exécutions dans le stockage | 90 jours | 90 jours <br>(Par défaut) | 366 jours | Durée de conservation de l’historique des exécutions d’un workflow dans le stockage après le démarrage d’une exécution. <p><p>**Remarque** : si la durée de l’exécution dépasse la limite de rétention, l’exécution est supprimée de l’historique d’exécution dans le stockage. Si une exécution n’est pas immédiatement supprimée après avoir atteint la limite de rétention, l’exécution est supprimée dans les 7 jours. <p><p>Qu’une exécution se termine ou expire, la rétention de l’historique des exécutions est toujours calculée en utilisant l’heure de début de l’exécution et la limite actuelle spécifiée dans le paramètre du workflow, [**Conservation de l’historique des exécutions en jours**](#change-retention). Quelle que soit la limite précédente, la limite actuelle est toujours utilisée pour le calcul de la rétention. <p><p>Pour plus d'informations, consultez [Modifier la durée et la conservation de l'historique des exécutions dans le stockage](#change-retention). |
| Durée d’exécution | 90 jours | - Workflow avec état : 90 jours <br>(Par défaut) <p><p>- Workflow sans état : 5 minutes <br>(Par défaut) | 366 jours | Durée pendant laquelle un workflow peut continuer à s'exécuter avant de forcer un délai d'expiration. <p><p>La durée d'exécution est calculée à l'aide de l'heure de début d'une exécution et de la limite spécifiée par le paramètre de workflow, [**Conservation de l'historique des exécutions en jours**](#change-duration) à l'heure de début. <p>**Important** : veillez à ce que la valeur de la durée d'exécution soit toujours inférieure ou égale à la valeur de conservation de l'historique des exécutions dans le stockage. Sinon, les historiques des exécutions peuvent être supprimés avant la fin des travaux associés. <p><p>Pour plus d'informations, consultez [Modifier la durée et la conservation de l'historique des exécutions dans le stockage](#change-duration). |
| Intervalle de récurrence | - Min : 1 seconde <p><p>- Max : 500 jours | - Min : 1 seconde <p><p>- Max : 500 jours | - Min : 1 seconde <p><p>- Max : 500 jours ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>Modifier la durée d’exécution et la conservation de l’historique dans le stockage

Dans le concepteur, le même paramètre contrôle le nombre maximal de jours pendant lesquels un workflow peut s'exécuter, ainsi que la conservation de l'historique des exécutions dans le stockage.

* Pour le service multilocataire, la limite par défaut de 90 jours est identique à la limite maximale. Vous pouvez uniquement réduire cette valeur.

* Pour le service monolocataire, vous pouvez réduire ou augmenter la limite par défaut de 90 jours. Pour plus d’informations, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md).

* Pour un environnement de service d'intégration, vous pouvez réduire ou augmenter la limite par défaut de 90 jours.

Par exemple, supposons que vous réduisiez la limite de conservation de 90 à 30 jours. Une exécution datant de 60 jours est supprimée de l’historique des exécutions. Si vous augmentez la période de conservation de 30 à 60 jours, une exécution datant de 20 jours reste dans l’historique des exécutions pendant 40 jours supplémentaires.

> [!IMPORTANT]
> Si la durée de l'exécution dépasse la limite de conservation actuelle, l'exécution est supprimée de l’historique des exécutions dans le stockage. Pour éviter de perdre l’historique des exécutions, assurez-vous que la limite de conservation est *toujours* plus que la durée la plus longue de l’exécution.

Pour modifier la valeur par défaut ou la limite actuelle de ces propriétés, procédez comme suit :

#### <a name="portal-multi-tenant-service"></a>[Portail (service multilocataire)](#tab/azure-portal)

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Logic Apps**.

1. Accédez à votre application logique et ouvrez-la dans le Concepteur d'applications logiques.

1. Dans le menu de l’application logique, sélectionnez **Paramètres de flux de travail**.

1. Sous **Options de runtime**, dans la liste **Conservation de l’historique des exécutions en jours**, choisissez **Personnalisée**.

1. Faites glisser le curseur pour modifier le nombre de jours souhaité.

1. Lorsque vous avez terminé, dans la barre d’outils **Paramètres du workflow**, sélectionnez **Enregistrer**.

#### <a name="resource-manager-template"></a>[modèle Azure Resource Manager](#tab/azure-resource-manager)

Si vous utilisez un modèle Azure Resource Manager, ce paramètre apparaît sous forme de propriété dans la définition de ressource de votre workflow, comme décrit dans [Informations de référence sur le modèle de workflow Microsoft.Logic](/azure/templates/microsoft.logic/workflows) :

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## <a name="looping-concurrency-and-debatching-limits"></a>Limites de bouclage, de simultanéité et de décomposition

Le tableau suivant répertorie les valeurs possibles pour une exécution de workflow unique :

### <a name="loop-actions"></a>Actions de bouclage

<a name="for-each-loop"></a>

#### <a name="for-each-loop"></a>Boucle For Each

Le tableau suivant répertorie les valeurs possibles pour une boucle **For each** :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Éléments de tableau | 100 000 éléments | - Workflow avec état : 100 000 éléments <br>(Par défaut) <p><p>- Workflow sans état : 100 éléments <br>(Par défaut) | 100 000 éléments | Nombre d'éléments de tableau qu'une boucle **For each** peut traiter. <p><p>Pour filtrer des tables plus grandes, vous pouvez utiliser l’[action de requête](logic-apps-perform-data-operations.md#filter-array-action). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Itérations simultanées | Simultanéité désactivée : 20 <p><p>Simultanéité activée : <p>- Par défaut : 20 <br>- Min : 1 <br>- Max : 50 | Simultanéité désactivée : 20 <br>(Par défaut) <p><p>Simultanéité activée : <p><p>- Par défaut : 20 <br>- Min : 1 <br>- Max : 50 | Simultanéité désactivée : 20 <p><p>Simultanéité activée : <p>- Par défaut : 20 <br>- Min : 1 <br>- Max : 50 | Nombre d'itérations de boucles **For each** qui peuvent s'exécuter simultanément ou en parallèle. <p><p>Pour modifier cette valeur dans le service multilocataire, consultez [Modifier la limite de simultanéité **For each**](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) ou [Exécuter des boucles **For each** de manière séquentielle](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
||||||

<a name="until-loop"></a>

#### <a name="until-loop"></a>Boucle Until

Le tableau suivant répertorie les valeurs possibles pour une boucle **Until** :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Itérations | - Par défaut : 60 <br>- Min : 1 <br>- Max : 5 000 | Workflow avec état : <p><p>- Par défaut : 60 <br>- Min : 1 <br>- Max : 5 000 <p><p>Workflow sans état : <p><p>- Par défaut : 60 <br>- Min : 1 <br>- Max : 100 | - Par défaut : 60 <br>- Min : 1 <br>- Max : 5 000 | Nombre de cycles que peut comporter une boucle **Until** pendant l'exécution d'un workflow. <p><p>Pour modifier cette valeur dans le service monolocataire, dans la forme de la boucle **Until**, sélectionnez **Modifier les limites**, puis spécifiez la valeur de la propriété **Count**. <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Délai d'expiration | Par défaut : PT1H (1 heure) | Workflow avec état : PT1H (1 heure) <p><p>Workflow sans état : PT5M (5 minutes) | Par défaut : PT1H (1 heure) | Durée d'exécution de la boucle **Until** avant qu'elle ne se termine, spécifiée au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). La valeur du délai d’attente est évaluée pour chaque cycle de boucle. Si une action dans la boucle dure plus longtemps que la limite de délai d’attente, le cycle actuel ne s’arrête pas. Toutefois, le cycle suivant ne démarre pas, car la condition de limite n’est pas remplie. <p><p>Pour modifier cette valeur dans le service monolocataire, dans la forme de la boucle **Until**, sélectionnez **Modifier les limites**, puis spécifiez la valeur de la propriété **Timeout**. <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
||||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>Accès concurrentiel et décomposition

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Déclencheur - exécutions simultanées | Simultanéité désactivée : llimité <p><p>Simultanéité activée (irréversible) : <p><p>- Par défaut : 25 <br>- Min : 1 <br>- Max : 100 | Simultanéité désactivée : llimité <p><p>Simultanéité activée (irréversible) : <p><p>- Valeur par défaut : 100 <br>- Min : 1 <br>- Max : 100 | Simultanéité désactivée : llimité <p><p>Simultanéité activée (irréversible) : <p><p>- Par défaut : 25 <br>- Min : 1 <br>- Max : 100 | Nombre d'exécutions simultanées qu'un déclencheur peut lancer en même temps ou en parallèle. <p><p>**Remarque** : lorsque la simultanéité est activée, la limite **SplitOn** est réduite à 100 éléments pour la [décomposition des tableaux](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Pour modifier cette valeur dans le service multilocataire, consultez [Modifier la limite de simultanéité du déclencheur](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) ou [Déclencher des instances de manière séquentielle](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Exécutions en attente maximale | Simultanéité désactivée : <p><p>- Min : 1 exécution <p>- Max : 50 exécutions <p><p>Simultanéité activée : <p><p>- Min : 10 exécutions, plus le nombre d'exécutions simultanées <p>- Max : 100 exécutions | Simultanéité désactivée : <p><p>- Min : 1 exécution <br>(Par défaut) <p>- Max : 50 exécutions <br>(Par défaut) <p><p>Simultanéité activée : <p><p>- Min : 10 exécutions, plus le nombre d'exécutions simultanées <p>- Max : 200 exécutions <br>(Par défaut) | Simultanéité désactivée : <p><p>- Min : 1 exécution <p>- Max : 50 exécutions <p><p>Simultanéité activée : <p><p>- Min : 10 exécutions, plus le nombre d'exécutions simultanées <p>- Max : 100 exécutions | Nombre d'instances de workflow dont l'exécution peut attendre lorsque votre instance de workflow actuelle exécute déjà le nombre maximal d'instances simultanées. <p><p>Pour modifier cette valeur dans le service multilocataire, consultez [Modifier la limite d'exécutions en attente](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Éléments **SplitOn** | Simultanéité désactivée : 100 000 éléments <p><p>Simultanéité activée : 100 éléments | Simultanéité désactivée : 100 000 éléments <p><p>Simultanéité activée : 100 éléments | Simultanéité désactivée : 100 000 éléments <br>(Par défaut) <p><p>Simultanéité activée : 100 éléments <br>(Par défaut) | Pour les déclencheurs qui renvoient un tableau, vous pouvez spécifier une expression utilisant une propriété **SplitOn** qui [fractionne ou décompose des éléments de tableau en plusieurs instances de workflow](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) à des fins de traitement, au lieu d'utiliser une boucle **For each**. Cette expression fait référence au tableau à utiliser pour la création et l’exécution d’une instance de workflow pour chaque élément du tableau. <p><p>**Remarque** : lorsque la simultanéité est activée, la limite **SplitOn** est réduite à 100 éléments. |
||||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limites de débit

Le tableau suivant répertorie les valeurs possibles pour une définition de workflow unique :

| Nom | Multi-locataire | Monolocataire | Notes |
|------|--------------|---------------|-------|
| Action - Exécutions par intervalle de roulement de 5 minutes | Par défaut : 100 000 exécutions <br>- Mode de débit élevé : 300 000 exécutions | Aucun | Dans le service multilocataire, vous pouvez augmenter la valeur par défaut pour définir la valeur maximale de votre workflow. Pour plus d’informations, consultez [Exécuter en mode de débit élevé](#run-high-throughput-mode), qui est une fonctionnalité en préversion. Ou, si nécessaire, vous pouvez [répartir la charge de travail entre plusieurs workflows](handle-throttling-problems-429-errors.md#logic-app-throttling). |
| Action - Appels sortants simultanés | Environ 2 500 appels | Aucun | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. |
| Limitation des connecteurs managés | Le seuil de limitation varie en fonction du connecteur | Le seuil de limitation varie en fonction du connecteur | Pour le service multilocataire, consultez la [page de référence technique de chaque connecteur managé](/connectors/connector-reference/connector-reference-logicapps-connectors). <p><p>Pour plus d'informations sur la gestion de la limitation des connecteurs, consultez [Gérer les problèmes de limitation (erreurs « 429 - Trop de requêtes »)](handle-throttling-problems-429-errors.md#connector-throttling). |
| Point de terminaison du runtime - Appels entrants simultanés | Environ 1 000 appels | Aucun | Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. |
| Point de terminaison du runtime - Appels de lecture par tranche de 5 minutes  | 60 000 appels de lecture | Aucun | Cette limite s'applique aux appels qui obtiennent les entrées et sorties brutes à partir de l'historique des exécutions d'un workflow. Si nécessaire, vous pouvez répartir la charge de travail entre plusieurs workflows. |
| Point de terminaison du runtime - Appels d'invocation par tranche de 5 minutes | 45 000 appels d'invocation | Aucun | Si nécessaire, vous pouvez répartir la charge de travail entre plusieurs workflows. |
| Débit de contenu par tranche de 5 minutes | 600 Mo | Aucun | Si nécessaire, vous pouvez répartir la charge de travail entre plusieurs workflows. |
|||||

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Exécuter en mode de débit élevé

Pour une définition de workflow unique, le nombre d'actions qui s'exécutent toutes les 5 minutes présente une [limite par défaut](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Pour définir la valeur par défaut sur la [valeur maximale](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) de votre workflow, qui équivaut à trois fois la valeur par défaut, vous pouvez activer le mode de débit élevé actuellement disponible en préversion. Ou, si nécessaire, vous pouvez [répartir la charge de travail entre plusieurs workflows](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling).

#### <a name="portal-multi-tenant-service"></a>[Portail (service multilocataire)](#tab/azure-portal)

1. Sur le portail Azure, dans le menu de votre application logique, sélectionnez **Paramètres**, puis **Paramètres de workflow**.

1. Sous **Options d’exécution** > **Débit élevé**, **activez** le paramètre.

   ![Capture d’écran montrant le menu d’application logique dans le portail Azure avec les options « Paramètres de workflow » et « Débit élevé » activées.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### <a name="resource-manager-template"></a>[Modèle Resource Manager](#tab/azure-resource-manager)

Pour activer ce paramètre dans un modèle ARM pour le déploiement de votre application logique, dans l’objet `properties` de la définition de ressource de votre application logique, ajoutez l’objet `runtimeConfiguration` avec la propriété `operationOptions` définie sur `OptimizedForHighThroughput` :

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Pour plus d’informations sur la définition de ressource de votre application logique, consultez [Présentation : Automatiser le déploiement pour le service Azure Logic Apps à l’aide de modèles Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

---

### <a name="integration-service-environment-ise"></a>Environnement de service d’intégration (ISE)

* [Référence SKU de l’ISE Développeur](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) : fournit jusqu’à 500 exécutions par minute, mais notez les considérations suivantes :

  * Veillez à utiliser cette référence SKU uniquement à des fins d’exploration, d’expérimentation, de développement ou de test, et non pour la production ou les tests de performances. Cette référence SKU n’a pas de contrat de niveau de service (SLA), de fonctionnalité de scale-up ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt.

  * Les mises à jour du back-end peuvent interrompre le service par intermittence.

* [Référence SKU de l’ISE Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) : le tableau suivant décrit les limites de débit de cette référence SKU, mais pour dépasser ces limites dans le cadre d’un traitement normal ou exécuter des tests de charge susceptibles d’entraîner leur dépassement, [contactez l’équipe Logic Apps](mailto://logicappsemail@microsoft.com) afin d’obtenir de l’aide.

  | Nom | Limite | Notes |
  |------|-------|-------|
  | Limite d’exécution de l’unité de base | Limitation du système quand la capacité de l’infrastructure atteint 80 % | Assure environ 4 000 exécutions d’action par minute, ce qui représente environ 160 millions d’exécutions d’action par mois |
  | Limite d’exécution de l’unité d’échelle | Limitation du système quand la capacité de l’infrastructure atteint 80 % | Chaque unité d'échelle peut assurer environ 2 000 exécutions d'action supplémentaires par minute, ce qui représente environ 80 millions d'exécutions d'action supplémentaires par mois |
  | Nombre maximum d’unités d’échelle que vous pouvez ajouter | 10 unités d'échelle | |
  ||||

<a name="gateway-limits"></a>

## <a name="data-gateway-limits"></a>Limites relatives à la passerelle de données

Azure Logic Apps prend en charge les opérations d'écriture, notamment les insertions et les mises à jour, par le biais de la passerelle de données locale. Toutefois, ces opérations ont des [limites quant à la taille de leur charge utile](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="variables-action-limits"></a>

## <a name="variables-action-limits"></a>Limites d'actions des variables

Le tableau suivant répertorie les valeurs possibles pour une définition de workflow unique :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Variables par workflow | 250 variables | 250 variables <br>(Par défaut) | 250 variables ||
| Variable - Taille maximale du contenu | 104 857 600 caractères | Workflow avec état : 104 857 600 caractères <br>(Par défaut) <p><p>Workflow sans état : 1 024 caractères <br>(Par défaut) | 104 857 600 caractères | Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Variable (type tableau) - Nombre maximal d'éléments de tableau | 100 000 éléments | 100 000 éléments <br>(Par défaut) | Référence SKU Premium : 100 000 éléments <p><p>Référence SKU Développeur : 5 000 éléments | Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
||||||

<a name="http-limits"></a>

## <a name="http-request-limits"></a>Limites de requête HTTP

Les tableaux suivants répertorient les valeurs possibles pour un appel entrant ou sortant unique :

<a name="http-timeout-limits"></a>

### <a name="timeout-duration"></a>Durée du délai d’expiration

Par défaut, l'action HTTP et les actions APIConnection suivent le [modèle d'opération asynchrone standard](/azure/architecture/patterns/async-request-reply), alors que l'action Response suit le *modèle d'opération synchrone*. Comme certaines opérations de connecteur managé passent des appels asynchrones ou écoutent des requêtes de webhook, le délai d'expiration de ces opérations peut dépasser les limites suivantes. Pour plus d'informations, consultez la [page de référence technique de chaque connecteur](/connectors/connector-reference/connector-reference-logicapps-connectors), ainsi que la documentation consacrée aux [Déclencheurs et actions de workflow](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

> [!NOTE]
> Pour le type de ressource **Application logique (Standard)** dans le service monolocataire, les workflows sans état peuvent s’exécuter uniquement de *façon synchrone*.

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Requête sortante | 120 secondes <br>(2 minutes) | 235 secondes <br>(3,9 minutes) <br>(Par défaut) | 240 secondes <br>(4 minutes) | Les appels effectués par l’action ou le déclencheur HTTP sont des exemples de demandes sortantes. <p><p>**Conseil** : pour les opérations à plus long terme, utilisez un [modèle d'interrogation asynchrone](../logic-apps/logic-apps-create-api-app.md#async-pattern) ou une [boucle « Until »](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). Pour contourner les limites liées au délai d'attente lorsque vous appelez un autre workflow qui dispose d'un [point de terminaison appelable](logic-apps-http-endpoint.md), utilisez plutôt l'action Azure Logic Apps intégrée, que vous trouverez dans le sélecteur d'opérations du concepteur, sous **Élément intégré**. <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Requête entrante | 120 secondes <br>(2 minutes) | 235 secondes <br>(3,9 minutes) <br>(Par défaut) | 240 secondes <br>(4 minutes) | Les appels reçus par le déclencheur de requête, le déclencheur Webhook HTTP et l’action Webhook HTTP sont des exemples de demandes entrantes. <p><p>**Remarque** : pour que l'appelant d'origine obtienne la réponse, toutes les étapes de la réponse doivent se terminer dans la limite, sauf si vous appelez un autre workflow imbriqué. Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques](../logic-apps/logic-apps-http-endpoint.md). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
||||||

<a name="message-size-limits"></a>

### <a name="messages"></a>Messages

| Nom | Segmentation activée | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| Téléchargement de contenu - Nombre maximal de requêtes | Oui | 1 000 requêtes | 1 000 requêtes <br>(Par défaut) | 1 000 requêtes ||
| Taille des messages | Non | 100 Mo | 100 Mo | 200 Mo | Pour contourner cette limite, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). Toutefois, certains connecteurs et API ne prennent pas en charge la segmentation ou même la limite par défaut. <p><p>- Les connecteurs tels que AS2, X12 et EDIFACT ont leur propre [limites de messages B2B](#b2b-protocol-limits). <p>- Les connecteurs ISE utilisent la limite ISE, et non les limites de connecteurs non ISE. <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Taille des messages | Oui | 1 Go | 1 073 741 824 octets <br>(1 Go) <br>(Par défaut) | 5 Go | Cette limite s’applique aux actions qui prennent en charge la segmentation en mode natif ou vous permettent d’activer la segmentation dans la configuration de leur runtime. <p><p>Si vous utilisez un ISE, le moteur Azure Logic Apps prend en charge cette limite, mais les connecteurs ont leurs propres limites de segmentation jusqu’à la limite du moteur. Pour un exemple, consultez les [Informations de référence sur l’API du connecteur Stockage Blob Azure](/connectors/azureblob/). Pour plus d’informations sur la segmentation, consultez [Gérer les messages volumineux avec la segmentation](../logic-apps/logic-apps-handle-large-messages.md). <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Taille du bloc de contenu | Oui | Varie selon le connecteur | 52 428 800 octets (52 Mo) <br>(Par défaut) | Varie selon le connecteur | Cette limite s’applique aux actions qui prennent en charge la segmentation en mode natif ou vous permettent d’activer la segmentation dans la configuration de leur runtime. <p><p>Pour modifier la valeur par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
|||||||

### <a name="character-limits"></a>Limites de caractères

| Nom | Limite | Notes |
|------|-------|-------|
| Limite d’évaluation des expressions | 131 072 caractères | Les expressions `@concat()`, `@base64()`, `@string()` ne peuvent pas contenir plus de caractères. |
| Limite de caractères de l’URL de la requête | 16 384 caractères | |
||||

<a name="retry-policy-limits"></a>

### <a name="retry-policy"></a>Stratégie de nouvelle tentative

| Nom | Limite multilocataire | Limite monolocataire | Notes |
|------|--------------------|---------------------|-------|
| Nouvelles tentatives | - Par défaut : 4 tentatives <br> - Max : 90 tentatives | - Par défaut : 4 tentatives | Pour modifier la limite par défaut dans le service multilocataire, utilisez le [paramètre de stratégie Retry](logic-apps-exception-handling.md#retry-policies). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Intervalle avant nouvelle tentative | Aucun | Par défaut : 7 secondes | Pour modifier la limite par défaut dans le service multilocataire, utilisez le [paramètre de stratégie Retry](logic-apps-exception-handling.md#retry-policies). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Délai maximal avant nouvelle tentative | Par défaut : 1 jour | Par défaut : 1 heure | Pour modifier la limite par défaut dans le service multilocataire, utilisez le [paramètre de stratégie Retry](logic-apps-exception-handling.md#retry-policies). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
| Délai minimal avant nouvelle tentative | Par défaut : 5 secondes | Par défaut : 5 secondes | Pour modifier la limite par défaut dans le service multilocataire, utilisez le [paramètre de stratégie Retry](logic-apps-exception-handling.md#retry-policies). <p><p>Pour modifier la limite par défaut dans le service monolocataire, consultez [Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire](edit-app-settings-host-settings.md). |
|||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>Limites d’authentification

Le tableau suivant répertorie les valeurs possibles pour un workflow qui démarre avec un déclencheur de requête et active [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) pour autoriser les appels entrants vers le déclencheur de requête :

| Nom | Limite | Notes |
| ---- | ----- | ----- |
| Stratégies d’autorisation Azure AD | 5 stratégies | |
| Demandes par stratégie d’autorisation | 10 revendications | |
| Valeur de revendication – Nombre maximal de caractères | 150 caractères |
||||

<a name="switch-action-limits"></a>

## <a name="switch-action-limits"></a>Limites d'actions de commutateur

Le tableau suivant répertorie les valeurs possibles pour une définition de workflow unique :

| Nom | Limite | Notes |
| ---- | ----- | ----- |
| Nombre maximal de cas par action | 25 ||
||||

<a name="inline-code-action-limits"></a>

## <a name="inline-code-action-limits"></a>Limites d'actions du code incorporé

Le tableau suivant répertorie les valeurs possibles pour une définition de workflow unique :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Nombre maximal de caractères de code | 1 024 caractères | 100 000 caractères | 1 024 caractères | Pour utiliser la limite supérieure, créez une ressource **Logic App (Standard)** , qui s’exécute dans des instances monolocataires Azure Logic Apps, [à l’aide du portail Azure](create-single-tenant-workflows-azure-portal.md), ou [de Visual Studio Code et de l’extension **Azure Logic Apps (Standard).**](create-single-tenant-workflows-visual-studio-code.md) |
| Durée maximale d'exécution du code | 5 secondes | 15 secondes | 1 024 caractères | Pour utiliser la limite supérieure, créez une ressource **Logic App (Standard)** , qui s’exécute dans des instances monolocataires Azure Logic Apps, [à l’aide du portail Azure](create-single-tenant-workflows-azure-portal.md), ou [de Visual Studio Code et de l’extension **Azure Logic Apps (Standard).**](create-single-tenant-workflows-visual-studio-code.md) |
||||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limites des connecteurs personnalisés

Dans Azure Logic Apps multilocataire et l’environnement de service d’intégration uniquement, vous pouvez créer et utiliser des [connecteurs managés personnalisés](/connectors/custom-connectors), qui sont des wrappers entourant une API REST ou une API SOAP existante. Dans Azure Logic Apps monolocataire, vous pouvez uniquement créer et utiliser des [connecteurs intégrés personnalisés](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

Le tableau suivant répertorie les valeurs possibles pour les connecteurs personnalisés :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Connecteurs personnalisés | 1 000 par abonnement Azure | Illimité | 1 000 par abonnement Azure ||
| Connecteurs personnalisés - Nombre d’API | Basé sur SOAP : 50 | Non applicable | Basé sur SOAP : 50 ||
| Requêtes par minute pour un connecteur personnalisé | 500 demandes par minute par connexion | En fonction de votre implémentation | 2 000 demandes par minute par *connecteur personnalisé* ||
| Délai de connexion | 2 minutes | Connexions inactives : <br>4 minutes <p><p>Connexion active : <br>10 min | 2 minutes ||
||||||

Pour plus d’informations, consultez la documentation suivante :

* [Vue d'ensemble des connecteurs managés personnalisés](/connectors/custom-connectors)
* [Activer la création de connecteurs intégrés - Visual Studio Code avec l’extension Azure Logic Apps (Standard)](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## <a name="managed-identity-limits"></a>Limites d’identité gérée

| Nom | Limite |
|------|-------|
| Identités gérées par application logique | Identité affectée par le système ou identité affectée par 1 utilisateur |
| Nombre d’applications logiques qui ont une identité managée dans un abonnement Azure par région | 1 000 |
|||

> [!NOTE] 
> Par défaut, l’identité managée assignée par le système d’une ressource Application logique (Standard) est automatiquement activée pour authentifier les connexions au moment de l’exécution. Cette identité diffère des informations d’identification d’authentification ou de la chaîne de connexion que vous utilisez lors de la création d’une connexion. Si vous désactivez cette identité, les connexions ne fonctionneront pas au moment de l’exécution. Pour afficher ce paramètre, dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Identité**.

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limites du compte d’intégration

Chaque abonnement Azure a les limites de compte d’intégration suivantes :

* Un compte d’intégration par région Azure [niveau Gratuit](../logic-apps/logic-apps-pricing.md#integration-accounts). Ce niveau est disponible uniquement pour les régions publiques dans Azure (par exemple, USA Ouest et Asie Sud-Est), mais pas pour [Azure China 21Vianet](/azure/china/overview-operations) ni [Azure Government](../azure-government/documentation-government-welcome.md).

* 1 000 comptes d’intégration au total, notamment des comptes d’intégration dans des [environnements de service d’intégration](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) sur les [références SKU Développeur et Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Chaque environnement de service d'intégration (ISE), qu'il s'agisse de [Développeur ou Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), peut utiliser un compte d'intégration unique sans coût supplémentaire, bien que le type de compte inclus varie selon la référence SKU de l'environnement ISE. Vous pouvez créer d'autres comptes d'intégration pour votre environnement ISE, jusqu'à la limite totale, moyennant un [coût supplémentaire](logic-apps-pricing.md#ise-pricing).

  | Référence (SKU) de l’environnement de service d’intégration | Limites du compte d’intégration |
  |---------|----------------------------|
  | **Premium** | 20 comptes au total, y compris un compte Standard sans coût supplémentaire. Avec cette référence SKU, vous ne pouvez avoir que des comptes [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts). Aucun compte Gratuit ou De base n’est autorisé. |
  | **Développeur** | 20 comptes au total, y compris un compte [Gratuit](../logic-apps/logic-apps-pricing.md#integration-accounts) (limité à 1). Avec cette référence SKU, vous pouvez avoir l’une des combinaisons suivantes : <p>- Un compte Gratuit et jusqu’à 19 comptes [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts). <br>- Aucun compte Gratuit et jusqu’à 20 comptes Standard. <p>Aucun compte De base ou autre compte gratuit n'est autorisé. <p><p>**Important !** Utilisez la [référence SKU du développeur](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) à des fins d’expérimentation, de développement et de test, mais pas pour la production ou les tests de performances. |
  |||

Pour plus d’informations sur la tarification et la facturation des environnements de service d’intégration, consultez le [modèle de tarif pour Logic Apps](../logic-apps/logic-apps-pricing.md#ise-pricing). Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limites du nombre d’artefacts par compte d’intégration

Les tableaux suivants répertorient les valeurs possibles en termes de nombre d'artefacts limités à chaque niveau de compte d'intégration. Pour connaître la tarification, consultez [Tarification Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). Pour plus d’informations sur la tarification et la facturation des comptes d’intégration, consultez [Modèle de tarification Logic apps](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Utilisez le niveau gratuit uniquement pour les scénarios exploratoires, pas pour les scénarios de production. Ce niveau limite le débit et l’utilisation et n’inclut aucun contrat de niveau de service (SLA).

| Artefact | Gratuit | De base | standard |
|----------|------|-------|----------|
| Contrats commerciaux EDI | 10 | 1 | 1 000 |
| Partenaires commerciaux EDI | 25 | 2 | 1 000 |
| Cartes | 25 | 500 | 1 000 |
| Schémas | 25 | 500 | 1 000 |
| Assemblys | 10 | 25 | 1 000 |
| Certificats | 25 | 2 | 1 000 |
| Configurations par lots | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limites de capacité des artefacts

| Artefact | Limite | Notes |
| -------- | ----- | ----- |
| Assembly | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez un [compte de stockage Azure et un conteneur d’objets blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mappage (fichier XSLT) | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez l’[API REST Azure Logic Apps – Maps](/rest/api/logic/maps/createorupdate). <p><p>**Remarque** : La quantité de données ou d’enregistrements qu’un mappage peut traiter avec succès est basée sur les limites de taille de message et de délai d’expiration d’action dans Azure Logic Apps. Par exemple, si vous utilisez une action HTTP, en fonction des [limites de taille et de délai d’attente des messages HTTP](#http-limits), un mappage peut traiter les données jusqu’à la limite de taille de message HTTP si l’opération se termine dans le délai imparti par le protocole HTTP. |
| schéma | 8 Mo | Pour charger des fichiers d’une taille supérieure à 2 Mo, utilisez un [compte de stockage Azure et un conteneur d’objets blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limites de débit

| Point de terminaison du runtime | Gratuit | De base | standard | Notes |
|------------------|------|-------|----------|-------|
| Appels de lecture par tranche de 5 minutes | 3 000 | 30,000 | 60 000 | Cette limite s’applique aux appels qui obtiennent les entrées et sorties brutes à partir de l’historique des exécutions d’une application logique. Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. |
| Appels d'invocation par tranche de 5 minutes | 3 000 | 30,000 | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. |
| Appels de suivi par tranche de 5 minutes | 3 000 | 30,000 | 45,000 | Vous pouvez répartir la charge de travail entre plusieurs comptes si nécessaire. |
| appels simultanés de blocage | ~1,000 | ~1,000 | ~1,000 | Identique pour toutes les références (SKU). Vous pouvez diminuer le nombre de requêtes simultanées ou réduire la durée si nécessaire. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Taille des messages de protocole B2B (AS2, X12, EDIFACT)

Le tableau suivant répertorie les limites de taille de message qui s'appliquent aux protocoles B2B :

| Nom | Multi-locataire | Monolocataire | Environnement de service d’intégration (ISE) | Notes |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100 Mo<br>v1 - 25 Mo | Non disponible | v2 - 200 Mo <br>v1 - 25 Mo | S’applique au décodage et à l’encodage. |
| X 12 | 50 Mo | Non disponible | 50 Mo | S’applique au décodage et à l’encodage. |
| EDIFACT | 50 Mo | Non disponible | 50 Mo | S’applique au décodage et à l’encodage. |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Configuration du pare-feu : Adresses IP et balises de service

Si votre environnement a des exigences réseau strictes ou si un pare-feu qui limite le trafic à destination d’adresses IP spécifiques, votre environnement ou pare-feu doit *à la fois* autoriser l’accès aux adresses IP [entrantes](#inbound) et [sortantes](#outbound) utilisées par le service ou le runtime Azure Logic Apps dans la région Azure où se trouve votre ressource d’application logique. Pour configurer cet accès, vous pouvez créer des [règles de Pare-feu Azure](../firewall/rule-processing.md). *Toutes* les applications logiques qui se trouvent dans une même région utilisent les mêmes plages d’adresses IP.

> [!NOTE]
> Si vous utilisez [Power Automate](/power-automate/getting-started), certaines actions telles que **HTTP** et **HTTP + OpenAPI** passent directement par le service Azure Logic Apps et proviennent des adresses IP listées ici. Pour plus d’informations sur les adresses IP utilisées par Power Automate, consultez [Limites et configuration pour Power Automate](/power-automate/limits-and-config#ip-address-configuration).

Par exemple, supposons que vos applications logiques sont déployées dans la région USA Ouest. Pour prendre en charge les appels que vos applications logiques envoient ou reçoivent via des déclencheurs et actions intégrés, tels que le [déclencheur ou l’action HTTP](../connectors/connectors-native-http.md), votre pare-feu doit autoriser l’accès pour *toutes* les adresses IP entrantes du service Azure Logic Apps *et* les adresses IP sortantes qui existent dans la région USA Ouest.

Si votre workflow utilise des [connecteurs managés](../connectors/managed.md), comme le connecteur Office 365 Outlook ou le connecteur SQL, ou qu’il utilise des [connecteurs personnalisés](/connectors/custom-connectors/), le pare-feu doit également autoriser l’accès pour *toutes* les [adresses IP sortantes de connecteur managé](/connectors/common/outbound-ip-addresses) dans la région Azure de votre application logique. Si votre workflow utilise des connecteurs personnalisés qui accèdent à des ressources locales via la [ressource de passerelle de données locale dans Azure](logic-apps-gateway-connection.md), vous devez configurer l’installation de la passerelle pour autoriser l’accès aux [*adresses IP sortantes* de connecteurs managés](/connectors/common/outbound-ip-addresses). Pour plus d’informations sur la configuration des paramètres de communication sur la passerelle, consultez les rubriques suivantes :

* [Ajuster les paramètres de communication pour la passerelle de données locale](/data-integration/gateway/service-gateway-communication)
* [Configurer les paramètres de proxy pour la passerelle de données locale](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> Si vous utilisez [Microsoft Azure opéré par 21Vianet](/azure/china/), les connecteurs managés et les connecteurs personnalisés n’ont pas d’adresse IP réservées ou fixes. Par conséquent, vous ne pouvez pas configurer des règles de pare-feu pour les applications logiques qui utilisent ces connecteurs dans ce cloud. Pour les adresses IP du service Azure Logic Apps, passez en revue la [version de la documentation d’Azure opéré par 21Vianet](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration).

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>Considérations importantes relatives à la configuration de l’adresse IP du pare-feu

Avant de configurer votre pare-feu avec des adresses IP, passez en revue les considérations suivantes :

* Si vos workflows d’application logique s’exécutent dans Azure Logic Apps monolocataire, vous devez rechercher les noms de domaine complets (FQDN) pour vos connexions. Pour plus d’informations, consultez les sections correspondantes dans les rubriques suivantes :

  * [Autorisations de pare-feu pour les applications logiques monolocataires - Portail Azure](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [Autorisations de pare-feu pour les applications logiques monolocataires - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* Si vos workflows d’application logique s’exécutent dans un [environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), assurez-vous que vous [ouvrez ces ports également](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Pour vous aider à simplifier les règles de sécurité que vous souhaitez créer, vous pouvez éventuellement utiliser les [étiquettes de service](../virtual-network/service-tags-overview.md) plutôt que de spécifier des préfixes d’adresses IP pour chaque région. Ces balises fonctionnent dans les régions où le service Logic Apps est disponible :

  * **LogicAppsManagement** : représente les préfixes d’adresses IP entrantes pour le service Logic Apps.

  * **LogicApps** : représente les préfixes d’adresse IP sortantes pour le service Logic Apps.

  * **AzureConnectors** : Représente les préfixes d’adresses IP utilisées pour les connecteurs managés qui effectuent des rappels de Webhook entrants vers le service Logic Apps et des appels sortants vers leurs services respectifs, tels que Stockage Azure ou Azure Event Hubs.

* Si vos applications logiques rencontrent des problèmes d’accès aux comptes Stockage Azure qui utilisent des [pare-feux et des règles de pare-feu](../storage/common/storage-network-security.md), vous disposez de [différentes options alternatives pour activer l’accès](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Par exemple, les applications logiques n’ont pas directement accès aux comptes de stockage qui utilisent des règles de pare-feu et se trouvent dans la même région. Toutefois, si vous autorisez les [adresses IP sortantes pour les connecteurs managés dans votre région](/connectors/common/outbound-ip-addresses), vos applications logiques peuvent accéder à des comptes de stockage dans une autre région, sauf lorsque vous utilisez le connecteur de Stockage Table Azure ou des connecteurs de Stockage File d’attente Azure. Pour accéder à votre Stockage Table ou Stockage File d’attente, vous pouvez toujours utiliser le déclencheur et les actions HTTP à la place. Pour d’autres options, voir [Accéder à des comptes de stockage derrière des pare-feu](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Adresses IP entrantes

Cette section répertorie les adresses IP entrantes pour le service Azure Logic Apps uniquement. Si vous utilisez Azure Government, consultez [Azure Government - Adresses IP entrantes](#azure-government-inbound).

> [!TIP]
> Pour réduire la complexité de la création des règles de sécurité, vous pouvez éventuellement utiliser la [balise de service](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, au lieu de spécifier les préfixes d’adresses IP Logic Apps entrants pour chaque région.
>
> Certains connecteurs managés effectuent des rappels de webhook entrants au service Azure Logic Apps. Pour ces connecteurs managés, vous pouvez éventuellement utiliser la balise de service **AzureConnectors**, plutôt que de spécifier des préfixes d’adresse IP entrants de connecteur managé pour chaque région. 
> Ces balises fonctionnent dans les régions où le service Logic Apps est disponible.
>
> Les connecteurs suivants effectuent des rappels de webhook entrants au service Logic Apps :
>
> Adobe Creative Cloud, Adobe Sign, version de démonstration Adobe Sign, Adobe Sign préversion, Adobe Sign Stage, Microsoft Sentinel, Business Central, Calendly, Common Data Service, DocuSign, version de démonstration DocuSign, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts pour Microsoft Teams, Teamwork Projects, Typeform
>
> \* **SAP** : L’appelant en retour varie selon que l’environnement de déploiement est multilocataire Azure ou ISE. Dans l’environnement multi-locataire, la passerelle de données locale effectue le rappel au service Logic Apps. Dans un environnement ISE, le connecteur SAP effectue le rappel au service Logic Apps.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant--single-tenant---inbound-ip-addresses"></a>Mutualisé et monolocataire - Adresses IP entrantes

| Région | IP |
|--------|----|
| Australie Est | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161, 13.70.78.192 – 13.70.78.223 |
| Sud-Australie Est | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brésil Sud | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198, 191.233.207.32 – 191.233.207.63 |
| Brésil Sud-Est | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Centre du Canada | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105, 20.38.149.160 – 20.38.149.191 |
| Est du Canada | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Inde centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA Centre | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39, 52.182.141.160 – 52.182.141.191 |
| Asie Est | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA Est | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165, 20.42.72.160 – 20.42.72.191 |
| USA Est 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253, 20.44.17.224 – 20.44.17.255 |
| France Centre | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203, 40.79.139.160 – 40.79.139.191 |
| France Sud | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Allemagne Nord | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Allemagne Centre-Ouest | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0, 51.116.243.224 – 51.116.243.255 |
| Japon Est | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164, 13.78.111.160 – 13.78.111.191 |
| OuJapon Est | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Inde Ouest Jio | 20.193.206.48,20.193.206.49,20.193.206.50,20.193.206.51 |
| Centre de la Corée | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Corée du Sud | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Centre-Nord des États-Unis | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europe Nord | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39, 13.69.231.160 – 13.69.231.191 |
| Norvège Est | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| Afrique du Sud Nord | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Afrique du Sud Ouest | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| États-Unis - partie centrale méridionale | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132, 13.73.244.160 – 13.73.244.191 |
| Inde Sud | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asie Sud-Est | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6, 13.67.13.224 – 13.67.13.255 |
| Suisse Nord | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Suisse Ouest | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| Émirats arabes unis Centre | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Émirats arabes unis Nord | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| Sud du Royaume-Uni | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81, 51.105.69.96 – 51.105.69.127 |
| Ouest du Royaume-Uni | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Centre-USA Ouest | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europe Ouest | 13.95.155.53, 52.174.54.218, 52.174.49.6, 13.69.71.160 – 13.69.71.191 |
| Inde Ouest | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA Ouest | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA Ouest 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68, 40.78.245.160 – 40.78.245.191 |
| USA Ouest 3| 20.150.172.240, 20.150.172.242, 20.150.172.243, 20.150.172.241 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government - Adresses IP entrantes

| Région Azure Government | IP |
|-------------------------|----|
| Gouvernement des États-Unis – Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| Gouvernement des États-Unis – Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| Gouvernement américain - Virginie | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| Centre des États-Unis – US DoD | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Adresses IP sortantes

Cette section répertorie les adresses IP sortantes pour le service Azure Logic Apps. Si vous avez Azure Government, consultez [Azure Government - Adresses IP sortantes](#azure-government-outbound). Si votre workflow utilise des [connecteurs managés](../connectors/managed.md), comme le connecteur Office 365 Outlook ou le connecteur SQL, ou qu’il utilise des [connecteurs personnalisés](/connectors/custom-connectors/), le pare-feu doit également autoriser l’accès pour *toutes* les [adresses IP sortantes de connecteur managé](/connectors/common/outbound-ip-addresses) dans la région Azure de votre application logique. Si votre workflow utilise des connecteurs personnalisés qui accèdent à des ressources locales via la [ressource de passerelle de données locale dans Azure](logic-apps-gateway-connection.md), vous devez configurer l’installation de la passerelle pour autoriser l’accès aux [*adresses IP sortantes* de connecteurs managés](/connectors/common/outbound-ip-addresses). Pour plus d’informations sur la configuration des paramètres de communication sur la passerelle, consultez les rubriques suivantes :

* [Ajuster les paramètres de communication pour la passerelle de données locale](/data-integration/gateway/service-gateway-communication)
* [Configurer les paramètres de proxy pour la passerelle de données locale](/data-integration/gateway/service-gateway-proxy)

> [!TIP]
> Pour réduire la complexité de la création des règles de sécurité, vous pouvez éventuellement utiliser la [balise de service](../virtual-network/service-tags-overview.md), **LogicApps**, au lieu de spécifier les préfixes d’adresses IP Logic Apps sortants pour chaque région. Vous pouvez éventuellement utiliser l’étiquette de service **AzureConnectors** pour les connecteurs managés qui effectuent des appels sortants vers leurs services respectifs, tels que Stockage Azure ou Azure Event Hubs, plutôt que de spécifier des préfixes d’adresse IP sortante de connecteur managé pour chaque région. Ces balises fonctionnent dans les régions où le service Logic Apps est disponible.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant--single-tenant---outbound-ip-addresses"></a>Mutualisé et monolocataire - Adresses IP sortantes

| Région | Adresse IP Logic Apps |
|--------|---------------|
| Australie Est | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139, 13.70.78.192 – 13.70.78.223 |
| Sud-Australie Est | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 |
| Brésil Sud | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131, 191.233.207.32 – 191.233.207.63 |
| Brésil Sud-Est | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 |
| Centre du Canada | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150, 20.38.149.160 – 20.38.149.191 |
| Est du Canada | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 |
| Inde centrale | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 |
| USA Centre | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16, 52.182.141.160 – 52.182.141.191 |
| Asie Est | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 |
| USA Est | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208, 20.42.72.160 – 20.42.72.191 |
| USA Est 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236, 20.44.17.224 – 20.44.17.255 |
| France Centre | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104, 40.79.139.160 – 40.79.139.191 |
| France Sud | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 |
| Allemagne Nord | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 |
| Allemagne Centre-Ouest | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51, 51.116.243.224 – 51.116.243.255 |
| Japon Est | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232, 13.78.111.160 – 13.78.111.191 |
| OuJapon Est | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 |
| Inde Ouest Jio | 20.193.206.128, 20.193.206.129, 20.193.206.130, 20.193.206.131, 20.193.206.132, 20.193.206.133, 20.193.206.134, 20.193.206.135 |
| Centre de la Corée | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 |
| Corée du Sud | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 |
| Centre-Nord des États-Unis | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 |
| Europe Nord | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181, 13.69.231.160 – 13.69.231.191 |
| Norvège Est | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 |
| Afrique du Sud Nord | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 |
| Afrique du Sud Ouest | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 |
| États-Unis - partie centrale méridionale | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225, 13.73.244.160 - 13.73.244.191 |
| Inde Sud | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 |
| Asie Sud-Est | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128, 13.67.13.224 – 13.67.13.255 |
| Suisse Nord | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 |
| Suisse Ouest | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 |
| Émirats arabes unis Centre | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 |
| Émirats arabes unis Nord | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 |
| Sud du Royaume-Uni | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24, 51.105.69.96 – 51.105.69.127 |
| Ouest du Royaume-Uni | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 |
| Centre-USA Ouest | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75, 13.71.199.128 - 13.71.199.159 |
| Europe Ouest | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167, 13.69.71.160 – 13.69.71.191 |
| Inde Ouest | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 |
| USA Ouest | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 |
| USA Ouest 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219, 40.78.245.160 – 40.78.245.191 |
| USA Ouest 3 | 20.150.181.32, 20.150.181.33, 20.150.181.34, 20.150.181.35, 20.150.181.36, 20.150.181.37, 20.150.181.38, 20.150.173.192 |
|||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government - Adresses IP sortantes

| Région | Adresse IP Logic Apps |
|--------|---------------|
| Centre des États-Unis – US DoD | 52.182.48.215, 52.182.92.143 |
| Gouvernement des États-Unis – Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 |
| Gouvernement des États-Unis – Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 |
| Gouvernement américain - Virginie | 13.72.54.205, 52.227.138.30, 52.227.152.44 |
|||

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* En savoir plus sur [les exemples et les scénarios courants](../logic-apps/logic-apps-examples-and-scenarios.md)
