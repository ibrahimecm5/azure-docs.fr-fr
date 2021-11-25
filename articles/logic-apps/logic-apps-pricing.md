---
title: Mesure de l’utilisation, facturation et tarification
description: Découvrez comment les modèles de mesure de l’utilisation, de facturation et de tarification fonctionnent dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/10/2021
ms.openlocfilehash: 90e0703313db99bee1a7d06e9c1bc9d0efac135f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372963"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>Modèles de mesure de l’utilisation, de facturation et de tarification pour Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vous aide à créer et à exécuter des flux de travail d’intégration automatisés et pouvant être mis à l’échelle dans le cloud. Cet article décrit le fonctionnement des modèles de mesure, de facturation et de tarification pour Azure Logic Apps et les ressources associées. Pour des informations telles que les tarifs spécifiques, la planification des coûts ou les différents environnements d’hébergement, consultez le contenu suivant :

* [Tarifs d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)
* [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md)
* [Architecture monolocataire ou multilocataire et environnement de service d’intégration](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>Consommation (multilocataire)

Dans Azure Logic Apps multilocataire, une application logique et son flux de travail suivent le plan [**Consommation**](https://azure.microsoft.com/pricing/details/logic-apps) pour la tarification et la facturation. Vous créez de telles applications logiques de différentes manières, par exemple, lorsque vous choisissez le type de ressource **Logic App (Consommation)** , que vous utilisez l’extension **Azure Logic Apps (Consommation)** dans Visual Studio Code, ou que vous créez des [tâches d’automatisation](create-automation-tasks-azure-resources.md).

Le tableau suivant résume la façon dont le modèle Consommation gère la mesure et la facturation des composants suivants lorsqu’il est utilisé avec une application logique et un flux de travail dans Azure Logic Apps multilocataire :

| Composant | Métriques et facturation |
| ----------|----------------------|
| Opérations de déclenchement et d’action | Le modèle Consommation comprend un *nombre initial* d’opérations intégrées gratuites, par abonnement Azure, qu’un flux de travail peut exécuter. Au-delà de ce nombre, la mesure s’applique à *chaque exécution* et la facturation suit la [tarification *Actions* pour le plan Consommation](https://azure.microsoft.com/pricing/details/logic-apps). Pour les autres types d’opérations, tels que les connecteurs managés, la facturation suit la [tarification du connecteur *Standard* ou *Entreprise* pour le plan Consommation](https://azure.microsoft.com/pricing/details/logic-apps). Pour plus d’informations, consultez la section [Opérations de déclenchement et d’action dans le modèle Consommation](#consumption-operations). |
| Opérations de stockage | La mesure s’applique *uniquement à la consommation de stockage liée à la conservation des données*, telle que la sauvegarde des entrées et des sorties de l’historique des exécutions de votre flux de travail. La facturation suit la [tarification de la conservation des données pour le plan Consommation](https://azure.microsoft.com/pricing/details/logic-apps/). Pour plus d’informations, consultez [Opérations de stockage](#storage-operations). |
| Comptes d’intégration | La mesure s’applique en fonction du type de compte d’intégration que vous créez et utilisez avec votre application logique. La facturation suit la [tarification *Compte d’intégration*](https://azure.microsoft.com/pricing/details/logic-apps/), sauf si votre application logique est déployée et hébergée dans un [environnement de service d’intégration (ISE)](#ise-pricing). Pour plus d’informations, consultez [Comptes d’intégration](#integration-accounts). |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>Opérations de déclenchement et d’action dans le modèle Consommation

À l’exception du nombre initial d’exécutions gratuites d’opérations intégrées, par abonnement Azure, qu’un flux de travail peut exécuter, le modèle Consommation mesure et facture une opération en fonction de *chaque exécution*, que le flux de travail global s’exécute correctement, se termine ou soit même instancié. Une opération n’est généralement exécutée qu’une seule fois, [à moins que les nouvelles tentatives ne soient activées](#other-operation-behavior). De même, une exécution effectue généralement un seul appel, [sauf si l’opération prend en charge et active la segmentation ou la pagination pour obtenir de grandes quantités de données](logic-apps-handle-large-messages.md). Si la segmentation ou la pagination est activée, l’exécution d’une opération peut nécessiter plusieurs appels. Le modèle Consommation mesure et facture une opération *par exécution, et non par appel*.

Supposons, par exemple, qu’un flux de travail commence par un déclencheur d’interrogation qui obtient des enregistrements en effectuant régulièrement des appels sortants vers un point de terminaison. L’appel sortant est mesuré et facturé comme une seule exécution, que le déclencheur soit activé ou ignoré, par exemple lorsqu’un déclencheur vérifie un point de terminaison, mais ne trouve pas de données ou d’événements. L’état du déclencheur contrôle la création et l’exécution ou non de l’instance de flux de travail. Supposons à présent que l’opération prend également en charge et a activé la segmentation ou la pagination. Si l’opération doit effectuer 10 appels pour finir d’obtenir toutes les données, l’opération est toujours mesurée et facturée comme une *seule exécution*, même si elle passe plusieurs appels.

Le tableau suivant résume la façon dont le modèle Consommation gère la mesure et la facturation de ces types d’opérations lorsqu’il est utilisé avec une application logique et un flux de travail dans Azure Logic Apps multilocataire :

| Type d'opération | Description | Métriques et facturation |
|----------------|-------------|----------------------|
| [*Intégré*](../connectors/built-in.md) | Ces opérations s’exécutent directement et en mode natif avec le runtime Azure Logic Apps. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Intégré**. <p>Par exemple, le déclencheur HTTP et le déclencheur Requête sont des déclencheurs intégrés. L’action HTTP et l’action Réponse sont des actions intégrées. Les autres opérations intégrées comprennent les actions de contrôle du flux de travail, telles que les boucles et les conditions, les opérations de données, les opérations de traitement par lot, etc. | Le modèle Consommation comprend un *nombre initial d’opérations intégrées gratuites*, par abonnement Azure, qu’un flux de travail peut exécuter. Au-delà de ce nombre, les exécutions d’opérations intégrées suivent la [tarification *Actions*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Remarque** : Certaines opérations de connecteur managé sont *également* disponibles en tant qu’opérations intégrées, qui sont incluses dans les opérations initiales gratuites. Au-delà des opérations gratuites initiales, la facturation suit la [tarification *Actions*](https://azure.microsoft.com/pricing/details/logic-apps/), et non [celle du connecteur *Standard* ou *Entreprise*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Connecteur managé*](../connectors/managed.md) | Ces opérations s’exécutent séparément dans Azure. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Standard** ou **Entreprise**. | Les exécutions de ces opérations suivent la [tarification du connecteur *Standard* ou *Entreprise*](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Remarque** : Les exécutions d’opérations du connecteur Entreprise en préversion suivent la [tarification du connecteur Consommation *Standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Connecteur personnalisé*](../connectors/apis-list.md#custom-apis-and-connectors) | Ces opérations s’exécutent séparément dans Azure. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Personnalisé**. Pour connaître les limites de nombre de connecteurs, de débit et de délai d’attente, consultez [Limites des connecteurs personnalisés dans Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). | Les exécutions de ces opérations suivent la [tarification du connecteur *Standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
||||

Pour plus d’informations sur le fonctionnement du modèle Consommation avec les opérations qui s’exécutent à l’intérieur d’autres opérations, comme les boucles, le traitement de plusieurs éléments (p. ex. : les tableaux) et les stratégies de nouvelles tentatives, consultez [Comportement des autres opérations](#other-operation-behavior).

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>Conseils pour l’estimation des coûts du modèle Consommation

Pour vous aider à estimer plus précisément les coûts de consommation, consultez les conseils suivants :

* Ne basez pas vos calculs juste sur l’intervalle d’interrogation, mais prenez plutôt en compte le nombre possible de messages ou d’événements qui pourraient arriver chaque jour.

* Lorsqu’un événement ou un message répond aux critères de déclencheurs, plusieurs déclencheurs essaient immédiatement de lire les autres événements ou messages en attente qui répondent aux critères. Ce comportement signifie que le déclencheur se lance selon le nombre d’événements ou de messages en attente qualifiés pour le démarrage de workflows, même si vous sélectionnez un intervalle d’interrogation plus étendue. Les déclencheurs qui adoptent ce comportement incluent Azure Service Bus et Azure Event Hub.

  Par exemple, imaginons que vous configuriez un déclencheur qui vérifie un point de terminaison par jour. Lorsqu’il vérifie le point de terminaison et trouve 15 événements qui répondent aux critères, il se lance et exécute le workflow correspondant 15 fois. Le service Logic Apps mesure toutes les actions réalisées par ces 15 workflows, dont les requêtes du déclencheur.

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>Standard (monolocataire)

Dans Azure Logic Apps monolocataire, une application logique et ses flux de travail suivent le plan [**Standard**](https://azure.microsoft.com/pricing/details/logic-apps/) pour la tarification et la facturation. Vous créez de telles applications logiques de différentes manières, par exemple, lorsque vous choisissez le type de ressource **Logic App (Standard)** ou que vous utilisez l’extension **Azure Logic Apps (Standard)** dans Visual Studio Code. Ce modèle tarifaire exige que les applications logiques utilisent un plan d’hébergement et un niveau tarifaire, ce qui diffère du plan Consommation en ce sens que la capacité de réserve et les ressources dédiées vous sont facturées, que vous les utilisiez ou non.

Lorsque vous créez ou déployez des applications logiques avec le type de ressource **Logic App (Standard)** , vous pouvez utiliser le plan d’hébergement Workflow standard dans toutes les régions Azure. Vous avez également la possibilité de sélectionner une ressource **App Service Environment v3** existante comme emplacement de déploiement, mais vous ne pouvez utiliser que le plan App Service avec cette option. Si vous choisissez cette option, vous êtes facturé pour les instances utilisées par le plan App Service et pour l’exécution des flux de travail de vos applications logiques. Aucuns autres frais ne s’appliquent.

> [!IMPORTANT]
> Les plans et ressources suivants ne sont plus disponibles ni pris en charge avec la version publique du type de ressource **Application logique (Standard)** dans les régions Azure : plan Functions Premium, App Service Environment v1 et App Service Environment v2. À l’exception d’ASEv3, le plan App Service n’est ni disponible ni pris en charge.

Le tableau suivant résume la façon dont le modèle Standard gère la mesure et la facturation des composants suivants lorsqu’il est utilisé avec une application logique et un flux de travail dans Azure Logic Apps monolocataire :

| Composant | Métriques et facturation |
|-----------|----------------------|
| Processeur virtuel et mémoire | Le modèle Standard *nécessite* que votre application logique utilise le plan d’hébergement **Flux de travail Standard** et un niveau tarifaire, lequel détermine les niveaux de ressources et les tarifs qui s’appliquent à la capacité de calcul et de mémoire. Pour plus d’informations, consultez [Niveaux tarifaires dans le modèle Standard](#standard-pricing-tiers). |
| Opérations de déclenchement et d’action | Le modèle Standard comprend un *nombre illimité* d’opérations intégrées gratuites que votre flux de travail peut exécuter. <p>Si votre flux de travail utilise des opérations de connecteur managé, la mesure de ces opérations s’applique à *chaque appel*, tandis que la facturation suit [la même tarification de connecteur *Standard* ou *Entreprise* que le plan Consommation](https://azure.microsoft.com/pricing/details/logic-apps). Pour plus d’informations, consultez la section [Opérations de déclenchement et d’action dans le modèle Standard](#standard-operations). |
| Opérations de stockage | Le contrôle s’applique à toutes les opérations de stockage exécutées par Azure Logic Apps. Par exemple, les opérations de stockage s’exécutent lorsque le service enregistre les entrées et les sorties à partir de l’historique des exécutions de votre flux de travail. La facturation suit le [niveau tarifaire](#standard-pricing-tiers) choisi. Pour plus d’informations, consultez [Opérations de stockage](#storage-operations). |
| Comptes d’intégration | Si vous créez un compte d’intégration à utiliser par votre application logique, la mesure est basée sur le type de compte d’intégration que vous créez. La facturation suit la [tarification *Compte d’intégration*](https://azure.microsoft.com/pricing/details/logic-apps/). Pour plus d’informations, consultez [Comptes d’intégration](#integration-accounts). |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>Niveaux tarifaires dans le modèle Standard

Le niveau tarifaire que vous choisissez pour mesurer et facturer votre application logique comprend des quantités spécifiques de calcul dans les ressources de processeur virtuel et de mémoire. Actuellement, seul le plan d’hébergement **Flux de travail Standard** est disponible pour le type de ressource **Logic App (Standard)** et offre les niveaux tarifaires suivants :

| Niveau tarifaire | Processeur virtuel | Mémoire (Go) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3,5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> L’exemple suivant est fourni à titre d’illustration uniquement et présente des estimations types pour illustrer le fonctionnement général d’un niveau tarifaire. 
> Pour connaître la tarification spécifique des processeurs virtuels et de la mémoire en fonction des régions où Azure Logic Apps est disponible, consultez le [plan Standard pour une région particulière sur la page de tarification d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).
>
> Supposons que dans une région d’exemple, les ressources suivantes ont ces tarifs horaires :
>
> | Ressource | Tarif horaire (exemple de région) |
> |----------|-----------------------------|
> | **Processeurs virtuels** | 0,192 USD par processeur virtuel |
> | **Mémoire** | 0,0137 USD par Go |
> |||
>
> Le calcul suivant fournit une estimation du tarif mensuel :
>
> <*tarif-mensuel*> = 730 heures (par mois) * [(<*nombre-processeur-virtuel*> * <*tarif-horaire-processeur-virtuel*>) + (<*nombre-Go-mémoire*> * <*tarif-horaire-Go-mémoire*>)]
>
> Sur la base des informations précédentes, le tableau suivant présente les tarifs mensuels estimés pour chaque niveau tarifaire et les ressources de ce niveau tarifaire :
>
> | Niveau tarifaire | Processeur virtuel | Mémoire (Go) | Tarif mensuel (exemple de région) |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3,5 | 175,16 USD |
> | **WS2** | 2 | 7 | 350,33 USD |
> | **WS3** | 4 | 14 | 700,65 USD |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>Opérations de déclenchement et d’action dans le modèle Standard

À l’exception des opérations intégrées gratuites illimitées qu’un flux de travail peut exécuter, le modèle Standard mesure et facture une opération en fonction de *chaque appel*, que le flux de travail global s’exécute correctement, se termine ou soit même instancié. Une opération n’est généralement exécutée qu’une seule fois, [à moins que les nouvelles tentatives ne soient activées](#other-operation-behavior). De même, une exécution effectue généralement un seul appel, [sauf si l’opération prend en charge et active la segmentation ou la pagination pour obtenir de grandes quantités de données](logic-apps-handle-large-messages.md). Si la segmentation ou la pagination est activée, l’exécution d’une opération peut nécessiter plusieurs appels. Le modèle Standard mesure et facture une opération *par appel, et non par exécution*.

Supposons, par exemple, qu’un flux de travail commence par un déclencheur d’interrogation qui obtient des enregistrements en effectuant régulièrement des appels sortants vers un point de terminaison. L’appel sortant est mesuré et facturé, que le déclencheur se déclenche ou non, ou qu’il soit ignoré. L’état du déclencheur contrôle la création et l’exécution ou non de l’instance de flux de travail. Supposons à présent que l’opération prend également en charge et a activé la segmentation ou la pagination. Si l’opération doit effectuer 10 appels pour finir d’obtenir toutes les données, l’opération est mesurée et facturée *par appel*.

Le tableau suivant résume la façon dont le modèle Standard gère la mesure et la facturation des types d’opérations lorsqu’il est utilisé avec une application logique et un flux de travail dans Azure Logic Apps monolocataire :

| Type d'opération | Description | Métriques et facturation |
|----------------|-------------|----------------------|
| [*Intégré*](../connectors/built-in.md) | Ces opérations s’exécutent directement et en mode natif avec le runtime Azure Logic Apps. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Intégré**. <p>Par exemple, le déclencheur HTTP et le déclencheur Requête sont des déclencheurs intégrés. L’action HTTP et l’action Réponse sont des actions intégrées. Les autres opérations intégrées comprennent les actions de contrôle du flux de travail, telles que les boucles et les conditions, les opérations de données, les opérations de traitement par lot, etc. | Le modèle Standard comprend un nombre illimité d’opérations intégrées gratuites. <p><p>**Remarque** : Certaines opérations de connecteur managé sont *également* disponibles en tant qu’opérations intégrées. Bien que les opérations intégrées soient gratuites, le modèle Standard continue de mesurer et de facturer les opérations de connecteur managé en utilisant [la même tarification de connecteur *Standard* ou *Entreprise* que le modèle Consommation](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Connecteur managé*](../connectors/managed.md) | Ces opérations s’exécutent séparément dans Azure. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Azure** combinée. | Le modèle Standard mesure et facture les opérations de connecteur managé selon [la même tarification de connecteur *Standard* et *Entreprise* que le modèle Consommation](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Remarque** : Les opérations du connecteur Entreprise en préversion suivent la [tarification du connecteur Consommation *Standard*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [*Connecteur personnalisé*](../connectors/apis-list.md#custom-apis-and-connectors) | Actuellement, vous pouvez créer et utiliser uniquement des [opérations de connecteur intégrées personnalisées](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) dans les flux de travail d’applications logiques monolocataires. | Le modèle Standard comprend un nombre illimité d’opérations intégrées gratuites. Pour connaître les limites de débit et de délai d’attente, consultez [Limites des connecteurs personnalisés dans Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). |
||||

Pour plus d’informations sur le fonctionnement du modèle Standard avec les opérations qui s’exécutent à l’intérieur d’autres opérations, comme les boucles, le traitement de plusieurs éléments (p. ex. : les tableaux) et les stratégies de nouvelles tentatives, consultez [Comportement des autres opérations](#other-operation-behavior).

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>Environnement de service d’intégration (ISE)

Lorsque vous créez une application logique à l’aide du type de ressource **Application logique (Consommation)** et que vous la déployez dans un [*environnement de service d’intégration* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) dédié, l’application logique et son flux de travail suivent le [plan Environnement de service d’intégration](https://azure.microsoft.com/pricing/details/logic-apps) pour la tarification et la facturation. Ce modèle de tarification dépend du [niveau ou du *SKU* de votre ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) et diffère du plan Consommation en ce sens que la capacité de réserve et les ressources dédiées vous sont facturées, que vous les utilisiez ou non.

Le tableau suivant résume la manière dont le modèle ISE gère la mesure et la facturation de la capacité de réserve et des autres ressources dédiées en fonction de votre niveau ou SKU ISE :

| Référence (SKU) de l’environnement de service d’intégration | Métriques et facturation |
|---------|----------------------|
| **Premium** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Premium](https://azure.microsoft.com/pricing/details/logic-apps). Si vous avez besoin d’un débit plus élevé, vous pouvez [ajouter des unités d’échelle supplémentaires](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) pendant ou après la création de votre ISE. Chaque unité d’échelle est facturée à un [tarif horaire qui correspond à peu près à la moitié du tarif de l’unité de base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Développeur** | L’unité de base a une [capacité fixe](logic-apps-limits-and-config.md#integration-service-environment-ise) et est [facturée à un tarif horaire pour le niveau tarifaire Développeur](https://azure.microsoft.com/pricing/details/logic-apps). Toutefois, cette référence SKU n’a pas de contrat de niveau de service (SLA), de fonctionnalité de scale-up ou de redondance pendant le recyclage, ce qui signifie que vous pouvez rencontrer des retards ou des temps d’arrêt. Les mises à jour du back-end peuvent interrompre le service par intermittence. <p><p>**Important !** Veillez à utiliser cette référence SKU uniquement à des fins d’exploration, d’expérimentation, de développement ou de test, et non pour la production ou les tests de performances. <p><p>Pour plus d’informations sur la capacité et les limites, consultez [Limites ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

Le tableau suivant résume la façon dont le modèle ISE gère les composants suivants lorsqu’il est utilisé avec une application logique et un flux de travail dans un ISE :

| Composant | Description |
|-----------|-------------|
| Opérations de déclenchement et d’action | Le modèle ISE comprend des opérations intégrées gratuites de connecteur managé et de connecteur personnalisé que votre flux de travail peut exécuter, mais sous réserve des [limites de l’ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise) et des [limites du connecteur personnalisé dans Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). Pour plus d’informations, consultez la section [Opérations de déclenchement et d’action dans le modèle ISE](#integration-service-environment-operations). |
| Opérations de stockage | Le modèle ISE inclut la consommation du stockage libre, telle que la conservation des données. Pour plus d’informations, consultez [Opérations de stockage](#storage-operations). |
| Comptes d’intégration | Le modèle ISE comprend un seul niveau de compte d’intégration gratuit, selon le SKU ISE que vous avez sélectionné. Pour un [coût supplémentaire](https://azure.microsoft.com/pricing/details/logic-apps/), vous pouvez créer davantage de comptes d’intégration pour votre ISE à utiliser jusqu’à la [limite totale de l’ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Pour plus d’informations, consultez [Comptes d’intégration](#integration-accounts). |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>Opérations de déclenchement et d’action dans le modèle ISE

Le tableau suivant résume la façon dont le modèle ISE gère les types d’opérations suivants lorsqu’il est utilisé avec une application logique et un flux de travail dans un ISE :

| Type d'opération | Description | Métriques et facturation |
|----------------|-------------|----------------------|
| [*Intégré*](../connectors/built-in.md) | Ces opérations s’exécutent directement et en mode natif avec le runtime Azure Logic Apps et dans le même ISE que le flux de travail de votre application logique. Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Intégré**, mais chaque opération affiche également l’étiquette **CORE**. <p>Par exemple, le déclencheur HTTP et le déclencheur Requête sont des déclencheurs intégrés. L’action HTTP et l’action Réponse sont des actions intégrées. Les autres opérations intégrées comprennent les actions de contrôle du flux de travail, telles que les boucles et les conditions, les opérations de données, les opérations de traitement par lot, etc. | Le modèle ISE inclut ces opérations *gratuitement*, mais elles sont soumises aux [limites de l’ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Connecteur managé*](../connectors/managed.md) | Qu’il s’agisse de *Standard* ou d’*Entreprise*, les opérations de connecteur managé s’exécutent soit dans votre ISE, soit dans Azure multilocataire, selon que le connecteur ou l’opération affiche l’étiquette **ISE**. <p><p>- Étiquette **ISE** : Ces opérations s’exécutent dans le même ISE que votre application logique et fonctionnent sans nécessiter la [passerelle de données locale](#data-gateway). <p><p>**Pas d’étiquette** ISE : Ces opérations s’exécutent dans Azure multilocataire. | Le modèle ISE inclut les opérations avec étiquette **ISE** et sans étiquette **ISE** *gratuitement*, mais elles sont soumises aux [limites de l’ISE dans Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| [*Connecteur personnalisé*](../connectors/apis-list.md#custom-apis-and-connectors) | Dans le concepteur, vous pouvez trouver ces opérations sous l’étiquette **Personnalisé**. | Le modèle ISE inclut ces opérations *gratuitement*, mais elles sont soumises aux [limites du connecteur personnalisé dans Azure Logic Apps](logic-apps-limits-and-config.md#custom-connector-limits). |
||||

Pour plus d’informations sur le fonctionnement du modèle ISE avec les opérations qui s’exécutent à l’intérieur d’autres opérations, comme les boucles, le traitement de plusieurs éléments (p. ex. : les tableaux) et les stratégies de nouvelles tentatives, consultez [Comportement des autres opérations](#other-operation-behavior).

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>Comportement des autres opérations

Le tableau suivant résume la façon dont les modèles Consommation, Standard et ISE traitent les opérations, comme les boucles, le traitement de plusieurs éléments (p. ex. : les tableaux) et les stratégies de nouvelles tentatives :

| Opération | Description | Consommation | standard | ISE |
|-----------|-------------|-------------|----------|-----|
| [Actions de bouclage](logic-apps-control-flow-loops.md) | Une action de boucle, telle que la boucle **For each** ou **Until**, peut inclure d’autres actions qui s’exécutent pendant chaque cycle de boucle. | À l’exception du nombre initial d’opérations intégrées incluses, l’action de boucle et chaque action dans la boucle sont mesurées à chaque fois que le cycle de la boucle s’exécute. Si une action traite des éléments d’une collection, telle qu’une liste ou un tableau, le nombre d’éléments est également utilisé dans le calcul de la mesure. <p><p>Par exemple, supposons que vous ayez une boucle **For each** avec des actions qui traite une liste. Le service multiplie le nombre d’éléments de la liste par le nombre d’actions dans la boucle, puis ajoute l’action qui démarre la boucle. Le calcul pour une liste de 10 éléments est donc (10 * 1) + 1, ce qui donne 11 exécutions d’action. <p><p>La tarification est basée sur les types d’opération suivants (intégré, Standard ou Entreprise). | À l’exception des opérations intégrées incluses, il est identique au modèle Consommation. | Non mesuré ni facturé. |
| [Stratégies de nouvelle tentative](logic-apps-exception-handling.md#retry-policies) | Pour les opérations prises en charge, vous pouvez implémenter une gestion de base des exceptions et des erreurs en configurant une [stratégie de nouvelles tentatives](logic-apps-exception-handling.md#retry-policies). | À l’exception du nombre initial d’opérations intégrées, l’exécution d’origine et chaque nouvelle tentative d’exécution sont mesurées. Par exemple, une action qui s’exécute avec 5 nouvelles tentatives est mesurée et facturée comme 6 exécutions. <p><p>La tarification est basée sur les types d’opération suivants (intégré, Standard ou Entreprise). | À l’exception des opérations intégrées incluses, il est identique au modèle Consommation. | Non mesuré ni facturé. |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>Opérations de stockage

Azure Logic Apps utilise [Stockage Azure](../storage/index.yml) pour toutes les transactions de stockage requises, comme l’utilisation de files d’attente pour la planification des opérations de déclenchement ou l’utilisation de tables et de blobs pour le stockage des états du workflow. En fonction des opérations de votre flux de travail, les coûts de stockage varient, car les différents déclencheurs, actions et charges utiles entraînent des opérations et des besoins de stockage différents. Le service enregistre et stocke également les entrées et les sorties de l’historique des exécutions de votre flux de travail, en fonction de la [limite de rétention de l’historique des exécutions](logic-apps-limits-and-config.md#run-duration-retention-limits) de la ressource d’application logique. Vous pouvez gérer cette limite de rétention au niveau de la ressource d’application logique, et non au niveau du flux de travail.

Le tableau suivant résume la façon dont les modèles Consommation, Standard et ISE gèrent la mesure et la facturation des opérations de stockage :

| Modèle | Description | Métriques et facturation |
|-------|-------------|----------------------|
| Consommation (multilocataire) | Les ressources de stockage et leur utilisation sont jointes à la ressource d’application logique. | La mesure et la facturation *s’appliquent uniquement à la consommation de stockage liée à la conservation des données* et suivent la [tarification de la conservation des données pour le plan Consommation](https://azure.microsoft.com/pricing/details/logic-apps). |
| Standard (monolocataire) | Vous pouvez utiliser votre propre [compte de stockage](../azure-functions/storage-considerations.md#storage-account-requirements) Azure, ce qui vous donne plus de contrôle et de flexibilité sur les données de votre flux de travail. |  La mesure et la facturation suivent le [modèle de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/). Les coûts de stockage apparaissent séparément sur votre facture Azure. <p><p>**Conseil** : Pour vous aider à mieux comprendre le nombre d’opérations de stockage qu’un flux de travail pourrait exécuter et leur coût, essayez d’utiliser la [calculatrice de prix Stockage Logic Apps](https://logicapps.azure.com/calculator). Sélectionnez un exemple de flux de travail ou utilisez une définition de flux de travail existante. Le premier calcul estime le nombre d’opérations de stockage dans votre workflow. Vous pouvez ensuite utiliser ces nombres pour estimer les coûts potentiels en utilisant la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/). Pour plus d’informations, consultez [Estimer les besoins et coûts de stockage des workflows dans des applications Azure Logic Apps monolocataires](estimate-storage-costs.md). |
| Environnement de service d’intégration (ISE) | Les ressources de stockage et leur utilisation sont jointes à la ressource d’application logique. | Non mesuré ni facturé. |
||||

Pour plus d’informations, consultez la documentation suivante :

* [Afficher les métriques pour les exécutions et l’utilisation du stockage](plan-manage-costs.md#monitor-billing-metrics)
* [Limites dans Azure Logic Apps](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Passerelle de données locale

La [passerelle de données locale](../logic-apps/logic-apps-gateway-install.md) est une ressource Azure distincte que vous créez pour que les flux de travail de votre application logique puissent accéder aux données locales en utilisant des connecteurs spécifiques pris en charge par la passerelle. La ressource de la passerelle elle-même n’entraîne pas de frais, mais les opérations qui s’exécutent via la passerelle sont facturées en fonction de la tarification et du modèle de facturation utilisés par votre application logique.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Comptes d’intégration

Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) est une ressource Azure distincte que vous créez en tant que conteneur pour définir et stocker des artefacts interentreprises (business-to-business, B2B) tels que des partenaires commerciaux, des contrats, des schémas, des mappages, etc. Après avoir créé ce compte et défini ces artefacts, liez ce compte à votre application logique afin de pouvoir utiliser ces artefacts et diverses opérations B2B dans des flux de travail pour explorer, générer et tester des solutions d’intégration qui utilisent les capacités de traitement [EDI](logic-apps-enterprise-integration-b2b.md) et [XML](logic-apps-enterprise-integration-xml.md).

Le tableau suivant résume la façon dont les modèles Consommation, Standard et ISE gèrent la mesure et la facturation des comptes d’intégration :

| Modèle | Métriques et facturation |
|-------|----------------------|
| Consommation (multilocataire) | La mesure de l’utilisation et la facturation utilisent la [tarification des comptes d’intégration](https://azure.microsoft.com/pricing/details/logic-apps/), en fonction du niveau de service du compte que vous utilisez. |
| Standard (monolocataire) | La mesure de l’utilisation et la facturation utilisent la [tarification des comptes d’intégration](https://azure.microsoft.com/pricing/details/logic-apps/), en fonction du niveau de service du compte que vous utilisez. |
| ISE | Ce modèle comprend un seul compte d’intégration basé sur votre niveau tarifaire ISE. Pour un [coût supplémentaire](https://azure.microsoft.com/pricing/details/logic-apps/), vous pouvez créer davantage de comptes d’intégration pour votre ISE à utiliser jusqu’à la [limite totale de l’ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). |
|||

Pour plus d’informations, consultez la documentation suivante :

* [Créer et gérer des comptes d’intégration](logic-apps-enterprise-integration-create-integration-account.md)
* [Limites du compte d’intégration dans Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>Autres éléments non mesurés ou facturés

Dans tous les modèles de tarification, les éléments suivants ne sont pas mesurés ni facturés :

* Actions qui n’ont pas été exécutées, car le flux de travail s’est arrêté avant la fin
* Applications logiques ou flux de travail désactivés, car ils ne peuvent pas créer de nouvelles instances tant qu’ils sont inactifs.

## <a name="next-steps"></a>Étapes suivantes

* [Prévoir et gérer les coûts d’Azure Logic Apps](plan-manage-costs.md)
