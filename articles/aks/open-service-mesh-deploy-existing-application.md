---
title: Intégrer des applications à Open Service Mesh
description: Guide pratique pour intégrer une application à Open Service Mesh
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: 202702623353462bafd77002662a35e7c7b7d2f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066764"
---
# <a name="onboarding-applications-to-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Intégration d’applications au module complémentaire Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

Le guide suivant décrit comment intégrer un microservice Kubernetes à OSM.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cette procédure pas à pas supposent que vous avez déjà activé le module complémentaire OSM AKS pour votre cluster AKS. Si ce n’est pas le cas, consultez l’article [Déployer le module complémentaire OSM AKS](./open-service-mesh-deploy-addon-az-cli.md) avant de poursuivre. En outre, votre cluster AKS doit avoir la version `1.19+` ou une version ultérieure de Kubernetes avec Kubernetes RBAC activé, une connexion `kubectl` doit être établie avec le cluster (si vous avez besoin d’aide pour l’un de ces éléments, consultez le [démarrage rapide d’AKS](./kubernetes-walkthrough.md)) et le module complémentaire AKS OSM doit être installé.

Les ressources suivantes doivent être installées :

- Azure CLI, version 2.20.0 ou ultérieure
- Module complémentaire OSM version 0.11.1 ou ultérieure
- Interface CLI OSM version 0.11.1 ou ultérieure

## <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Vérifier la stratégie de mode de trafic permissif d’Open Service Mesh (OSM)

Le mode de stratégie de trafic permissif d’OSM est un mode dans lequel l’application de la stratégie de trafic [SMI](https://smi-spec.io/) est ignorée. Dans ce mode, OSM découvre automatiquement les services qui font partie du maillage de services et programme des règles de stratégie de trafic sur chaque side-car de proxy Envoy pour pouvoir communiquer avec ces services.

Pour vérifier le mode de trafic permissif actuel d’OSM pour votre cluster, exécutez la commande suivante :

```azurecli-interactive
kubectl get meshconfig osm-mesh-config -n kube-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

Si **enablePermissiveTrafficPolicyMode** est configuré sur **true**, vous pouvez intégrer vos espaces de noms en toute sécurité sans interrompre vos communications service à service. Si **enablePermissiveTrafficPolicyMode** est configuré sur **false**, vous devez vous assurer d’avoir déployé les manifestes de stratégie d’accès au trafic [SMI](https://smi-spec.io/) corrects. Vous devez également vous assurer que vous disposez d’un compte de service représentant chaque service déployé dans l’espace de noms. Pour plus d’informations sur le mode de trafic permissif, consultez et lisez l’article [Mode de stratégie de trafic permissif](https://docs.openservicemesh.io/docs/guides/traffic_management/permissive_mode/).

## <a name="onboard-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Intégrer des applications à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur True

1. Reportez-vous au guide des [exigences relatives aux applications](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/prereqs/) avant d’intégrer des applications.

1. Si une application dans le maillage doit communiquer avec le serveur d’API Kubernetes, l’utilisateur doit l’autoriser explicitement en utilisant l’exclusion de plage d’adresses IP ou en créant une stratégie de sortie.

1. Intégrer des espaces de noms Kubernetes à OSM

    Pour intégrer un espace de noms contenant des applications à gérer par OSM, exécutez la commande `osm namespace add` :

    ```console
    $ osm namespace add <namespace>
    ```

    Par défaut, la commande `osm namespace add` active l’injection automatique de sidecar pour les pods dans l’espace de noms.

    Pour désactiver l’injection automatique de sidecar dans le cadre de l’inscription d’un espace de noms dans le maillage, utilisez `osm namespace add <namespace> --disable-sidecar-injection`.
    Une fois un espace de noms intégré, des pods peuvent être inscrits dans le maillage en configurant l’injection automatique de sidecar. Pour plus d’informations, consultez le document sur l’[injection de sidecar](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/sidecar_injection/) .

1.  Déployer de nouvelles applications ou redéployer des applications existantes

    Par défaut, les nouveaux déploiements dans des espaces de noms intégrés prennent en charge l’injection automatique de sidecar. Ainsi, quand un pod est créé dans un espace de noms managé, OSM injecte automatiquement le proxy sidecar dans le pod.
    Les déploiements existants doivent être redémarrés pour qu’OSM puisse automatiquement injecter le proxy sidecar lors de la recréation du pod. Les pods gérés par un déploiement peuvent être redémarrés avec la commande `kubectl rollout restart deploy`.

    Pour acheminer correctement le trafic spécifique au protocole vers les ports du service, configurez le protocole d’application à utiliser. Pour en savoir plus, reportez-vous au [guide de sélection du protocole d’application](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/app_protocol_selection/) .


## <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Intégrer des applications déployées à la stratégie de trafic permissif d’Open Service Mesh (OSM) configurée sur False

Lorsque la configuration d’OSM pour la stratégie de trafic permissif est définie sur `false`, OSM requiert le déploiement de stratégies d’accès au trafic [SMI](https://smi-spec.io/) explicites pour que la communication de service à service ait lieu au sein de votre cluster. Dans la mesure où OSM utilise des comptes de service Kubernetes pour implémenter des stratégies de contrôle d’accès entre les applications du maillage, appliquez des stratégies d’accès de trafic [SMI](https://smi-spec.io/) pour autoriser le flux de trafic entre les applications.

Pour obtenir des exemples de stratégies SMI, consultez les exemples suivants :
    - [demo/deploy-traffic-specs.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/openservicemesh/osm/blob/release-v0.11/demo/deploy-traffic-target.sh)


#### <a name="removing-namespaces"></a>Suppression d’espaces de noms
Les espaces de noms peuvent être supprimés du maillage OSM avec la commande `osm namespace remove` :

```console
$ osm namespace remove <namespace>
```

> [!NOTE]
>
> - La commande **`osm namespace remove`** indique uniquement à OSM d’arrêter d’appliquer les mises à jour aux configurations du proxy sidecar dans l’espace de noms. Elle **ne supprime pas** les proxys sidecar. Cela signifie que la configuration de proxy existante continue à être utilisée, mais qu’elle n’est pas mise à jour par le plan de contrôle OSM. Si vous souhaitez supprimer les proxys de tous les pods, supprimez les espaces de noms des pods du maillage avec OSM LCI et redéployez les pods ou déploiements correspondants.
