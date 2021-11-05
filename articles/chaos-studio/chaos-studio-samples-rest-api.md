---
title: Utiliser les API REST pour gérer les expériences Azure Chaos Studio
description: Exécutez et gérez une expérience de chaos avec Azure Chaos Studio à l’aide des API REST.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: edbaeece6a486c170603304dfe328974e6b70879
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097044"
---
# <a name="use-the-chaos-studio-rest-apis-to-run-and-manage-chaos-experiments"></a>Utiliser les API REST Chaos Studio pour exécuter et gérer les expériences de chaos

> [!WARNING]
> L’injection d’erreurs peut impacter votre application ou votre service. Veillez à ne pas occasionner de perturbations pour les clients.  

L’API Chaos Studio aide à démarrer des expériences par programmation. Vous pouvez également utiliser armclient et l’interface Azure CLI pour exécuter ces commandes à partir de la console. Les exemples ci-après s’appliquent à l’interface Azure CLI.

> [!Warning]
> Ces API sont toujours en cours de développement et sujettes à modification.

## <a name="rest-apis"></a>API REST

Vous pouvez utiliser les API REST Squall pour démarrer et arrêter des expériences, interroger l’état de la cible, interroger l’état des expériences et interroger et supprimer des configurations d’abonnement. Vous pouvez utiliser l’utilitaire `AZ CLI` pour effectuer ces actions à partir de la ligne de commande.

> [!TIP]
> Pour obtenir une sortie plus détaillée avec AZ CLI, ajoutez **--verbose** à la fin de chaque commande. Cela retournera plus de métadonnées à l’exécution des commandes, notamment **x-ms-correlation-request-id**, qui simplifie le débogage.

### <a name="chaos-provider-commands"></a>Commandes relatives au fournisseur Chaos

#### <a name="enumerate-details-about-the-microsoftchaos-resource-provider"></a>Afficher des informations détaillées sur le fournisseur de ressources Microsoft.Chaos

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-all-the-operations-of-the-chaos-studio-resource-provider"></a>Lister toutes les opérations du fournisseur de ressources Chaos Studio

```bash
az rest --method get --url "https://management.azure.com/providers/Microsoft.Chaos/operations?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-chaos-provider-configurations"></a>Lister les configurations de fournisseur Chaos

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/?api-version={apiVersion}" --resource "https://management.azure.com" --verbose 
```

#### <a name="create-chaos-provider-configuration"></a>Créer une configuration de fournisseur Chaos

```bash
az rest --method put --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/{chaosProviderType}?api-version={apiVersion}" --body @{providerSettings.json} --resource "https://management.azure.com"
```

### <a name="chaos-target-and-agent-commands"></a>Commandes relatives aux cibles et agents Chaos

#### <a name="list-all-the-targets-or-agents-under-a-subscription"></a>Lister toutes les cibles ou tous les agents d’un abonnement

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos/chaosTargets/?api-version={apiVersion}" --url-parameter "chaosProviderType={chaosProviderType}" --resource "https://management.azure.com"
```

### <a name="chaos-experiment-commands"></a>Commandes relatives aux expériences Chaos

#### <a name="list-all-experiments-in-a-resource-group"></a>Lister toutes les expériences dans un groupe de ressources

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Chaos/chaosExperiments?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-an-experiment-configuration-details-by-name"></a>Obtenir les détails d’une configuration d’expérience par nom

```bash
az rest --method get --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="create-or-update-an-experiment"></a>Créer ou mettre à jour une expérience

```bash
az rest --method put --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --body @{experimentName.json} --resource "https://management.azure.com"
```

#### <a name="delete-an-experiment"></a>Supprimer une expérience

```bash
az rest --method delete --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com" --verbose
```

#### <a name="start-an-experiment"></a>Commencer une expérience

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/start?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-statuses-history-of-an-experiment"></a>Obtenir les états (historique) d’une expérience

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/statuses?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-status-of-an-experiment"></a>Obtenir l’état d’une expérience

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/status?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="cancel-stop-an-experiment"></a>Annuler (arrêter) une expérience

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/cancel?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-the-last-two-experiment-executions"></a>Lister les détails des deux dernières exécutions d’expérience

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-a-specific-experiment-execution"></a>Lister les détails d’une exécution d’expérience spécifique

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails/{executionDetailsId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

## <a name="parameter-definitions"></a>Définitions de paramètres

| Nom du paramètre | Définition | Recherche |
| --- | --- | --- |
| {apiVersion} | Version de l’API à utiliser quand la commande fournie est exécutée | Disponible dans la [documentation de l’API](https://aka.ms/chaosrestapi) |
| {experimentId} | ID de ressource Azure pour l’expérience | Disponible dans le [panneau du portail des expériences Chaos Studio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments) |
| {chaosProviderType} | Type ou nom du fournisseur Chaos Studio | Pour connaître les fournisseurs disponibles, consultez la [liste des types de configuration de fournisseurs actuels](chaos-studio-fault-providers.md) |
| {experimentName.json} | JSON contenant la configuration de l’expérience de chaos | Généré par l’utilisateur |
| {subscriptionId} | ID de l’abonnement où se trouve la ressource cible | Disponible dans le [panneau du portail Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) |
| {resourceGroupName} | Nom du groupe de ressources dans lequel se trouve la ressource cible | Disponible dans le [panneau du portail Groupes de ressources](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) |
| {executionDetailsId} | ID d’exécution d’une exécution d’expérience | Disponible dans le [panneau du portail des expériences Chaos Studio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments) |
