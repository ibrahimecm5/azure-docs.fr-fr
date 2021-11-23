---
title: 'Démarrage rapide : Déployer votre première application conteneur avec le portail Azure'
description: Déployez votre première application dans la préversion d’Azure Container Apps en utilisant le portail Azure.
services: container-apps
author: cebundy
ms.service: container-apps
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: v-bcatherine
ms.custom: ''
ms.openlocfilehash: dc0550e4e32e84bbf99456a337a66c3da8f6cf57
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526370"
---
# <a name="quickstart-deploy-your-first-container-app-using-the-azure-portal"></a>Démarrage rapide : Déployer votre première application conteneur avec le portail Azure

La préversion d’Azure Container Apps vous permet d’exécuter des microservices et des applications conteneurisées sur une plateforme serverless. Avec Container Apps, vous bénéficiez des avantages de l’exécution de conteneurs tout en évitant les soucis de configuration manuelle de l’infrastructure cloud et des orchestrateurs de conteneurs complexes.

Dans ce guide de démarrage rapide, vous créez un environnement Container Apps sécurisé et déployez votre première application conteneur en utilisant le portail Azure.

## <a name="prerequisites"></a>Prérequis

Un compte Azure avec un abonnement actif est requis. Si vous n’en avez pas déjà un, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="setup"></a>Programme d’installation

Commencez par vous connecter au [portail Azure](https://portal.azure.com).
<!--
Do we need to include steps to login?  Probably not..
-->

## <a name="create-a-container-app"></a>Créer une application de conteneur

Pour créer votre application conteneur, commencez sur la page d’accueil du portail Azure.

1. Dans la barre de recherche supérieure, recherchez **Applications conteneur**.
1. Dans les résultats de la recherche, sélectionnez **Applications conteneur**.
1. Cliquez sur le bouton **Créer**.

### <a name="basics-tab"></a>Onglet Informations de base

Sous l’onglet *Informations de base*, effectuez les actions suivantes.

#### <a name="enter-project-details"></a>Entrer les détails du projet

| Paramètre | Action |
|---|---|
| Abonnement | Sélectionnez votre abonnement Azure. |
| Resource group | Sélectionnez **Créer** et entrez **my-container-apps**. |
| Nom de l’application conteneur |  Entrez **my-container-app**. |

#### <a name="create-an-environment"></a>Créer un environnement
 
1. Dans le champ *Créer un environnement d’application conteneur*, sélectionnez **Créer**.
1. Dans la page *Créer un environnement d’application conteneur* sous l’onglet *Informations de base*, entrez les valeurs suivantes :

    | Paramètre | Valeur |
    |---|---|
    | Nom de l’environnement | Entrez **my-environment**. | 
    | Région | Sélectionnez **Canada Centre**. |

1. Sélectionnez **Supervision** pour créer un espace de travail Log Analytics.
1. Sélectionnez **Créer** dans le champ *Espace de travail Log Analytics*.
1. Entrez **my-container-apps-logs** dans le champ *Nom* de la boîte de dialogue *Créer un espace de travail Log Analytics*.
  
    Le champ *Localisation* est pré-rempli avec *Canada Centre*.
1. Sélectionnez **OK**.
1. Sélectionnez le bouton **Créer** au bas de la page *Créer un environnement d’application conteneur*.
1. Sélectionnez le bouton **Suivant : Paramètres de l’application** en bas de la page.

### <a name="app-settings-tab"></a>Onglet Paramètres de l’application

Sous l’onglet *Paramètres de l’application*, effectuez les actions suivantes :

| Paramètre | Action |
|---|---|
| Utiliser l’image de démarrage rapide | **Décochez** la case. |
| Name | Entrez **my-app**. <!-- I don't know what name to use --> |
| Source d’image | Sélectionnez **Docker Hub ou d’autres registres**. |
| Type d’image | Sélectionnez **Public**. |
| Serveur de connexion au registre | Entrez `mcr.microsoft.com`. |  
| Image et étiquette | Entrez **/azuredocs/containerapps-helloworld:latest**. |

#### <a name="application-ingress-settings"></a>Paramètres d’entrée d’application

| Paramètre | Action |
|---|---|
| Entrée | Sélectionnez **Enabled**. |
| Visibilité de l’entrée | Sélectionnez **Externe**. |
| Port cible | Entrez **80**. |

### <a name="deploying-the-container-app"></a>Déploiement de l’application conteneur

1. Sélectionnez le bouton **Vérifier et créer** au bas de la page.  

    Ensuite, les paramètres de l’application conteneur sont vérifiés. Si aucune erreur n’est rencontrée, le bouton *Créer* est activé.  

    En cas d’erreur, tout onglet contenant des erreurs est marqué d’un point rouge.  Accédez à l’onglet approprié. Les champs contenant une erreur sont mis en évidence en rouge.  Une fois toutes les erreurs résolues, resélectionnez **Vérifier et créer**.

1. Sélectionnez **Create** (Créer).

    Une page contenant le message *Le déploiement est en cours* s’affiche.  Une fois le déploiement terminé, le message « Votre déploiement est terminé » s’affiche.

### <a name="view-your-deployed-application"></a>Afficher votre application déployée

Sélectionnez **Accéder à la ressource** pour afficher votre nouvelle application conteneur.  Sélectionnez le lien en regard d’*URL de l’application* pour afficher votre application. Le message suivant s’affiche dans votre navigateur.

:::image type="content" source="media/get-started/azure-container-apps-quickstart.png" alt-text="Votre premier déploiement Azure Container Apps.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette application, vous pouvez supprimer l’instance Azure Container Apps et tous les services associés en supprimant le groupe de ressources.

1. Sélectionnez le groupe de ressources **my-container-apps** dans la section *Vue d’ensemble*.
1. Sélectionnez le bouton **Supprimer un groupe de ressources** en haut de la *Vue d’ensemble* du groupe de ressources.
1. Entrez le nom du groupe de ressources **my-container-apps** dans la boîte de dialogue de confirmation *Voulez-vous vraiment supprimer « my-container-apps »* .
1. Sélectionnez **Supprimer**.  
    Le processus de suppression du groupe de ressources peut prendre quelques minutes.


> [!TIP]
> Vous rencontrez des problèmes ? Faites-le nous savoir sur GitHub en ouvrant un problème dans le [dépôt Azure Container Apps](https://github.com/microsoft/azure-container-apps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Environnements dans Azure Container Apps](environment.md)
