---
title: Sécurité et gouvernance en entreprise
titleSuffix: Azure Machine Learning
description: 'Utilisez de manière sécurisée Azure Machine Learning : authentification, autorisation, sécurité réseau, chiffrement des données et supervision.'
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: d5034f59cc71352518c0570e525f952f0f657c71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343132"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sécurité et gouvernance de l’entreprise pour Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité et de gouvernance disponibles pour Azure Machine Learning. Ces fonctionnalités sont utiles pour les administrateurs, les DevOps et les MLOps qui souhaitent créer une configuration sécurisée conforme à vos stratégies d’entreprise. Azure Machine Learning et la plateforme Azure vous permettent d’effectuer les opérations suivantes :

* Limiter l’accès aux ressources et aux opérations par compte d’utilisateur ou par groupe
* Limiter les communications réseau entrantes et sortantes
* Chiffrer des données en transit et au repos
* Analyser les vulnérabilités
* Appliquer et auditer des stratégies de configuration

## <a name="restrict-access-to-resources-and-operations"></a>Limiter l'accès aux ressources et aux opérations

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) est le fournisseur de service d’identité pour Azure Machine Learning. Il vous permet de créer et de gérer les objets de sécurité (utilisateur, groupe, principal de service et identité managée) utilisés pour s’_authentifier_ auprès des ressources Azure. L’authentification multifacteur est prise en charge si Azure AD est configuré pour l’utiliser.

Voici le processus d’authentification pour Azure Machine Learning à l’aide de l’authentification multifacteur dans Azure AD :

1. Le client se connecte à Azure AD et obtient un jeton Azure Resource Manager.
1. Le client présente le jeton à Azure Resource Manager et à tout Azure Machine Learning.
1. Azure Machine Learning fournit un jeton de service Machine Learning à la cible de calcul utilisateur (par exemple, cluster de calcul Azure Machine Learning). Ce jeton est utilisé par la cible de calcul utilisateur pour rappeler le service Machine Learning une fois l’exécution terminée. L’étendue se limite à l’espace de travail.

[![Authentification dans Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Chaque espace de travail est associé à une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) attribuée par le système qui porte le même nom que l’espace de travail. Cette identité managée est utilisée pour accéder en toute sécurité aux ressources utilisées par l’espace de travail. Elle dispose des autorisations Azure RBAC suivantes sur les ressources associées :

| Ressource | Autorisations |
| ----- | ----- |
| Espace de travail | Contributeur |
| Compte de stockage | Contributeur aux données Blob du stockage |
| Coffre de clés | Accès à l’ensemble des clés, secrets et certificats |
| Azure Container Registry | Contributeur |
| Groupe de ressources contenant l’espace de travail | Contributeur |

L’identité managée affectée par le système est utilisée pour l’authentification de service à service interne entre Azure Machine Learning et d’autres ressources Azure. Le jeton d’identité n’est pas accessible aux utilisateurs, qui ne peuvent pas s’en servir pour accéder à ces ressources. Ils n’ont accès aux ressources que par le biais des [API de contrôle et de plan de données Azure Machine Learning](how-to-assign-roles.md), s’ils disposent d’autorisations RBAC suffisantes.

L’identité managée a besoin d’autorisations de type Contributeur sur le groupe de ressources contenant l’espace de travail afin de provisionner les ressources associées et de [déployer Azure Container Instances pour des points de terminaison de service web](how-to-deploy-azure-container-instance.md).

Nous déconseillons aux administrateurs de révoquer l’accès de l’identité managée aux ressources mentionnées dans le tableau précédent. Vous pouvez restaurer l’accès par une [opération de resynchronisation des clés](how-to-change-storage-access-key.md).

> [!NOTE]
> Si vos espaces de travail Azure Machine Learning comportent des cibles de calcul (cluster de calcul, instance de calcul, Azure Kubernetes Service, etc.) créées __avant le 14 mai 2021__, vous pouvez également disposer d’un compte Azure Active Directory supplémentaire dont le nom commence par `Microsoft-AzureML-Support-App-`. Ce compte dispose d’un accès de niveau Contributeur à votre abonnement pour chacune des régions de l’espace de travail.
> 
> Si votre espace de travail n’est pas attaché à un service Azure Kubernetes Service (AKS), vous pouvez sans risque supprimer ce compte Azure AD. 
> 
> Si votre espace de travail est attaché à des clusters AKS _créés avant le 14 mai 2021_, __ne supprimez pas ce compte Azure AD__. Dans ce cas de figure, vous devez commencer par supprimer et recréer le cluster AKS pour pouvoir supprimer le compte Azure AD.

Vous pouvez provisionner l’espace de travail de façon à utiliser l’identité managée affectée par l’utilisateur et à accorder à celle-ci des rôles supplémentaires, par exemple pour accéder à votre propre registre Azure Container Registry d’images Docker de base. Pour plus d’informations, consultez [Contrôle d’accès avec des identités managées](how-to-use-managed-identities.md).

Vous pouvez également configurer des identités managées pour pouvoir les utiliser avec un cluster de calcul Azure Machine Learning. Cette identité managée est indépendante de celle de l’espace de travail. Avec un cluster de calcul, l’identité managée permet d’accéder à des ressources telles que des magasins de stockage sécurisés auxquels l’utilisateur exécutant le travail d’apprentissage n’a peut-être pas accès. Pour plus d’informations, consultez [Accès basé sur l’identité aux données des services de stockage sur Azure](how-to-identity-based-data-access.md).

> [!TIP]
> Il existe quelques exceptions à l’utilisation d’Azure AD et d’Azure RBAC dans Azure Machine Learning :
> * Vous pouvez éventuellement activer l’accès __SSH__ à certaines ressources de calcul, telles qu’une instance de calcul Azure Machine Learning et un cluster de calcul. L’accès SSH est basé sur les paires de clés publiques/privées et non sur Azure AD. L’accès SSH n’est pas régi par Azure RBAC.
> * Vous pouvez vous authentifier sur les modèles déployés en tant que services web (points de terminaison d’inférence) à l’aide de l’authentification basée sur les __clés__ ou les __jetons__. Les clés sont des chaînes statiques, tandis que les jetons sont récupérés à l’aide d’un objet de sécurité Azure AD. Pour plus d’informations, consultez [Configurer l’authentification pour des modèles déployés en tant que services web](how-to-authenticate-web-service.md).

Pour plus d’informations, consultez les articles suivants :
* [Authentification pour l’espace de travail Azure Machine Learning](how-to-setup-authentication.md)
* [Gérer l’accès à Azure Machine Learning](how-to-assign-roles.md)
* [Se connecter à des services de stockage](how-to-access-data.md)
* [Utiliser Azure Key Vault pour les secrets lors des formations](how-to-use-secrets-in-runs.md)
* [Utiliser l’identité managée Azure AD avec Azure Machine Learning](how-to-use-managed-identities.md)
* [Utiliser l’identité managée Azure AD avec votre service web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Sécurité et isolation de réseau

Pour restreindre l’accès réseau aux ressources Azure Machine Learning, vous pouvez utiliser le [Réseau virtuel Microsoft Azure (VNet)](../virtual-network/virtual-networks-overview.md). Les réseaux virtuels vous permettent de créer des environnements réseau partiellement ou entièrement isolés de l’Internet public. Cela a pour effet de réduire la surface d’attaque de votre solution, ainsi que les risques d’exfiltration de données.

Vous pouvez utiliser une passerelle de réseau privé virtuel (VPN) pour connecter des clients individuels ou votre propre réseau au réseau virtuel.

L’espace de travail Azure Machine Learning peut utiliser [Azure Private Link](../private-link/private-link-overview.md) pour créer un point de terminaison privé derrière le réseau virtuel. Cela fournit un ensemble d’adresses IP privées qui peuvent permettre d’accéder à l’espace de travail à partir du réseau virtuel. Certains des services sur lesquels Azure Machine Learning s’appuie peuvent également utiliser Azure Private Link, mais certains reposent sur des groupes de sécurité réseau ou un routage défini par l’utilisateur.

Pour plus d’informations, consultez les documents suivants :

* [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Utiliser le studio dans un réseau virtuel sécurisé](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Configurer le pare-feu](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Chiffrement des données

Azure Machine Learning utilise une série de ressources de calcul et de magasins de données sur la plateforme Azure. Pour en savoir plus sur la façon dont ceux-ci prennent en charge le chiffrement de données au repos et en transit, consultez [Chiffrement des données avec Azure Machine Learning](concept-data-encryption.md).

Lors du déploiement de modèles en tant que services web, vous pouvez activer le protocole TLS pour chiffrer les données en transit. Pour plus d’informations, consultez [Configurer le service de synchronisation web](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Analyse des vulnérabilités

[Microsoft Defender pour le cloud](../security-center/security-center-introduction.md) fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces dans les charges de travail cloud hybrides. Pour Azure Machine Learning, vous devez activer l’analyse de vos ressources [Azure Container Registry](../container-registry/container-registry-intro.md) et Azure Kubernetes Service. Pour plus d’informations, consultez [Analyse d’images Azure Container Registry par Defender pour le cloud](../security-center/defender-for-container-registries-introduction.md) et [Intégration d’Azure Kubernetes Service à Defender pour le cloud](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditer et gérer la conformité

[Azure Policy](../governance/policy/index.yml) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies. Vous pouvez définir des stratégies pour autoriser ou appliquer des configurations spécifiques, par exemple si votre espace de travail Azure Machine Learning utilise un point de terminaison privé. Pour plus d’informations sur Azure Policy, consultez la [documentation relative à Azure Policy](../governance/policy/overview.md). Pour plus d’informations sur les stratégies spécifiques à Azure Machine Learning, consultez [Audit et gestion de la conformité avec Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Étapes suivantes

* [Meilleures pratiques en lien avec Azure Machine Learning pour la sécurité de l’entreprise](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)
* [Sécuriser les services web Azure Machine Learning avec TLS](how-to-secure-web-service.md)
* [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
* [Utiliser Azure Machine Learning avec un pare-feu Azure](how-to-access-azureml-behind-firewall.md)
* [Utiliser Azure Machine Learning avec un réseau virtuel Azure](how-to-network-security-overview.md)
* [Chiffrement au repos et en transit](concept-data-encryption.md)
* [Générer une API de recommandation en temps réel sur Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
