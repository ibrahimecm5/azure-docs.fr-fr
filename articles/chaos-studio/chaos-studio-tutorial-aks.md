---
title: Créer une expérience qui utilise une erreur Chaos Mesh AKS avec Azure Chaos Studio
description: Créer une expérience qui utilise une erreur AKS Chaos Mesh
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e9a9b458343d54b9adadc07cfc009be43dc4b609
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337829"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods"></a>Créer une expérience chaos qui utilise une erreur Chaos Mesh pour tuer les pods AKS

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer des défaillances périodiques du pod Azure Kubernetes Service sur un espace de noms à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous défendre contre l’indisponibilité du service en cas de défaillances sporadiques.

Azure Chaos Studio utilise la [Chaos Mesh](https://chaos-mesh.org/), une plateforme d’ingénierie chaos libre et open source pour Kubernetes qui permet d’injecter des erreurs dans un cluster AKS. Les erreurs Chaos Mesh sont des erreurs de [service-direct](chaos-studio-tutorial-aks.md) qui nécessitent l’installation de Chaos Mesh sur le cluster AKS. Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience pour toute erreur AKS Chaos Mesh.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un cluster AKS. Si vous ne disposez pas de cluster AKS, vous pouvez [suivre ces étapes pour en créer un](../aks/kubernetes-walkthrough-portal.md).

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>Configurer Chaos Mesh sur votre cluster AKS

Avant de pouvoir exécuter des erreurs Chaos Mesh dans Chaos Studio, vous devez installer Chaos Mesh sur votre cluster AKS.

1. Exécutez les commandes suivantes dans une fenêtre [Azure Cloud Shell](../cloud-shell/overview.md) dans laquelle l’abonnement actif est défini comme étant l’abonnement dans lequel votre cluster AKS est déployé. Remplacez `$RESOURCE_GROUP` et `$CLUSTER_NAME` par le groupe de ressources et le nom de votre ressource de cluster.

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. Vérifiez que les pods Chaos Mesh sont installés en exécutant la commande suivante :

```bash
kubectl get po -n chaos-testing
```

La sortie doit ressembler à ce qui suit (un chaos-controller-manager et un ou plusieurs chaos-daemons) :

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

Vous pouvez également [utiliser les instructions d’installation sur le site Web de Chaos Mesh](https://chaos-mesh.org/docs/production-installation-using-helm/).


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>Activer Chaos Studio sur votre cluster AKS

Chaos Studio ne peut pas injecter d’erreurs sur une ressource, sauf si elle a d’abord été intégrée à Chaos Studio. Vous intégrez une ressource à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource. Les clusters AKS ne possèdent qu’un seul type de cible (service-direct), mais d’autres ressources peuvent avoir jusqu’à deux types cibles, l’un pour les erreurs de service-direct et l’autre pour les erreurs basées sur l’agent. Chaque type d’erreur de Chaos Mesh est représenté sous la forme d’une fonctionnalité (PodChaos, NetworkChaos, IOChaos, etc.).

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Recherchez **Chaos Studio (version préliminaire)** dans la barre de recherche.
3. Cliquez sur **Cibles** et accédez à votre cluster AKS.
![Vue des cibles dans le Portail Azure](images/tutorial-aks-targets.png)
4. Cochez la case à côté de votre cluster AKS et cliquez sur **Activer les cibles**, puis **activez les cibles service-direct** dans le menu déroulant.
![Activation des cibles dans le Portail Azure](images/tutorial-aks-targets-enable.png)
5. Une notification s’affiche pour indiquer que la ou les ressources sélectionnées ont été activées correctement.
![Notification indiquant que la cible a été activée correctement](images/tutorial-aks-targets-enable-confirm.png)

Vous avez maintenant correctement intégré votre cluster AKS à Chaos Studio. Dans la vue **Cibles**, vous pouvez également gérer les fonctionnalités activées sur cette ressource. En cliquant sur le lien **Gérer les actions** à côté d’une ressource, vous affichez les fonctionnalités activées pour cette ressource.

## <a name="create-an-experiment"></a>Créer une expérience
Une fois votre cluster AKS intégré, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Cliquez sur l’onglet **Expériences** dans Chaos Studio. Dans cette vue, vous pouvez afficher et gérer toutes vos expériences de chaos. Cliquez sur **Ajouter une expérience**
![Vue des expériences dans le Portail Azure](images/tutorial-aks-add.png)
2. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, là où vous souhaitez déployer l’expérience de chaos. Donnez un **nom** à votre expérience. Cliquez sur **Suivant : concepteur d’expérience >** 
![Ajout des détails de l’expérience de base](images/tutorial-aks-add-basics.png)
3. Vous êtes maintenant dans le concepteur d’expérience Chaos Studio. Le concepteur d’expérience vous permet de créer votre expérience en ajoutant des étapes, des branches et des erreurs. Donnez un nom convivial à votre **étape** et votre **branche**, puis cliquez sur **Ajouter une erreur**.
![Concepteur d’expériences](images/tutorial-aks-add-designer.png)
4. Sélectionnez **AKS Chaos Mesh Pod Chaos** dans la liste déroulante, puis saisissez la **Durée** en indiquant la durée de l’échec et **jsonSpec** avec les informations ci-dessous :

    Pour formuler votre Chaos Mesh jsonSpec :
    1. Consultez la documentation Chaos Mesh pour un type d’erreur, [par exemple, le type PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files).
    2. Formulez la configuration YAML pour ce type d’erreur à l’aide de la documentation Chaos Mesh.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: one
          duration: '30s'
          selector:
            namespaces:
              - default
        ```
    3. Supprimez toute configuration YAML en dehors de `spec` (y compris le nom de la propriété Spec) et supprimez la mise en retrait des détails de la spécification.

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. Utilisez un [convertisseur YAML vers JSON comme celui-ci](https://www.convertjson.com/yaml-to-json.htm) pour convertir la configuration YAML de Chaos Mesh en JSON et la réduire.

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. Collez le code JSON réduit dans le champ **json** du portail.




Cliquez sur **Suivant : ressources cibles >** 
![Propriétés d’erreur](images/tutorial-aks-add-fault.png)
5. Sélectionnez votre cluster AKS, puis cliquez sur **Suivant**
![Ajouter une cible](images/tutorial-aks-add-targets.png)
6. Vérifiez que votre expérience semble correcte, puis cliquez sur **Vérifier + créer**, puis sur **Créer.** 
![Vérifier et créer une expérience](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>Accorder une autorisation d’expérience à votre cluster AKS
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement.

1. Accédez à votre cluster AKS, puis cliquez sur **Contrôle d’accès (IAM)** .
![Page de présentation AKS](images/tutorial-aks-access-resource.png)
2. Cliquez sur **Ajouter**, puis sur **Ajouter une attribution de rôle**.
![Présentation du contrôle d’accès](images/tutorial-aks-access-iam.png)
3. Recherchez **le rôle Administrateur de cluster Azure Kubernetes Service** et sélectionnez le rôle. Cliquez sur **Suivant**
![Affectation du rôle d’administrateur de cluster AKS](images/tutorial-aks-access-role.png)
4. Cliquez sur **Sélectionner des membres** et recherchez le nom de votre expérience. Sélectionnez votre expérience, puis cliquez sur **Sélectionner**. S’il existe plusieurs expériences dans le même locataire avec le même nom, le nom de votre expérience sera tronqué avec des caractères aléatoires ajoutés.
![Ajout de l’expérience au rôle](images/tutorial-aks-access-experiment.png)
5. Cliquez sur **Vérifier + attribuer**, puis sur **Vérifier + attribuer**.

## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir la vue d’ensemble de votre cluster AKS et d’accéder à **Informations** dans un autre onglet. Les données actives pour le **Nombre de Pod actif** indiquent l’impact de l’exécution de votre expérience.

1. Dans **Expériences**, cliquez sur votre expérience, sur **Démarrer**, puis sur **OK**.
![Démarrage d’une expérience](images/tutorial-aks-start.png)
2. Lorsque l'**État** passe à **En cours d’exécution**, cliquez sur **Détails** pour la dernière exécution sous **Historique** pour afficher les détails de l’expérience en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience service-direct AKS Chaos Mesh, vous êtes prêt à :
- [Créer une expérience qui utilise des erreurs basées sur un agent](chaos-studio-tutorial-agent-based-portal.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)
