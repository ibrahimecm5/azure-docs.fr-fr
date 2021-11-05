---
title: Extension Dapr pour Azure Kubernetes Service (AKS) (préversion)
description: Installez et configurez Dapr sur votre cluster Azure Kubernetes Service (AKS) à l’aide de l’extension de cluster Dapr.
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: ab1c521be2748c8f58fec4c2f0455e1f08c39bdc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097596"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>Extension Dapr pour Azure Kubernetes Service (AKS) (préversion)

[Dapr](https://dapr.io/) est un runtime portable et piloté par les événements qui permet aux développeurs de créer facilement des applications résilientes, sans état et avec état qui s’exécutent sur le cloud et la périphérie, et qui englobe la diversité des langages et des frameworks de développeur. En tirant parti des avantages d’une architecture side-car, Dapr vous aide à relever les défis liés à la création de microservices et maintient votre code indépendant de la plateforme. Cela permet notamment de résoudre les problèmes liés aux services qui appellent d’autres services de manière fiable et sécurisée, de créer des applications pilotées par les événements avec publication/abonnement et de créer des applications portables sur plusieurs services cloud et hôtes (par exemple, Kubernetes ou une machine virtuelle).

En utilisant l’extension Dapr pour AKS afin de provisionner Dapr sur votre cluster AKS, vous supprimez la surcharge liée au téléchargement des outils Dapr ainsi qu’à l’installation et à la gestion manuelles du runtime sur votre cluster AKS. En outre, l’extension assure la prise en charge de toutes les [fonctionnalités de configuration Dapr natives][dapr-configuration-options] à l’aide d’arguments de ligne de commande simples.

> [!NOTE]
> Si vous prévoyez d’installer Dapr dans un environnement de production Kubernetes, consultez la page de documentation [Instructions Dapr pour une utilisation en production][kubernetes-production].

## <a name="how-it-works"></a>Fonctionnement

L’extension Dapr pour AKS utilise l’interface de ligne de commande Azure pour provisionner le plan de contrôle Dapr sur votre cluster AKS. Cela permet de créer :

- **dapr-operator** : gère les mises à jour des composants et les points de terminaison des services Kubernetes pour Dapr (magasins d’état, publications/abonnements, etc.)
- **dapr-sidecar-injector** : injecte Dapr dans des pods de déploiement annotés et ajoute les variables d’environnement `DAPR_HTTP_PORT` et `DAPR_GRPC_PORT` pour permettre aux applications définies par l’utilisateur de communiquer facilement avec Dapr sans coder de manière irréversible les valeurs de port Dapr.
- **dapr-placement** : utilisé uniquement pour les intervenants. Crée des tables de mappage qui mappent les instances d’intervenant aux pods
- **dapr-sentry** : gère mTLS entre les services et agit en tant qu’autorité de certification. Pour plus d’informations, consultez la [vue d’ensemble de la sécurité][dapr-security].

Une fois Dapr installé sur votre cluster AKS, vos services d’application ont à présent le side-car Dapr qui s’exécute à leur côté. Cela vous permet de commencer immédiatement à utiliser les API de bloc de construction Dapr. Pour une présentation plus détaillée des API de bloc de construction et comment les utiliser au mieux, consultez la [vue d’ensemble des blocs de construction Dapr][building-blocks-concepts].

> [!WARNING]
> Si vous installez Dapr par le biais de l’extension AKS, nous vous recommandons de continuer à utiliser l’extension pour la gestion future de Dapr au lieu de l’interface CLI Dapr. La combinaison des deux outils peut entraîner des conflits et un comportement indésirable.

## <a name="prerequisites"></a>Prérequis 

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli-windows) et de l’extension *aks-preview*.
- Si vous n’en avez pas déjà un, vous devez créer un [cluster AKS][deploy-cluster].


### <a name="register-the-extensions-aks-extensionmanager-and-aks-dapr-preview-features"></a>Inscrire les fonctionnalités en préversion `Extensions`, `AKS-ExtensionManager` et `AKS-Dapr`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Pour créer un cluster AKS qui peut utiliser l’extension Dapr, vous devez activer les indicateurs de fonctionnalité `Extensions`, `AKS-ExtensionManager` et `AKS-Dapr` sur votre abonnement.

Inscrivez les indicateurs de fonctionnalité `Extensions`, `AKS-ExtensionManager` et `AKS-Dapr` à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --namespace "Microsoft.KubernetesConfiguration" --name "Extensions"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vérifiez l’état de l’inscription à l’aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.KubernetesConfiguration/Extensions')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

Quand vous êtes prêt, actualisez l’inscription des fournisseurs de ressources *Microsoft.KubernetesConfiguration* et *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Configurer l’extension Azure CLI pour les extensions de cluster

Vous aurez également besoin de l’extension Azure CLI `k8s-extension`. Installez la en exécutant les commandes suivantes :
  
```azurecli-interactive
az extension add --name k8s-extension
```

Si l’extension `k8s-extension` est déjà installée, vous pouvez la mettre à jour vers la version la plus récente à l’aide de la commande suivante :

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>Créer l’extension et installer Dapr sur votre cluster AKS

Une fois que votre abonnement est inscrit pour utiliser les extensions Kubernetes, vous pouvez créer l’extension Dapr, qui installe Dapr sur votre cluster AKS. Par exemple :

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
```

Vous avez la possibilité de permettre à Dapr de mettre à jour automatiquement sa version mineure en spécifiant le paramètre `--auto-upgrade-minor-version` et en définissant la valeur sur `true` :

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>Paramètres de configuration

L’extension vous permet de définir des options de configuration Dapr à l’aide du paramètre `--configuration-settings`. Par exemple, pour provisionner Dapr avec la haute disponibilité (HA) activée, définissez le paramètre `global.ha.enabled` sur `true` :

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
```

> [!NOTE]
> Si les paramètres de configuration sont sensibles et doivent être protégés, par exemple les informations relatives au certificat, transmettez le paramètre `--configuration-protected-settings` et la valeur sera protégée contre toute lecture.

Si aucun paramètre de configuration n’est transmis, la configuration Dapr par défaut est :

```yaml
  ha:
    enabled: true
    replicaCount: 3
    disruption:
      minimumAvailable: ""
      maximumUnavailable: "25%"
  prometheus:
    enabled: true
    port: 9090
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
```

Pour obtenir la liste des options disponibles, consultez [Configuration Dapr][dapr-configuration-options].

## <a name="targeting-a-specific-dapr-version"></a>Ciblage d’une version spécifique de Dapr

> [!NOTE]
> Dapr est pris en charge avec une fenêtre dynamique, comprenant uniquement les versions actuelles et précédentes. Il est de votre responsabilité opérationnelle de rester à jour avec ces versions prises en charge. Si vous disposez d’une version antérieure de Dapr, vous devrez peut-être effectuer des mises à niveau intermédiaires pour obtenir une version prise en charge.

Le même argument de ligne de commande est utilisé pour installer une version spécifique de Dapr ou restaurer une version antérieure. Définissez `--auto-upgrade-minor-version` sur `false` et `--version` sur la version de Dapr que vous souhaitez installer. Si le paramètre `version` est omis, l’extension installe la dernière version de Dapr. Par exemple, pour utiliser Dapr X.X.X : 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup 
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X \
```

## <a name="show-current-configuration-settings"></a>Afficher les paramètres de configuration actuels

Utilisez la commande `az k8s-extension show` pour afficher les paramètres de configuration Dapr actuels :  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>Mettre à jour les paramètres de configuration du système

> [!NOTE]
> La haute disponibilité (HA) peut être activée à tout moment. Toutefois, une fois activée, sa désactivation nécessite la suppression et la recréation de l’extension. Si vous ne savez pas si la haute disponibilité est nécessaire pour votre cas d’usage, nous vous recommandons de commencer en l’ayant désactivée pour réduire les interruptions.

Pour mettre à jour vos paramètres de configuration Dapr, il vous suffit de recréer l’extension avec l’état souhaité. Par exemple, supposons que nous avons déjà créé et installé l’extension à l’aide de la configuration suivante :

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" 
```

Pour mettre à jour `dapr_operator.replicaCount` de 2 à 3, utilisez la commande suivante :

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=3" 
```

## <a name="troubleshooting-extension-errors"></a>Résolution des problèmes liés aux erreurs d’extension

Si la création ou la mise à jour de l’extension échoue, vous pouvez vérifier l’emplacement où la création de l’extension a échoué en exécutant la commande `az k8s-extension list`. Par exemple, si une clé incorrecte est utilisée dans les paramètres de configuration, comme `global.ha=false` au lieu de `global.ha.enabled=false` : 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

Le code JSON ci-dessous est retourné et le message d’erreur est capturé dans la propriété `message`.

```json
"statuses": [
      {
        "code": "InstallationFailed",
        "displayStatus": null,
        "level": null,
        "message": "Error: {failed to install chart from path [] for release [dapr-1]: err [template: dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml:1:17: executing \"dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml\" at <.Values.global.ha.enabled>: can't evaluate field enabled in type interface {}]} occurred while doing the operation : {Installing the extension} on the config",
        "time": null
      }
],
```

## <a name="delete-the-extension"></a>Supprimer l’extension

Si vous devez supprimer l’extension et retirer Dapr de votre cluster AKS, vous pouvez utiliser la commande suivante : 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>Étapes suivantes

- Une fois que vous avez correctement provisionné Dapr dans votre cluster AKS, essayez de déployer un [exemple d’application][sample-application]

<!-- LINKS INTERNAL -->
[deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS EXTERNAL -->
[kubernetes-production]: https://docs.dapr.io/operations/hosting/kubernetes/kubernetes-production
[building-blocks-concepts]: https://docs.dapr.io/developing-applications/building-blocks/
[dapr-configuration-options]: https://github.com/dapr/dapr/blob/master/charts/dapr/README.md#configuration
[sample-application]: https://github.com/dapr/quickstarts/tree/master/hello-kubernetes#step-2---create-and-configure-a-state-store
[dapr-security]: https://docs.dapr.io/concepts/security-concept/
