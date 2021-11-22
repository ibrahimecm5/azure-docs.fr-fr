---
title: Gérer une infrastructure hybride à grande échelle avec Azure Arc
description: Azure Lighthouse vous permet de gérer efficacement les machines et les clusters Kubernetes des clients en dehors d’Azure.
ms.date: 09/07/2021
ms.topic: how-to
ms.openlocfilehash: 5acb9cae08795a9c87db0d6353f4870e92849f24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293058"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Gérer une infrastructure hybride à grande échelle avec Azure Arc

[Azure Lighthouse](../overview.md) peut aider les fournisseurs de services à utiliser Azure Arc pour gérer les environnements hybrides des clients, avec une visibilité sur tous les locataires Azure Active Directory (Azure AD) managés.

[Azure Arc](../../azure-arc/overview.md) permet de simplifier les environnements complexes et distribués localement, en périphérie et sur différents clouds, ce qui vous permet de déployer des services Azure n’importe où et d’étendre la gestion Azure à n’importe quelle infrastructure.

Grâce aux [serveurs avec Azure Arc](../../azure-arc/servers/overview.md), les clients peuvent gérer toutes les machines Windows et Linux hébergées en dehors d'Azure sur leur réseau d'entreprise de la même façon qu'ils gèrent des machines virtuelles Azure natives. Dès lors qu’une machine hybride est liée à Azure, elle est connectée et traitée comme ressource dans Azure. Les fournisseurs de services peuvent ensuite gérer ces machines non Azure, ainsi que les ressources Azure de leurs clients.

[Kubernetes compatible avec Azure Arc](../../azure-arc/kubernetes/overview.md) permet aux clients de joindre et de configurer des clusters Kubernetes à l’intérieur ou à l’extérieur d’Azure. Lorsqu’un cluster Kubernetes est joint à Azure Arc, il s’affiche dans le portail Azure, avec un ID Azure Resource Manager et une identité managée. Les clusters sont attachés à des abonnements Azure standard, sont situés dans un groupe de ressources et peuvent recevoir des étiquettes comme n’importe quelle autre ressource Azure.

Cette rubrique fournit une vue d’ensemble évolutive de l’utilisation des serveurs avec Azure Arc et de Kubernetes avec Azure Arc dans les locataires clients que vous gérez.

> [!TIP]
> Bien que nous faisons référence aux fournisseurs de services et aux clients dans cette rubrique, ces instructions s’appliquent également aux [entreprises utilisant Azure Lighthouse pour gérer plusieurs locataires](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arcenabled-servers"></a>Gérer des serveurs hybrides à grande échelle à l'aide de serveurs avec Azure Arc

En tant que fournisseur de services, vous pouvez gérer des machines Windows Server ou Linux locales en dehors d’Azure que vos clients ont connectées à leur abonnement à l’aide de l’[agent Azure Connected Machine](../../azure-arc/servers/agent-overview.md). Lorsque vous affichez les ressources d’un abonnement délégué dans le portail Azure, vous verrez ces ordinateurs connectés étiquetés avec **Azure Arc**.

Vous pouvez gérer ces machines connectées à l’aide de constructions Azure, telles qu’Azure Policy et le balisage, de la même façon que vous géreriez les ressources Azure du client. Vous pouvez également travailler sur plusieurs locataires client pour gérer ensemble les machines hybrides connectées.

Par exemple, vous pouvez [vous assurer que le même ensemble de stratégies est appliqué sur les machines hybrides des clients](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). Vous pouvez également utiliser Microsoft Defender pour le cloud afin de surveiller la conformité dans tous les environnements hybrides de vos clients, ou [utiliser Azure Monitor pour collecter des données directement depuis les machines hybrides](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) et les copier dans un espace de travail Log Analytics. Les [extensions de machine virtuelle](../../azure-arc/servers/manage-vm-extensions.md) peuvent être déployées sur des machines virtuelles Windows et Linux non Azure, ce qui simplifie la gestion des machines hybrides du client.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Gérer des clusters Kubernetes hybrides à grande échelle à l’aide de Kubernetes avec Azure Arc

Vous pouvez gérer les clusters Kubernetes qui ont été [connectés à l’abonnement d’un client avec Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md), comme s’ils étaient exécutés sur Azure.

Si votre client a créé un [compte de principal du service pour intégrer des clusters Kubernetes à Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md), vous pouvez accéder à ce compte de sorte que vous puissiez intégrer et gérer des clusters. Pour ce faire, un utilisateur du locataire gérant doit avoir reçu le rôle Azure intégré « Cluster Kubernetes – Intégration Azure Arc » lorsque l’abonnement contenant le compte du principal de service a été [intégré à Azure Lighthouse](onboard-customer.md).

Vous pouvez déployer des [configurations](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) et des [charts Helm](../../azure-arc/kubernetes/use-gitops-with-helm.md) à l’aide de GitOps pour les clusters connectés.

Vous pouvez également surveiller les clusters connectés avec Azure Monitor et [utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Étapes suivantes

- Explorez les démarrages rapides et les exemples dans le [référentiel GitHub d’Azure Arc](https://github.com/microsoft/azure_arc).
- En savoir plus sur les [scénarios pris en charge pour les serveurs avec Azure Arc](../../azure-arc/servers/overview.md#supported-cloud-operations).
- En savoir plus sur les [distributions Kubernetes prises en charge par Azure Arc](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
