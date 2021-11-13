---
title: Déployer un cluster ARO dans le portail Azure
description: Déployer un cluster ARO dans le portail Azure
author: rahulmehta
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: quickstart
ms.date: 10/21/2021
ms.openlocfilehash: 15fe2cc5c9735c28de867510cd5497a892554db8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271159"
---
# <a name="quickstart-deploy-an-azure-red-hat-openshift-aro-cluster-using-the-azure-portal"></a>Démarrage rapide : Déployer un cluster Azure Red Hat OpenShift (ARO) en utilisant le portail Azure

Azure Red Hat OpenShift (ARO) est un service OpenShift managé qui vous permet de déployer et gérer rapidement des clusters. Dans ce guide de démarrage rapide, vous déployez un cluster ARO à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-aro-cluster"></a>Créer un cluster ARO
1.  Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2.  Sélectionnez **Conteneurs** > **Azure Red Hat OpenShift**.
3.  Sur la page **Bases**, configurez les options suivantes :
    * **Détails du projet** :
        *   Sélectionnez un **Abonnement Azure**.
        *   Sélectionnez ou créez un **Groupe de ressources Azure**, par exemple, *myResourceGroup*.
    * **Détails du cluster** :
        * Sélectionnez une **Région** pour le cluster ARO.
        *   Entrez un **nom de cluster** OpenShift, par exemple, *myAROCluster*.
        *   Entrez un **Nom de domaine**.
        *   Sélectionnez la **Taille de la machine virtuelle maître** et la **Taille de la machine virtuelle worker**.

![Onglet **Informations de base** sur le portail Azure](./media/portal-quickstart/basics-tab.jpg)

4.  Dans la page **Authentification**, configurez les options suivantes :
    1) Choisissez **Sélectionner existant** dans le sélecteur de type de principal de service (s’il est déjà présélectionné, aucune action n’est nécessaire et vous pouvez passer aux étapes suivantes).
    2) Recherchez Azure Red Hat open Shift RP et sélectionnez-le. 

>[!NOTE]
>Ignorez le bouton « Créer » et les autres champs facultatifs.

![Onglet **Authentification** sur le portail Azure](./media/portal-quickstart/authentication.jpg)

5.  Sous l’onglet **Réseau**, veillez à configurer :
    * Réseau virtuel
    * Sous-réseau maître
    * Sous-réseau worker
    * Visibilité du serveur d’API
    * Visibilité de l’entrée

![Onglet **Réseau** sur le portail Azure](./media/portal-quickstart/networking.jpg)

6.  Dans la section **Étiquettes**, ajoutez des étiquettes pour organiser vos ressources.

![Onglet **Étiquettes** sur le portail Azure](./media/portal-quickstart/tags.jpg)
 
7.  Cliquez sur **Vérifier + créer**, puis **Créer** lorsque la validation est terminée.

![Onglet **Vérifier + créer** sur le portail Azure](./media/portal-quickstart/review.jpg)
 
8.  La création du cluster ARO prend environ 35-45 minutes. Une fois votre déploiement terminé, accédez à votre ressource en procédant une des façons suivantes :
    *   En cliquant sur **Accéder à la ressource**, ou
    *   Accédez au groupe de ressources du cluster ARO et sélectionnez la ressource ARO.
        *   Par exemple, pour le tableau de bord de cluster ci-dessous : accédez à *myResourceGroup* et sélectionnez la ressource *myAROCluster*.
