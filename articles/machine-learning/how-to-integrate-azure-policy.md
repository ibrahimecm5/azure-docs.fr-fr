---
title: Auditer et gérer Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser Azure Policy pour utiliser des stratégies intégrées pour Azure Machine Learning afin de vous assurer que vos espaces de travail sont conformes à vos besoins.
author: aashishb
ms.author: aashishb
ms.date: 10/21/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: ea374a6503b2d015ca92d3aee8179b3511c9755f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289677"
---
# <a name="audit-and-manage-azure-machine-learning"></a>Auditer et gérer Azure Machine Learning

Quand plusieurs équipes collaborent sur Azure Machine Learning, elles peuvent être confrontées à différents impératifs liés à la configuration et à l’organisation des ressources. Les équipes de machine learning peuvent chercher à obtenir une certaine flexibilité dans l’organisation des espaces de travail à des fins de collaboration, ou bien à dimensionner les clusters de calcul en fonction des impératifs de leurs cas d’usage. Dans ces scénarios, une productivité maximale peut être atteinte si l’équipe d’application parvient à gérer sa propre infrastructure.

En tant qu’administrateur de plateforme, vous pouvez utiliser des stratégies pour disposer des garde-fous permettant aux équipes de gérer leurs propres ressources. [Azure Policy](../governance/policy/index.yml) permet d’auditer et de gouverner l’état des ressources. Dans cet article, vous allez découvrir les contrôles d’audit et les pratiques de gouvernance disponibles pour Azure Machine Learning.

## <a name="policies-for-azure-machine-learning"></a>Stratégies pour Azure Machine Learning

[Azure Policy](../governance/policy/index.yml) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies.

Azure Machine Learning fournit un ensemble de stratégies que vous pouvez utiliser dans des scénarios courants. Vous pouvez attribuer ces définitions de stratégie à votre abonnement existant ou les utiliser comme base pour créer vos propres définitions personnalisées.

Le tableau ci-dessous comprend une sélection de stratégies que vous pouvez affecter avec Azure Machine Learning. Pour obtenir la liste complète des stratégies intégrées pour Azure Machine Learning, consultez [Stratégies intégrées pour Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

| Stratégie | Description |
| ----- | ----- |
| **Clé gérée par le client** | auditez ou appliquez une valeur indiquant si les espaces de travail doivent utiliser une clé gérée par le client. |
| **Liaison privée** | Auditez ou appliquez une valeur indiquant si les espaces de travail utilisent un point de terminaison privé pour communiquer avec un réseau virtuel. |
| **Point de terminaison privé** | Configurez le sous-réseau du réseau virtuel Azure sur lequel le point de terminaison privé doit être créé. |
| **Zone DNS privée** | Configurez la zone DNS privée à utiliser pour la liaison privée. |
| **Identité managée affectée par l’utilisateur** | Auditez ou appliquez une valeur indiquant si les espaces de travail utilisent une identité gérée affectée par l’utilisateur. |
| **Désactiver l’authentification locale** | Auditez ou appliquez si les méthodes d’authentification locales doivent être désactivées pour les ressources de calcul Azure Machine Learning. |
| **Modifier/désactiver l’authentification locale** | Configurez les ressources de calcul pour désactiver les méthodes d’authentification locales. |

Les stratégies peuvent être définies sur des étendues différentes, par exemple au niveau de l'abonnement ou du groupe de ressources. Pour plus d'informations, consultez la [documentation relative à Azure Policy](../governance/policy/overview.md).

## <a name="assigning-built-in-policies"></a>Affectation de stratégies intégrées

Pour consulter les définitions de stratégie intégrées en rapport avec Azure Machine Learning, procédez comme suit :

1. Accédez à __Azure Policy__ sur le [portail Azure](https://portal.azure.com).
1. Sélectionnez __Définitions__.
1. Dans le champ __Type__, sélectionnez _Intégré_ et dans le champ __Catégorie__, sélectionnez __Machine Learning__.

De là, vous pouvez sélectionner les définitions de stratégie à consulter. Lorsque vous consultez une définition, vous pouvez utiliser le lien __Attribuer__ pour attribuer la stratégie à une étendue spécifique et configurer les paramètres de cette stratégie. Pour plus d'informations, consultez [Attribuer une stratégie - portail](../governance/policy/assign-policy-portal.md).

Vous pouvez également attribuer des stratégies à l'aide d'[Azure PowerShell](../governance/policy/assign-policy-powershell.md), d'[Azure CLI](../governance/policy/assign-policy-azurecli.md) et de [modèles](../governance/policy/assign-policy-template.md).

## <a name="conditional-access-policies"></a>Stratégies d'accès conditionnel

Pour contrôler qui peut accéder à votre espace de travail Azure Machine Learning, utilisez l’[accès conditionnel](../active-directory/conditional-access/overview.md) Azure Active Directory.

## <a name="enable-self-service-using-landing-zones"></a>Activer le libre-service à l’aide des zones d’atterrissage

Les zones d’atterrissage sont un modèle architectural permettant de configurer les environnements Azure pour prendre en compte la mise à l’échelle, la gouvernance, la sécurité et la productivité. Une zone d’atterrissage de données est un environnement configuré par un administrateur et utilisé par une équipe d’application pour héberger une charge de travail de données et d’analytique.

L’objectif de la zone d’atterrissage est de garantir qu’au moment où une équipe commence à travailler dans l’environnement Azure, l’ensemble de la configuration de l’infrastructure est effectué. Par exemple, les contrôles de sécurité sont configurés conformément aux standards de l’organisation, et la connectivité réseau est configurée.

Le modèle des zones d’atterrissage permet aux équipes de machine learning de déployer et gérer leurs propres ressources en libre-service. À l’aide d’Azure Policy, en tant qu’administrateur, vous pouvez auditer et gérer la conformité des ressources Azure, et vérifier que les espaces de travail sont conformes aux besoins. 

Azure Machine Learning s’intègre aux [zones d’atterrissage de données](https://github.com/Azure/data-landing-zone) dans le [scénario de gestion des données et d’analytique du Cloud Adoption Framework](/azure/cloud-adoption-framework/scenarios/data-management/). Cette implémentation de référence fournit un environnement optimisé vers lequel migrer les charges de travail de machine learning. De plus, elle comprend des stratégies préconfigurées pour Azure Machine Learning.

## <a name="configure-built-in-policies"></a>Configurer des stratégies intégrées

### <a name="workspace-encryption-with-customer-managed-key"></a>Chiffrement de l’espace de travail à l’aide d’une clé gérée par le client

Détermine si un espace de travail doit être chiffré à l’aide d’une clé gérée par le client, ou si une clé gérée par Microsoft doit être utilisée pour chiffrer les métriques et les métadonnées. Pour plus d’informations sur l’utilisation d’une clé gérée par le client, consultez la section [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) de l’article consacré au chiffrement des données.

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__ ou __deny__. Si le paramètre est défini sur __audit__, vous pouvez créer un espace de travail sans clé gérée par le client ; un événement d’avertissement est alors créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer d’espace de travail, sauf si une clé gérée par le client est spécifiée. Toute tentative de création d’un espace de travail sans clé gérée par le client génère une erreur semblable à `Resource 'clustername' was disallowed by policy` et crée une erreur dans le journal d’activité. L'identificateur de la stratégie est également renvoyé dans le cadre de cette erreur.

### <a name="workspace-should-use-private-link"></a>L’espace de travail doit utiliser Private Link

Détermine si un espace de travail doit utiliser Azure Private Link pour communiquer avec le Réseau virtuel Azure. Pour plus d'informations sur l'utilisation de Private Link, consultez [Configurer Private Link pour un espace de travail](how-to-configure-private-link.md).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__ ou __deny__. Si le paramètre est défini sur __audit__, vous pouvez créer un espace de travail utiliser Private Link ; un événement d’avertissement est alors créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer d’espace de travail, sauf s’il utilise Private Link. Toute tentative de création d’un espace de travail sans liaison privée génère une erreur. L’erreur est également consignée dans le journal d’activité. L’identificateur de la stratégie est renvoyé dans le cadre de cette erreur.

### <a name="workspace-should-use-private-endpoint"></a>L’espace de travail doit utiliser un point de terminaison privé

Configure un espace de travail pour créer un point de terminaison privé dans le sous-réseau spécifié d’un réseau virtuel Azure.

Pour configurer cette stratégie, définissez le paramètre d’effet sur __DeployIfNotExists__. Définissez le __privateEndpointSubnetID__ sur l’ID Azure Resource Manager du sous-réseau.

### <a name="workspace-should-use-private-dns-zones"></a>L’espace de travail doit utiliser des zones DNS privées

Configure un espace de travail pour utiliser une zone DNS privée, en remplaçant la résolution DNS par défaut pour un point de terminaison privé.

Pour configurer cette stratégie, définissez le paramètre d’effet sur __DeployIfNotExists__. Définissez __privateDnsZoneId__ sur l’ID Azure Resource Manager de la zone DNS privée à utiliser. 

### <a name="workspace-should-use-user-assigned-managed-identity"></a>L’espace de travail doit utiliser une identité gérée affectée par l’utilisateur

Contrôle si un espace de travail est créé à l’aide d’une identité gérée affectée par le système (valeur par défaut) ou d’une identité gérée affectée par l’utilisateur. L’identité gérée de l’espace de travail est utilisée pour accéder aux ressources associées telles que le stockage Azure, Azure Container Registry, Azure Key Vault et Azure Application Insights. Pour plus d’informations, consultez [Utiliser les identités managées avec Azure Machine Learning](how-to-use-managed-identities.md).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__, __deny__ ou __disabled__. Si la valeur est définie sur __audit__, vous pouvez créer un espace de travail sans spécifier d’identité gérée affectée à l'utilisateur. Une identité affectée par le système est utilisée et un événement d’avertissement est créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer un espace de travail, sauf si vous fournissez une identité affectée par l’utilisateur pendant le processus de création. Une erreur survient si vous essayez de créer un espace de travail sans fournir d’identité affectée par l’utilisateur. L’erreur est également consignée dans le journal d’activité. L’identificateur de la stratégie est renvoyé dans le cadre de cette erreur.

### <a name="disable-local-authentication"></a>Désactiver l’authentification locale

Contrôle si un cluster ou une instance de calcul Azure Machine Learning doit désactiver l’authentification locale (SSH).

Pour configurer cette stratégie, définissez le paramètre d'effet sur __audit__, __deny__ ou __disabled__. Si le paramètre est défini sur __audit__, vous pouvez créer un calcul avec SSH activé et un événement d’avertissement est créé dans le journal d’activité.

Si la stratégie est définie sur __deny__, vous ne pouvez pas créer de calcul, sauf si SSH est désactivé. Toute tentative de création d’un calcul avec SSH activé génère une erreur. L’erreur est également consignée dans le journal d’activité. L’identificateur de la stratégie est renvoyé dans le cadre de cette erreur.

### <a name="modifydisable-local-authentication"></a>Modifier/désactiver l’authentification locale

Modifie toute demande de création d’instance ou de cluster de calcul Azure Machine Learning pour désactiver l’authentification locale (SSH).

Pour configurer cette stratégie, définissez le paramètre d’effet sur __Modifier__ ou __Désactivé__. Si vous choisissez __Modifier__, l’authentification locale sera automatiquement désactivée pour toute création d’un cluster ou d’une instance de calcul dans l’étendue à laquelle la stratégie s’applique.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation Azure Policy](../governance/policy/overview.md)
* [Stratégies intégrées pour Azure Machine Learning](policy-reference.md)
* [Utilisation des stratégies de sécurité avec Microsoft Defender pour le cloud](../security-center/tutorial-security-policy.md)
* Le [scénario du Cloud Adoption Framework pour la gestion des données et l’analytique](/azure/cloud-adoption-framework/scenarios/data-management/) décrit les points à prendre en compte pour l’exécution des charges de travail liées aux données et à l’analytique dans le cloud.
* Les [zones d’atterrissage de données du Cloud Adoption Framework](https://github.com/Azure/data-landing-zone) fournissent une implémentation de référence pour la gestion des charges de travail liées aux données et à l’analytique dans Azure.
* [Découvrez comment utiliser une stratégie permettant d’intégrer Azure Private Link aux zones DNS privées Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/private-link-and-dns-integration-at-scale) afin de gérer la configuration des liaisons privées pour l’espace de travail et les ressources dépendantes.
