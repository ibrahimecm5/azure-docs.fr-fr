---
title: Créer un principal de service d’intégration pour Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: 'Créer un principal de service d’intégration compatible avec Azure Arc '
keywords: Kubernetes, Arc, Azure, conteneurs
ms.openlocfilehash: 5c102ce73672528810bbb35b2a74a5de2a3bf81e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056242"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Créer un principal de service d’intégration pour Kubernetes avec Azure Arc

## <a name="overview"></a>Vue d’ensemble

Vous pouvez connecter des clusters Kubernetes à Azure Arc à l’aide de principaux de service ayant des attributions de rôles à privilèges limités. Cette fonctionnalité est utile dans les pipelines d’intégration et de déploiement continus (CI/CD) comme Azure Pipelines et GitHub Actions.

Suivez les étapes ci-dessous afin d’apprendre à utiliser les principaux de service pour connecter des clusters Kubernetes à Azure Arc.

## <a name="create-a-new-service-principal"></a>Création d’un principal de service

Créez un principal de service avec un nom informatif unique pour votre locataire Azure Active Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Output:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Affecter des autorisations

Attribuez le rôle « Kubernetes Cluster - Azure Arc Onboarding » au principal de service que vous venez de créer. Ce rôle Azure intégré doté d’autorisations limitées permet uniquement au principal d’enregistrer des clusters dans Azure. Le principal doté de ce rôle attribué ne peut pas mettre à jour, supprimer ni modifier les autres clusters ou ressources au sein de l’abonnement.

Étant donné les capacités limitées, les clients peuvent facilement réutiliser ce principal pour intégrer plusieurs clusters.

Vous pouvez limiter encore les autorisations en transmettant l’argument `--scope` approprié lors de l’affectation du rôle. Cela permet aux administrateurs de restreindre l’inscription du cluster à l’étendue de l’abonnement ou du groupe de ressources. Les scénarios suivants sont pris en charge par différents paramètres `--scope` :

| Ressource  | Argument `scope`| Résultat |
| ------------- | ------------- | ------------- |
| Abonnement | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Le principal de service peut inscrire le cluster dans n’importe quel groupe de ressources sous cet abonnement. |
| Groupe de ressources | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Le principal de service peut __uniquement__ inscrire les clusters dans le groupe de ressources `myGroup`. |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Output:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Utiliser le principal de service avec Azure CLI

Référencez le principal de service que vous venez de créer avec les commandes suivantes :

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Gérez la configuration de votre cluster [à l’aide d’Azure Policy](./use-azure-policy.md).
