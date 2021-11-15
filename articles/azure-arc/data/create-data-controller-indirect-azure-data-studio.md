---
title: Créer un contrôleur de données dans Azure Data Studio
description: Créer un contrôleur de données dans Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a42b0ff8a23f3504b642ab79a1b85718aa0c62e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131557753"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Créer un contrôleur de données dans Azure Data Studio

Vous pouvez créer un contrôleur de données en utilisant Azure Data Studio via l’Assistant Déploiement et des notebooks.


## <a name="prerequisites"></a>Prérequis

- Vous devez avoir accès à un cluster Kubernetes et votre fichier kubeconfig doit être configuré pour pointer vers le cluster Kubernetes sur lequel vous voulez déployer.
- Vous devez [installer les outils clients](install-client-tools.md), notamment **Azure Data Studio**, les extensions Azure Data Studio appelées **Azure Arc** et l’interface Azure CLI avec l’extension `arcdata`.
- Vous devez vous connecter à Azure dans Azure Data Studio.  Pour cela, tapez Ctrl/Cmd+Maj+P pour ouvrir la fenêtre de texte de commande, puis tapez **Azure**.  Choisissez **Azure : Sign in**.   Dans le volet qui s’affiche, cliquez sur l’icône + en haut à droite pour ajouter un compte Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Utiliser l’Assistant Déploiement pour créer un contrôleur de données Azure Arc

Suivez ces étapes pour créer un contrôleur de données Azure Arc en utilisant l’Assistant Déploiement.

1. Dans Azure Data Studio, cliquez sur l’onglet Connexions dans le volet de navigation gauche.
1. Cliquez sur le bouton **...** en haut du panneau Connexions, puis choisissez **Nouveau déploiement...**
1. Dans le nouvel Assistant Déploiement, choisissez **Contrôleur de données Azure Arc** et cliquez sur le bouton **Sélectionner** situé en bas.
1. Vérifiez que les outils requis sont disponibles et satisfont aux versions requises. Cliquez sur **Suivant**.
1. Utilisez le fichier kubeconfig par défaut ou sélectionnez-en un autre.  Cliquez sur **Suivant**.
1. Choisissez un contexte de cluster Kubernetes. Cliquez sur **Suivant**.
1. Choisissez un profil de configuration de déploiement en fonction de votre cluster Kubernetes cible. Cliquez sur **Suivant**.
1. Choisissez l’abonnement et le groupe de ressources souhaités.
1. Sélectionnez un emplacement Azure.
   
   L’emplacement Azure sélectionné ici est l’emplacement dans Azure où les *métadonnées* sur le contrôleur de données et les instances de base de données qu’il gère seront stockées. Les instances de contrôleur de données et de base de données seront créées sur votre cluster Kubernetes, où qu’il soit.
   
   Cliquez sur **Suivant** lorsque vous avez terminé.

1. Entrez un nom pour le contrôleur de données et pour l’espace de noms dans lequel le contrôleur de données sera créé.

    Le nom du contrôleur de données et de l’espace de noms sont utilisés pour créer une ressource personnalisée dans le cluster Kubernetes : ils doivent donc être conformes aux [conventions de nommage de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Si l’espace de noms existe déjà, il sera utilisé si l’espace de noms ne contient pas déjà d’autres objets Kubernetes, comme des pods, etc.  Si l’espace de noms n’existe pas, une tentative de création de l’espace de noms sera effectuée.  La création d’un espace de noms dans un cluster Kubernetes nécessite des privilèges d’administrateur du cluster Kubernetes.  Si vous ne disposez pas de privilèges d’administrateur du cluster Kubernetes, demandez à l’administrateur de votre cluster Kubernetes d’effectuer les premières étapes décrites dans l’article [Créer un contrôleur de données en utilisant des outils natifs Kubernetes](./create-data-controller-using-kubernetes-native-tools.md) qui doivent être exécutées par un administrateur Kubernetes avant que vous puissiez utiliser cet Assistant.


1. Sélectionnez la classe de stockage dans laquelle le contrôleur de données sera déployé. 
1.  Entrez un nom d’utilisateur et un mot de passe, puis confirmez le mot de passe pour le compte d’administrateur du contrôleur de données. Cliquez sur **Suivant**.

1. Examinez la configuration du déploiement.
1. Cliquez sur **Déployer** pour déployer la configuration souhaitée ou l’option **Script en bloc-notes** pour consulter les instructions de déploiement ou apporter les modifications nécessaires, comme les noms de classes de stockage ou les types de services. Cliquez sur **Exécuter tout** en haut du notebook.

## <a name="monitoring-the-creation-status"></a>Surveillance de l’état de la création

La création du contrôleur prend plusieurs minutes. Vous pouvez superviser la progression dans une autre fenêtre de terminal avec les commandes suivantes :

> [!NOTE]
>  Les exemples de commandes ci-dessous supposent que vous avez créé un contrôleur de données et un espace de noms Kubernetes avec le nom « arc ».  Si vous avez utilisé un autre nom pour le contrôleur de données/espace de noms, vous pouvez remplacer « arc » par ce nom.

```console
kubectl get datacontroller --namespace arc
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
