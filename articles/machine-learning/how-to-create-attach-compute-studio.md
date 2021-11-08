---
title: Créer des calculs de formation et de déploiement (studio)
titleSuffix: Azure Machine Learning
description: Utilisez Studio pour créer des ressources de calcul d’entraînement et de déploiement (cibles de calcul) pour le Machine Learning.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.openlocfilehash: 669b14511c13ac1571082507ad8c16ed6b571600
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556537"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Créer des cibles de calcul pour l’entraînement et le déploiement de modèle dans Azure Machine Learning Studio

Dans cet article, vous allez découvrir comment créer et gérer des cibles de calcul dans Azure Machine Learning Studio.  Vous pouvez également créer et gérer des cibles de calcul avec :

* Le SDK Azure Machine Learning ou l’extension CLI pour Azure Machine Learning
  * [Instance de calcul](how-to-create-manage-compute-instance.md)
  * [Cluster de calcul](how-to-create-attach-compute-cluster.md)
  * [Cluster Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
  * [Autres ressources de calcul](how-to-attach-compute-targets.md)
* L’[extension VS Code](how-to-manage-resources-vscode.md#compute-clusters) pour Azure Machine Learning.

> [!IMPORTANT]
> Les éléments marqués (préversion) dans cet article sont actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/) dès aujourd’hui
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Qu’est-ce qu’une cible de calcul ? 

Azure Machine Learning vous permet de faire l’apprentissage de votre modèle sur une variété de ressources ou d’environnements, appelés collectivement [__cibles de calcul__](concept-azure-machine-learning-architecture.md#compute-targets). Une cible de calcul peut être un ordinateur local ou une ressource cloud, comme une capacité de calcul Azure Machine Learning, Azure HDInsight ou une machine virtuelle distante.  Vous pouvez également créer des cibles de calcul pour le déploiement de modèle, comme décrit dans [« Déployer des modèles avec le service Azure Machine Learning »](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Afficher les cibles de calcul

Pour afficher toutes les cibles de calcul de votre espace de travail, effectuez les étapes suivantes :

1. Accédez à [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Sous __Gérer__, sélectionnez __Calcul__.

1. Sélectionnez des onglets en haut pour afficher chaque type de cible de calcul.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Affichez la liste des cibles de calcul":::

## <a name="start-creation-process"></a><a id="portal-create"></a>Démarrer le processus de création

Suivez la procédure ci-dessus pour afficher la liste des cibles de calcul. Puis procédez comme suit pour créer une cible de calcul :

1. Sélectionnez l’onglet en haut correspondant au type de calcul que vous allez créer.

1. Si vous n’avez aucune cible de calcul, sélectionnez  **Créer** au milieu de la page.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Créer une cible de calcul":::

1. Si vous voyez une liste de ressources de calcul, sélectionnez **+ Nouveau** au-dessus de la liste.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Sélectionnez Nouveau":::


1. Remplissez le formulaire correspondant à votre type de calcul :

    * [Instance de calcul](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)
    * [Clusters de calcul](#amlcompute)
    * [Clusters d’inférence](#inference-clusters)
    * [Calcul attaché](#attached-compute)

1. Sélectionnez __Create__ (Créer).

1. Pour afficher l’état de l’opération de création, sélectionnez la cible de calcul dans la liste :

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Affichez l’état du calcul dans une liste":::

Suivez les étapes décrites dans [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md?tabs=azure-studio#create).


## <a name="create-compute-clusters"></a><a name="amlcompute"></a> Créer des clusters de calcul

Créez un cluster de calcul à un seul ou plusieurs nœuds pour vos charges de travail d’entraînement, d’inférence de lot ou d’apprentissage par renforcement. Utilisez les [étapes ci-dessus](#portal-create) pour créer le cluster de calcul.  Remplissez ensuite le formulaire comme suit :

|Champ  |Description  |
|---------|---------|
| Emplacement | Région Azure où le cluster de calcul est créé. Par défaut, il s’agit du même emplacement que l’espace de travail. La définition de l’emplacement sur une région différente de celle de l’espace de travail est en __préversion__ et n’est disponible que pour les __clusters de calcul__ et pas les instances de calcul.</br>Lorsque vous utilisez une région différente de celle de votre espace de travail ou de vos magasins de données, vous pouvez constater des coûts accrus de latence du réseau et de transfert de données. La latence et les coûts peuvent survenir lors de la création du cluster et lors de l’exécution de travaux sur celui-ci. |
|Type de machine virtuelle |  Choisissez UC ou GPU. Vous ne pouvez pas modifier ce type après la création.     |
|Priorité de machine virtuelle | Choisissez **Dédié** ou **Basse priorité**.  Les machines virtuelles basse priorité sont moins chères, mais ne garantissent pas les nœuds de calcul. Votre travail peut être anticipé.
|Taille de la machine virtuelle     |  Il se peut que les tailles de machines virtuelles prises en charge soient restreintes dans votre région. Vérifier la [liste de disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |

Sélectionnez **Suivant** pour accéder aux **Paramètres avancés** et renseigner le formulaire comme suit :

|Champ  |Description  |
|---------|---------|
|Nom du calcul     |  <li>Le nom est obligatoire et doit comprendre entre 3 et 24 caractères.</li><li>Les caractères valides sont les lettres majuscules et minuscules, les chiffres et le caractère **-** .</li><li>Le nom doit commencer par une lettre.</li><li>Le nom doit être unique parmi tous les calculs existants au sein d’une région Azure. Si le nom que vous choisissez n’est pas unique, une alerte s’affiche.</li><li>Si le caractère **-** est utilisé, il doit être suivi d’au moins une lettre plus loin dans le nom.</li>     |
|Nombre minimal de nœuds | Nombre minimal de nœuds que vous souhaitez provisionner. Si vous souhaitez un nombre dédié de nœuds, spécifiez-le ici. Faites des économies en affectant la valeur 0 comme minimum. Ainsi, vous ne paierez aucun nœud lorsque le cluster sera inactif. |
|Nombre maximal de nœuds | Nombre maximal de nœuds que vous souhaitez provisionner. Le calcul sera automatiquement mis à l’échelle jusqu’à ce nombre maximal de nœuds lors de l’envoi d’un travail. |
| Secondes d’inactivité avant le scale-down | Durée d'inactivité avant le scale-down du cluster jusqu'au nombre minimum de nœuds. |
| Activer l’accès SSH | Utilisez les mêmes instructions que pour [Activer l’accès SSH](#enable-ssh) pour une instance de calcul (ci-dessus). |
|Paramètres avancés     |  facultatif. Configurez un réseau virtuel. Pour créer l’instance de calcul à l’intérieur d’un réseau virtuel Azure (vnet), spécifiez le **Groupe de ressources**, le **Réseau virtuel** et le **Sous-réseau**. Pour plus d’informations, consultez la [configuration requise](./how-to-secure-training-vnet.md) pour le réseau virtuel.   Attachez également des [identités managées](#managed-identity) pour accorder l’accès aux ressources.     |

### <a name="enable-ssh-access"></a><a name="enable-ssh"></a> Activer l’accès SSH

L’accès SSH est désactivé par défaut.  Il n’est pas possible de changer l’accès SSH après sa création. Veillez à activer l’accès si vous envisagez de déboguer de manière interactive avec [VS Code Remote](how-to-set-up-vs-code-remote.md)  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

Une fois le cluster de calcul créé et en cours d’exécution, consultez [Se connecter avec un accès SSH](#ssh-access).

### <a name="set-up-managed-identity"></a><a name="managed-identity"></a>Configurer une identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Lors de la création du cluster de calcul ou de la modification de ses détails, dans les **Paramètres avancés**, activez **Attribuer une identité managée** et spécifiez une identité affectée par le système ou une identité affectée par l’utilisateur.

### <a name="managed-identity-usage"></a>Utilisation de l’identité managée

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="create-inference-clusters"></a><a name="inference-clusters"></a> Créer des clusters d’inférence

> [!IMPORTANT]
> L’utilisation du service Azure Kubernetes avec Azure Machine Learning offre plusieurs options de configuration. Certains scénarios, tels que la mise en réseau, nécessitent une installation et une configuration supplémentaires. Pour plus d’informations sur l’utilisation d’AKS avec Azure ML, consultez [Créer et attacher un cluster Azure Kubernetes Service](how-to-create-attach-kubernetes.md).

Créez ou attachez un cluster AKS (Azure Kubernetes service) pour l’inférence à grande échelle. Utilisez les [étapes ci-dessus](#portal-create) pour créer le cluster AKS.  Remplissez ensuite le formulaire comme suit :


|Champ  |Description  |
|---------|---------|
|Nom du calcul     |  <li>Le nom est obligatoire. Le nom doit faire entre 2 et 16 caractères. </li><li>Les caractères valides sont les lettres majuscules et minuscules, les chiffres et le caractère **-** .</li><li>Le nom doit commencer par une lettre.</li><li>Le nom doit être unique parmi tous les calculs existants au sein d’une région Azure. Si le nom que vous choisissez n’est pas unique, une alerte s’affiche.</li><li>Si le caractère **-** est utilisé, il doit être suivi d’au moins une lettre plus loin dans le nom.</li>     |
|Kubernetes Service | Sélectionnez **Créer** et remplissez le reste du formulaire.  Ou sélectionnez **Utiliser l’existant**, puis sélectionnez un cluster AKS existant dans votre abonnement.
|Région |  Sélectionnez l’emplacement où sera créé le cluster. |
|Taille de la machine virtuelle     |  Il se peut que les tailles de machines virtuelles prises en charge soient restreintes dans votre région. Vérifier la [liste de disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Objectif du cluster  | Sélectionnez **Production** ou **Dev-test**. |
|Nombre de nœuds | Le nombre de nœuds multiplié par le nombre de cœurs (processeurs virtuels) de la machine virtuelle doit être supérieur ou égal à 12. |
| Configuration réseau | Sélectionnez **Avancé** pour créer le calcul au sein d’un réseau virtuel existant. Pour plus d’informations sur AKS dans un réseau virtuel, consultez [Isolement réseau pendant l’entraînement et l’inférence avec les points de terminaison privés et les réseaux virtuels privés](./how-to-secure-inferencing-vnet.md). |
| Activer la configuration SSL | Utilisez ceci pour configurer le certificat SSL sur le calcul. |

## <a name="attach-other-compute"></a><a name="attached-compute"></a> Attacher un autre calcul

Pour utiliser des cibles de calcul créées en dehors de l’espace de travail Azure Machine Learning, vous devez les joindre. Une fois la cible de calcul jointe, elle sera disponible dans votre espace de travail.  Utilisez **Calcul attaché** afin d’attacher une cible de calcul pour l’**entraînement**.  Utilisez **Clusters d’inférence** afin d’attacher un cluster AKS pour l’**inférence**.

Utilisez les [étapes ci-dessus](#portal-create) pour attacher un calcul.  Remplissez ensuite le formulaire comme suit :

1. Entrez un nom pour la cible de calcul. 
1. Sélectionnez le type de calcul à attacher. Il n’est pas possible d’attacher tous les types de capacités de calcul à partir d’Azure Machine Learning Studio. Actuellement, les types qui peuvent être attachés pour l’entraînement sont les suivants :
    * Une machine virtuelle Azure (pour attacher un environnement Data Science Virtual Machine)
    * Azure Databricks (pour une utilisation dans des pipelines de Machine Learning)
    * Azure Data Lake Analytics (pour une utilisation dans des pipelines de Machine Learning)
    * Azure HDInsight
    * Kubernetes (préversion)

1. Remplissez le formulaire et indiquez une valeur pour les propriétés requises.

    > [!NOTE]
    > Microsoft recommande d’utiliser des clés SSH, car elles sont plus sûres que les mots de passe. Les mots de passe sont vulnérables aux attaques en force brute. Les clés SSH utilisent des signatures de chiffrement. Pour plus d’informations sur la création de clés SSH pour une utilisation avec Machines virtuelles Azure, consultez les documents suivants :
    >
    > * [Créer et utiliser des clés SSH sur Linux ou macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Créer et utiliser des clés SSH sur Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Sélectionnez __Attacher__.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

> [!IMPORTANT]
> Pour attacher un cluster Azure Kubernetes Services (AKS) ou Kubernetes avec Azure Arc, vous devez être propriétaire de l’abonnement ou être autorisé à accéder aux ressources de cluster AKS de l’abonnement. Dans le cas contraire, la liste de clusters de la page « attacher un nouveau calcul » est vide.

Pour détacher votre calcul, procédez comme suit :

1. Dans Azure Machine Learning Studio, sélectionnez __Calcul__, __Calcul attaché__ et le calcul que vous souhaitez supprimer.
1. Utilisez le lien __Détacher__ pour détacher votre calcul.

## <a name="connect-with-ssh-access"></a><a name="ssh-access"></a> Se connecter avec accès SSH

Si vous avez créé une instance de calcul ou un cluster de calcul avec l’accès SSH activé, procédez comme suit.

1. Recherchez le calcul dans les ressources de l’espace de travail :
    1. Sur la gauche, sélectionnez **Calcul**.
    1. Utilisez les onglets situés en haut pour sélectionner **Instance de calcul** ou **Cluster de calcul** et rechercher votre machine.
1. Sélectionnez le nom du calcul dans la liste des ressources.
1. Recherchez la chaîne de connexion :

    * Pour une **instance de calcul**, sélectionnez **Connecter** en haut de la section **Détails**.

        :::image type="content" source="media/how-to-create-attach-studio/details.png" alt-text="Capture d’écran : outil Connecter en haut de la page Détails.":::

    * Pour un **cluster de calcul**, sélectionnez **Nœuds** dans la partie supérieure, puis sélectionnez la **Chaîne de connexion** dans la table pour votre nœud.
        :::image type="content" source="media/how-to-create-attach-studio/compute-nodes.png" alt-text="Capture d’écran : chaîne de connexion pour un nœud dans un cluster de calcul.":::

1. Copiez la chaîne de connexion.
1. Pour Windows, ouvrez PowerShell ou une invite de commandes :
   1. Accédez au répertoire ou au dossier où votre clé est stockée.
   1. Ajoutez l’indicateur -i à la chaîne de connexion pour rechercher la clé privée et pointer vers l’emplacement où elle est stockée :
    
      `ssh -i <keyname.pem> azureuser@... (rest of connection string)`

1. Pour les utilisateurs Linux, suivez les étapes de la section [Créer et utiliser une paire de clés SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).
1. Pour SCP, utilisez : 

   `scp -i key.pem -P {port} {fileToCopyFromLocal }  azureuser@yourComputeInstancePublicIP:~/{destination}`

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une cible et l’avoir attachée à votre espace de travail, vous l’utilisez dans votre [configuration d’exécution](how-to-set-up-training-targets.md) avec un objet `ComputeTarget` :

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Utilisez la ressource de calcul pour [soumettre une exécution d’entraînement](how-to-set-up-training-targets.md).
* [Tutoriel : Entraîner un modèle](tutorial-train-models-with-aml.md) utilise une cible de calcul managée pour entraîner un modèle.
* Découvrez comment [optimiser efficacement les hyperparamètres](how-to-tune-hyperparameters.md) afin de générer des modèles plus efficaces.
* Une fois le modèle formé, découvrez [comment et où déployer les modèles](how-to-deploy-and-where.md).
* [Utiliser Azure Machine Learning avec des réseaux virtuels Azure](./how-to-network-security-overview.md)
