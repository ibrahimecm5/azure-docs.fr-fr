---
title: Créer un contrôleur de données à l’aide des outils Kubernetes
description: Créer un contrôleur de données à l’aide des outils Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2380a10d9a4a054a5d83b2c1096797e41bba7d0f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563603"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Créer un contrôleur de données Azure Arc à l’aide des outils Kubernetes


## <a name="prerequisites"></a>Prérequis

Pour obtenir des informations générales, consultez la rubrique [Créer le contrôleur de données Azure Arc](create-data-controller.md).

Pour créer le contrôleur de données Azure Arc à l’aide des outils Kubernetes, vous devez avoir installé ces derniers.  Les exemples de cet article utilisent `kubectl`, mais des approches similaires peuvent être utilisées avec d’autres outils Kubernetes tels que le tableau de bord Kubernetes, `oc`ou `helm` si vous êtes familiarisé avec ces outils et Kubernetes yaml/json.

[Installer l’outil kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Certaines étapes de création du contrôleur de données Azure Arc indiqué ci-après nécessitent des autorisations d’administrateur de cluster Kubernetes.  Si vous n’êtes pas administrateur de cluster Kubernetes, vous devez faire en sorte que l’administrateur de cluster Kubernetes effectue ces étapes à votre place.

### <a name="cleanup-from-past-installations"></a>Nettoyage des installations précédentes

Si vous avez installé le contrôleur de données Azure Arc dans le passé sur le même cluster et que vous l’avez supprimé, il se peut que des objets de niveau cluster doivent encore être supprimés. Exécutez les commandes suivantes pour supprimer les objets de niveau cluster du contrôleur de données Azure Arc :

```console
# Cleanup azure arc data service artifacts

# Note: not all of these objects will exist in your environment depending on which version of the Arc data controller was installed

# Custom resource definitions (CRD)
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

# Cluster roles and role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole arc:cr-arc-metricsdc-reader
kubectl delete clusterrole arc:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job
kubectl delete clusterrole {namespace}:cr-upgrade-worker
kubectl delete clusterrolebinding {namespace}:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding {namespace}:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job
kubectl delete clusterrolebinding {namespace}:crb-upgrade-worker

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get clusterrolebinding'

# API services
# Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

# June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

# GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get mutatingwebhookconfiguration'
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-{namespace}

```

## <a name="overview"></a>Vue d’ensemble

La création du contrôleur de données Azure Arc présente les étapes générales suivantes :

   > [!IMPORTANT]
   > Certaines étapes ci-dessous requièrent des autorisations d’administrateur de cluster Kubernetes.

1. Créez les définitions de ressource personnalisées pour le contrôleur de données Arc, l’instance gérée Azure SQL et PostgreSQL Hyperscale. 
1. Créez un espace de noms dans lequel le contrôleur de données sera créé. 
1. Créez le service du programme d’amorçage, y compris le jeu de réplicas, le compte de service, le rôle et la liaison de rôle.
1. Créez un secret pour le nom d’utilisateur et le mot de passe de l’administrateur du contrôleur de données.
1. Créez la tâche de déploiement de webhook, le rôle de cluster et la liaison de rôle de cluster. 
1. Créez le contrôleur de données.


## <a name="create-the-custom-resource-definitions"></a>Créer les définitions de ressource personnalisées

Exécutez la commande suivante pour créer les définitions de ressources personnalisées.  

   > [!IMPORTANT]
   > Requiert des autorisations d’administrateur de cluster Kubernetes.

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Créer un espace de noms dans lequel le contrôleur de données sera créé

Exécutez une commande semblable à la suivante pour créer un espace de noms dédié dans lequel le contrôleur de données sera créé.  Dans cet exemple et dans les autres exemples de cet article, un nom d’espace de noms de `arc` sera utilisé. Si vous choisissez d’utiliser un autre nom, utilisez le même nom partout.

```console
kubectl create namespace arc
```
Si vous utilisez OpenShift, vous devrez modifier les annotations `openshift.io/sa.scc.supplemental-groups` et `openshift.io/sa.scc.uid-range` sur l’espace de noms à l’aide de `kubectl edit namespace <name of namespace>`.  Modifiez ces annotations existantes pour qu’elles correspondent à ces ID/plages fsGroup et UID _spécifiques_.

```console
openshift.io/sa.scc.supplemental-groups: 1000700001/10000
openshift.io/sa.scc.uid-range: 1000700001/10000
```

Si d’autres personnes utilisant cet espace de noms ne sont pas des administrateurs de cluster, nous vous recommandons de créer un rôle d’administrateur d’espace de noms et d’accorder ce rôle à ces utilisateurs via une liaison de rôle.  L’administrateur de l’espace de noms doit avoir les autorisations complètes sur l’espace de noms.  Vous trouverez des exemples de liaisons de rôles et des rôles plus précis dans le [dépôt GitHub Azure Arc](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/rbac).

## <a name="create-the-bootstrapper-service"></a>Créer le service du programme d’amorçage

Le service du programme d’amorçage traite les demandes entrantes pour la création, la modification et la suppression de ressources personnalisées telles qu’un contrôleur de données, des instances managées SQL ou des groupes de serveurs PostgreSQL Hyperscale.

Exécutez la commande suivante pour créer un service de programme d’amorçage, un compte de service pour le service du programme d’amorçage, ainsi qu’un rôle et une liaison de rôle pour le compte de service du programme d’amorçage.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Vérifiez que le pod du programme d’amorçage est en cours d’exécution à l’aide de la commande suivante.  Vous devrez peut-être l’exécuter plusieurs fois jusqu’à ce que l’état passe à `Running`.

```console
kubectl get pod --namespace arc
```

Le fichier de modèle bootstrapper.yaml est utilisé par défaut pour extraire l’image de conteneur du programme d’amorçage de Microsoft Container Registry (MCR).  Si votre environnement n’a pas accès directement à Microsoft Container Registry, vous pouvez effectuer les opérations suivantes :
- Suivez les étapes pour [extraire les images de conteneur de Microsoft Container Registry et les transmettre à un registre de conteneurs privé](offline-deployment.md).
- [Créez un secret d’extraction d’image](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) pour votre registre de conteneurs privé.
- Ajoutez une clé secrète d’extraction d’image au conteneur du programme d’amorçage. Voir l'exemple ci-dessous.
- Modifiez l’emplacement de l’image pour l’image du programme d’amorçage. Voir l'exemple ci-dessous.

L’exemple ci-dessous suppose que vous avez créé un nom de secret d’extraction d’image `arc-private-registry`.

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:v1.1.0_2021-11-02 #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-secrets-for-the-metrics-and-logs-dashboards"></a>Créer des secrets pour les tableaux de bord des métriques et des journaux

Vous pouvez spécifier un nom d’utilisateur et un mot de passe utilisés pour l’authentification auprès des tableaux de bord des métriques et des journaux en tant qu’administrateur. Choisissez un mot de passe sécurisé et partagez-le uniquement avec ceux qui doivent disposer de ces privilèges.

Une clé secrète Kubernetes est stockée sous la forme d’une chaîne encodée en base64, une pour le nom d’utilisateur et l’autre pour le mot de passe.

Vous pouvez utiliser un outil en ligne pour coder en base64 le nom d’utilisateur et le mot de passe souhaités, ou vous pouvez utiliser des outils d’interface de ligne de commande intégrés en fonction de votre plateforme.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Une fois que vous avez encodé les noms d’utilisateur et les mots de passe, vous pouvez créer un fichier basé sur le [fichier modèle ](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) et remplacer les noms d’utilisateur et les mots de passe par les vôtres.

Exécutez ensuite la commande ci-dessous pour créer la clé secrète :

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-webhook-deployment-job-cluster-role-and-cluster-role-binding"></a>Créer la tâche de déploiement de webhook, le rôle de cluster et la liaison de rôle de cluster

Tout d'abord, créez une copie du [fichier modèle](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/web-hook.yaml) localement sur votre ordinateur afin de pouvoir modifier certains des paramètres.

Modifiez le fichier et remplacez `{{namespace}}` à tous les emplacements comportant le nom de l’espace de noms que vous avez créé à l’étape précédente. **Enregistrez le fichier .**

Exécutez la commande suivante pour créer le rôle de cluster et les liaisons de rôles de cluster.  

   > [!IMPORTANT]
   > Requiert des autorisations d’administrateur de cluster Kubernetes.

```console
kubectl create -n arc -f <path to the edited template file on your computer>
```


## <a name="create-the-data-controller"></a>Créer le contrôleur de données

Vous êtes maintenant prêt à créer le contrôleur de données lui-même.

Tout d'abord, créez une copie du [fichier modèle](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) localement sur votre ordinateur afin de pouvoir modifier certains des paramètres.

Modifiez les éléments suivants selon les besoins :

**OBLIGATOIRE**
- **location** : Modifiez-le pour qu’il corresponde à l’emplacement Azure dans lequel les _métadonnées_ relatives au contrôleur de données sont stockées.  Vous pouvez consulter la liste des emplacements Azure disponibles dans l’article [Vue d’ensemble de la création d’un contrôleur de données](create-data-controller.md).
- **resourceGroup** : groupe de ressources Azure dans lequel vous souhaitez créer la ressource Azure Data Controller dans Azure Resource Manager.  En général, ce groupe de ressources doit déjà exister, mais il n’est pas nécessaire tant que vous n’avez pas chargé les données vers Azure.
- **subscription** : GUID de l’abonnement Azure dans lequel vous souhaitez créer les ressources Azure.

**RECOMMANDÉ POUR LA RÉVISION ET ÉVENTUELLEMENT LA MODIFICATION DES VALEURS PAR DÉFAUT**
- **storage..className** : classe de stockage à utiliser pour les fichiers de données et les fichiers journaux du contrôleur de données.  En cas de doute sur les classes de stockage disponibles dans votre cluster Kubernetes, vous pouvez exécuter la commande suivante : `kubectl get storageclass`.  La valeur par défaut est `default`, ce qui suppose qu'il existe une classe de stockage nommée `default` et non pas qu’une classe de stockage _est_ utilisée par défaut.  Remarque : Il existe deux paramètres className à définir pour la classe de stockage souhaitée, l’un pour les données et l’autre pour les journaux.
- **serviceType** : Modifiez le type de service en `NodePort` si vous n’utilisez pas de programme d’équilibrage de charge.
- **Sécurité** Pour Azure Red Hat OpenShift ou Red Hat OpenShift Container Platform, remplacez les paramètres `security:` par les valeurs suivantes dans le fichier YAML du contrôleur de données.

```yml
  security:
    allowDumps: false
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
```

**FACULTATIF**
- **nom** : Le nom par défaut du contrôleur de données est `arc`, mais vous pouvez le modifier si vous le souhaitez.
- **displayName** : Affectez-lui la même valeur que l’attribut Name en haut du fichier.
- **registry** : Microsoft Container Registry est le registre par défaut.  Si vous extrayez les images de Microsoft Container Registry et [les transférez vers un registre de conteneurs privé](offline-deployment.md), entrez l’adresse IP ou le nom DNS de votre registre ici.
- **dockerRegistry** : secret d’extraction d’image à utiliser pour extraire les images à partir d’un registre de conteneurs privé, si nécessaire.
- **repository** : `arcdata` est le registre par défaut sur Microsoft Container Registry.  Si vous utilisez un registre de conteneurs privé, entrez le chemin du dossier/référentiel contenant les images de conteneur des services de données avec Azure Arc.
- **imageTag** : l’étiquette de la dernière version actuelle est utilisée par défaut dans le modèle mais vous pouvez la modifier si vous souhaitez utiliser une version antérieure.

L’exemple suivant montre un fichier YAML de contrôleur de données terminé. Mettez à jour l’exemple pour votre environnement, en fonction de vos besoins et des informations ci-dessus.

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-arc-controller
---
apiVersion: arcdata.microsoft.com/v1
kind: DataController
metadata:
  generation: 1
  name: arc-dc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-arc-controller
  docker:
    imagePullPolicy: Always
    imageTag: v1.1.0_2021-11-02
    registry: mcr.microsoft.com
    repository: arcdata
  infrastructure: other #Must be a value in the array [alibaba, aws, azure, gcp, onpremises, other]
  security:
    allowDumps: true #Set this to false if deploying on OpenShift
    allowNodeMetricsCollection: true #Set this to false if deploying on OpenShift
    allowPodMetricsCollection: true #Set this to false if deploying on OpenShift
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc-dc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

enregistrez le fichier modifié sur votre ordinateur local et exécutez la commande suivante pour créer le contrôleur de données :

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Surveillance de l’état de la création

La création du contrôleur prend plusieurs minutes. Vous pouvez superviser la progression dans une autre fenêtre de terminal avec les commandes suivantes :

> [!NOTE]
>  Les exemples de commandes ci-dessous supposent que vous avez créé un contrôleur de données et un espace de noms Kubernetes avec le nom `arc`.  Si vous avez utilisé un autre nom pour le contrôleur de données/espace de noms, vous pouvez remplacer `arc` par ce nom.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Vous pouvez également vérifier l’état de la création de n’importe quel pod en exécutant une commande comme celle qui figure ci-dessous.  C’est particulièrement utile pour résoudre les problèmes.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Résolution des problèmes de création

Si vous rencontrez des problèmes avec la création, consultez le [Guide de résolution des problèmes](troubleshoot-guide.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une instance gérée SQL à l’aide des outils Kubernetes natifs](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Créer un groupe de serveurs PostgreSQL Hyperscale à l’aide des outils Kubernetes natifs](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
