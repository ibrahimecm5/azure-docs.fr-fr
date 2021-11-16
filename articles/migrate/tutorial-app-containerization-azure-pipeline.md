---
title: Déploiement continu pour des applications conteneurisées avec Azure DevOps
description: 'Tutoriel : Déploiement continu pour des applications conteneurisées avec Azure DevOps'
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: rahugup
ms.openlocfilehash: 7d23b7c84cc7a7053bfa80b3f6e1b24c3bd4470c
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000612"
---
# <a name="continuous-deployment-for-containerized-applications-with-azure-devops"></a>Déploiement continu pour des applications conteneurisées avec Azure DevOps

Dans ce guide pas à pas, vous allez apprendre à créer un pipeline qui génère et déploie en continu vos applications conteneurisées pour les opérations de jour 2 avec Azure DevOps. Chaque fois que vous modifiez votre code dans un dépôt contenant un Dockerfile, les images sont envoyées (push) à votre registre de conteneurs Azure, puis les manifestes sont déployés sur Azure Kubernetes Service ou Azure App Service.

Azure DevOps vous permet d’héberger, de créer, de planifier et de tester votre code avec des workflows gratuits. L’utilisation d’Azure Pipelines comme l’un de ces workflows vous permet de déployer une application avec CI/CD qui fonctionne avec n’importe quelle plateforme et n’importe quel cloud. Un pipeline est défini en tant que fichier YAML dans le répertoire racine de votre dépôt.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

-  Conteneuriser et déployer votre application web ASP.NET ou Java en utilisant la conteneurisation d’applications Azure Migrate.
-  Un compte GitHub, dans lequel vous pouvez créer un dépôt. Si vous n’en avez pas, vous pouvez en [créer un gratuitement](https://github.com/).
-  Une organisation Azure DevOps. Si vous n’en avez pas, vous pouvez en [créer un gratuitement](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). (Une organisation Azure DevOps est différente de votre organisation GitHub. Vous pouvez donner le même nom à votre organisation DevOps et à votre organisation GitHub si vous souhaitez les aligner.) <br/> Si votre équipe en a déjà une, vérifiez que vous êtes bien un administrateur du projet Azure DevOps que vous souhaitez utiliser.
-  Possibilité d’exécuter des pipelines sur des agents hébergés par Microsoft. Vous pouvez acheter un [travail parallèle](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops) ou demander un niveau gratuit. Pour demander un niveau gratuit, suivez les instructions de [cet article](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops). Notez que l’octroi du niveau gratuit peut nécessiter 2 à 3 jours ouvrables.


## <a name="locate-the-artifacts"></a>Localiser les artefacts

L’outil de conteneurisation d’applications Azure Migrate génère automatiquement des artefacts qui peuvent être utilisés afin de configurer un workflow CI/CD pour votre application avec Azure Pipelines. Les artefacts sont générés une fois le déploiement de l’application terminé par le biais de l’outil. Pour trouver les artefacts, effectuez les étapes suivantes : 

1. Accédez à l’ordinateur exécutant l’outil de conteneurisation d’applications Azure Migrate. 
2. Accédez au répertoire **C:\ProgramData\Microsoft Azure Migrate App Containerization**. Si vous ne parvenez pas à accéder à C:\ProgramData, veillez à sélectionner l’option permettant d’afficher les *Éléments masqués* sous *Affichage* dans l’Explorateur de fichiers. 
3. Sélectionnez le répertoire correspondant **à l’IP ou au nom de domaine complet de l’ordinateur source**. L’ordinateur source est l’ordinateur spécifié dans l’outil de conteneurisation d’applications qui exécute l’application conteneurisée.
4. Pour les applications Java 
    - Accédez à **JavaTomcatWebApp\Artifacts**.    
    - Accédez au répertoire **Catalina\localhost**. Si vous ne le trouvez pas, essayez d’accéder au répertoire correspondant au nom du moteur Tomcat et au nom d’hôte.
    - Localisez le dossier de l’application dans ce répertoire. 
5. Pour les applications ASP.NET
    - Accédez à **IISAspNetWebApp**.
    - Localisez le dossier de l’application dans ce répertoire.


## <a name="upload-artifacts-to-github"></a>Charger des artefacts sur GitHub

Vous devez charger les artefacts dans un dépôt source qui sera utilisé avec Azure DevOps. 

1. Connectez-vous à votre compte GitHub. 
2. Effectuez les étapes décrites dans [cet article](https://docs.github.com/get-started/quickstart/create-a-repo) pour créer un dépôt. 
3. L’étape suivante consiste à charger les artefacts suivants dans ce dépôt.
   -  Pour les applications Java, sélectionnez les dossiers et fichiers suivants dans le dossier de l’application sur l’ordinateur exécutant l’outil de conteneurisation d’applications.
        - Dossier MandatoryArtifacts
        - Dossier manifests
        - Dossier OptionalArtifacts
        - Dockerfile 
        - Fichier Entryscript.sh
        - fichier azure-pipelines.yml
    - Pour les applications ASP.NET, sélectionnez les dossiers et fichiers suivants dans le dossier de l’application sur l’ordinateur exécutant l’outil de conteneurisation d’applications.
        - Dossier manifests
        - Dossier Build
        - fichier azure-pipelines.yml

## <a name="sign-in-to-azure-pipelines"></a>Se connecter à Azure Pipelines

Connectez-vous à [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines). Une fois que vous êtes connecté, votre navigateur accède à https://dev.azure.com/my-organization-name et affiche votre tableau de bord Azure DevOps.

Dans votre organisation sélectionnée, créez un *projet*. Si votre organisation ne compte aucun projet, l’écran **Pour commencer, créez un projet** s’affiche. Dans le cas contraire, sélectionnez le bouton **Créer un project** dans l’angle supérieur droit du tableau de bord.

## <a name="add-service-connections"></a>Ajouter des connexions de service

Avant de créer votre pipeline, vous devez créer vos connexions de service puisque vous serez invité à choisir et à vérifier vos connexions dans le modèle. Une connexion de service vous permet de vous connecter à votre registre de conteneurs Azure lors de l’utilisation des modèles de tâche et à l’abonnement Azure sur lequel vous souhaitez déployer l’application. 

1. Dans le coin inférieur gauche, sélectionnez **Paramètres du projet > Connexions de service**.
2. Sélectionnez **Nouvelle connexion de service**, sélectionnez l’option **Registre Docker > Azure Container Registry** pour le type de connexion de service dont vous avez besoin, puis sélectionnez Suivant.
3. Choisissez une méthode d’authentification, puis sélectionnez Suivant.
4. Entrez les paramètres de la connexion de service. La liste des paramètres varie selon le type de connexion de service. Pour plus d’informations, consultez [la liste des types de connexion de service et les paramètres associés](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#common-service-connection-types).
5. Sélectionnez **Enregistrer** pour créer la connexion.
6. Sélectionnez **Valider** pour valider la connexion une fois celle-ci créée et les paramètres entrés. Le lien de validation utilise un appel REST au service externe avec les informations que vous avez entrées, et indique si l’appel a réussi.
7. Répétez les mêmes étapes pour créer une connexion de service à votre abonnement Azure en sélectionnant **Nouvelle connexion de service > Azure Resource Manager**. 
8. Notez l’ID de ressource de chaque connexion de service. 

## <a name="create-the-pipeline"></a>Créer le pipeline

Maintenant que vous avez créé les deux connexions de service, vous pouvez configurer votre pipeline. Le pipeline YAML est créé automatiquement par l’outil de conteneurisation d’applications et peut être configuré comme ceci :  

1. Accédez à Pipelines, puis sélectionnez **Nouveau pipeline**.
2. Suivez les étapes de l’Assistant en sélectionnant d’abord **GitHub** comme emplacement du code source.
3. Vous serez peut-être redirigé vers GitHub pour vous connecter. Si c’est le cas, entrez vos informations d’identification GitHub.
4. Quand la liste des dépôts s’affiche, sélectionnez votre dépôt.
5. Vous serez peut-être redirigé vers GitHub pour pouvoir installer l’application Azure Pipelines. Si c’est le cas, sélectionnez Approuver et installer.
6. Quand votre nouveau pipeline s’affiche, passez en revue le code YAML pour voir ce qu’il fait.
7. Dans le code YAML, indiquez l’ID de ressource de la connexion de service Azure Container Registry comme valeur de la variable **dockerRegistryServiceConnection**.
8. Indiquez l’ID de ressource de la connexion de service Azure Resource Manager comme valeur de la variable **dockerRegistryServiceConnection**.
9. Quand vous êtes prêt, sélectionnez **Enregistrer** pour valider le nouveau pipeline dans votre dépôt. 

Votre pipeline est configuré pour créer et déployer votre conteneur pour les opérations de jour 2. Vous pouvez [personnaliser votre pipeline](/azure/devops/pipelines/customize-pipeline?view=azure-devops#prerequisite) pour répondre aux besoins de votre organisation. 