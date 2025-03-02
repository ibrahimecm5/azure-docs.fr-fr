---
title: Scénarios d’authentification avec Azure Container Registry à partir de Kubernetes
description: Vue d’ensemble des options et des scénarios permettant de s’authentifier auprès d’un registre de conteneurs Azure à partir d’un cluster Kubernetes pour extraire des images conteneur
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/20/2021
ms.openlocfilehash: 2fc24714183438f9d740de79f9a9e9ce11f17391
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545394"
---
# <a name="scenarios-to-authenticate-with-azure-container-registry-from-kubernetes"></a>Scénarios d’authentification avec Azure Container Registry à partir de Kubernetes


Vous pouvez utiliser un registre de conteneurs Azure comme source d’images conteneur pour Kubernetes, notamment les clusters que vous gérez, les clusters managés hébergés dans [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md) ou d’autres clouds, et les configurations Kubernetes locales, telles que [minikube](https://minikube.sigs.k8s.io/) et [kind](https://kind.sigs.k8s.io/). 

Pour extraire des images vers votre cluster Kubernetes à partir d’un registre de conteneurs Azure, vous devez établir un mécanisme d’authentification et d’autorisation. En fonction de votre environnement de cluster, choisissez l’une des méthodes suivantes :

## <a name="scenarios"></a>Scénarios

| Cluster Kubernetes |Méthode d'authentification  | Description  | Exemple | 
|---------|---------|---------|----------|
| Cluster AKS |Identité managée AKS    |  Activez l'[identité managée](../aks/use-managed-identity.md) AKS kubelet pour extraire des images à partir d’un registre de conteneurs Azure attaché.<br/><br/> Le registre et le cluster peuvent se trouver dans un abonnement Azure identique ou différent, mais doivent être dans le même locataire Azure Active Directory.      | [S’authentifier auprès d’Azure Container Registry à partir d’Azure Kubernetes Service](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)| 
| Cluster AKS | Principal de service AKS     | Activez le [principal de service AKS](../aks/kubernetes-service-principal.md) avec des autorisations sur un registre de conteneurs Azure cible.<br/><br/>Le registre et le cluster peuvent se trouver dans un abonnement Azure identique ou différent dans le même locataire Azure Active Directory.        | [Extraire des images à partir d’un registre de conteneurs Azure vers un cluster AKS dans un locataire Active Directory différent](authenticate-aks-cross-tenant.md)
| Cluster Kubernetes autre que AKS |Pod [imagePullSecrets](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)   |  Utilisez le mécanisme Kubernetes général pour gérer les informations d’identification du Registre pour les déploiements de pods.<br/><br/>Configurez le principal de service Active Directory, le jeton délimité par le référentiel ou d’autres [informations d’identification du Registre](container-registry-authentication.md) prises en charge.  | [Extraire des images depuis un registre de conteneurs Azure vers un cluster Kubernetes à l’aide d’un secret d’extraction](container-registry-auth-kubernetes.md) | 



## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la procédure d'[authentification avec un registre de conteneurs Azure](container-registry-authentication.md)
