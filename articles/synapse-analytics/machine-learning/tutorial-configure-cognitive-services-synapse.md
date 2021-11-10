---
title: 'Démarrage rapide : Prérequis de Cognitive Services dans Azure Synapse Analytics'
description: Découvrez comment configurer les prérequis de l’utilisation de Cognitive Services dans Azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: d72db64026ff3d4d4cee759b34047662248737a6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845130"
---
# <a name="quickstart-configure-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Démarrage rapide : Configurer les prérequis pour utiliser Cognitive Services dans Azure Synapse Analytics

Dans ce guide de démarrage rapide, vous apprenez à configurer les prérequis pour utiliser Azure Cognitive Services de manière sécurisée dans Azure Synapse Analytics. La liaison de ces services cognitifs Azure vous permet de tirer parti d’Azure Cognitive Services à partir de différentes expériences dans Synapse.

Ce démarrage rapide couvre les sujets suivants :
> [!div class="checklist"]
> - Créer une ressource Cognitive Services comme Analyse de texte ou Détecteur d’anomalies.
> - Stocker une clé d’authentification auprès des ressources Cognitive Services sous la forme de secrets dans Azure Key Vault et configurer l’accès pour un espace de travail Azure Synapse Analytics.
> - Créer un service lié Azure Key Vault dans votre espace de travail Azure Synapse Analytics.
> - Créer un service lié Azure Cognitive Services dans votre espace de travail Azure Synapse Analytics.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Espace de travail Azure Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage Azure Data Lake Storage Gen2 configuré comme stockage par défaut. Vous devez être le *contributeur aux données Blob de stockage* du système de fichiers Azure Data Lake Storage Gen2 que vous utilisez.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Créer une ressource Cognitive Services

[Azure Cognitive Services](../../cognitive-services/index.yml) comprend de nombreux types de services. Les services suivants sont des exemples utilisés dans les tutoriels Azure Synapse.

Vous pouvez créer une ressource [Analyse de texte](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) dans le portail Azure :

![Capture d’écran qui montre Analyse de texte dans le portail, avec le bouton Créer](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Vous pouvez créer une ressource [Détecteur d’anomalies](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) dans le portail Azure :

![Capture d’écran qui montre Détecteur d’anomalies dans le portail, avec le bouton Créer](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

Vous pouvez créer une ressource [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) dans le portail Azure :

![Capture d’écran qui montre Form Recognizer dans le portail, avec le bouton Créer.](media/tutorial-configure-cognitive-services/tutorial-configure-form-recognizer.png)

Vous pouvez créer une ressource [Traducteur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) dans le portail Azure :

![Capture d’écran qui montre Traducteur dans le portail, avec le bouton Créer.](media/tutorial-configure-cognitive-services/tutorial-configure-translator.png)

Vous pouvez créer une ressource [Vision par ordinateur](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) dans le portail Azure :

![Capture d’écran qui montre Vision par ordinateur dans le portail, avec le bouton Créer.](media/tutorial-configure-cognitive-services/tutorial-configure-computer-vision.png)


Vous pouvez créer une ressource [Visage](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) dans le portail Azure :

![Capture d’écran qui montre Visage dans le portail, avec le bouton Créer.](media/tutorial-configure-cognitive-services/tutorial-configure-face.png)


Vous pouvez créer une ressource [Speech](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) dans le portail Azure :

![Capture d’écran qui montre Speech dans le portail, avec le bouton Créer.](media/tutorial-configure-cognitive-services/tutorial-configure-speech.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Créer un coffre de clés et configurer des secrets et l’accès

1. Créez un [coffre de clés](https://ms.portal.azure.com/#create/Microsoft.KeyVault) dans le portail Azure.
2. Accédez à **Coffre de clés** > **Stratégies d’accès**, puis accordez à l’[Identité de service managée (MSI) de l’espace de travail Azure Synapse](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics) des autorisations pour lire des secrets à partir d’Azure Key Vault.

   > [!NOTE]
   > Vérifiez que les changements de stratégie sont enregistrés. Il est facile d’oublier cette étape.

   ![Capture d’écran montrant les sélections pour ajouter une stratégie d’accès](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Accédez à votre ressource Cognitive Services. Par exemple, accédez à **Détecteur d’anomalies** > **Clés et point de terminaison**. Copiez ensuite l’une des deux clés dans le Presse-papiers.

4. Accédez à **Coffre de clés** > **Secret** pour créer un secret. Spécifiez le nom du secret, puis collez la clé de l’étape précédente dans le champ **Valeur**. Pour finir, sélectionnez **Créer**.

   ![Capture d’écran montrant les sélections pour la création d’un secret](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Veillez à mémoriser ou à noter le nom de ce secret. Vous l’utilisez par la suite pour créer le service lié Azure Cognitive Services.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Créer un service lié Azure Key Vault dans Azure Synapse

1. Ouvrez votre espace de travail dans Synapse Studio. 
2. Accédez à **Gérer** > **Services liés**. Créez un service lié **Azure Key Vault** en pointant vers le coffre de clés que vous venez de créer. 
3. Vérifiez la connexion en sélectionnant le bouton **Tester la connexion**. Si la connexion est verte, sélectionnez **Créer**, puis **Publier tout** pour enregistrer vos modifications.

![Capture d’écran qui montre Azure Key Vault en tant que nouveau service lié](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)


## <a name="create-an-azure-cognitive-service-linked-service-in-azure-synapse"></a>Créer un service lié Azure Cognitive Services dans Azure Synapse

1. Ouvrez votre espace de travail dans Synapse Studio.
2. Accédez à **Gérer** > **Services liés**. Créez un service lié **Azure Cognitive Services** en pointant sur le service cognitif que vous venez de créer. 
3. Vérifiez la connexion en sélectionnant le bouton **Tester la connexion**. Si la connexion est verte, sélectionnez **Créer**, puis **Publier tout** pour enregistrer vos modifications.

![Capture d’écran qui montre le service cognitif Azure comme un nouveau service lié.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-linked-service.png)

Vous êtes maintenant prêt à passer à l’un des tutoriels concernant l’utilisation de l’expérience Azure Cognitive Services dans Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Analyse des sentiments avec Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutoriel : Détection d’anomalie avec Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Tutoriel : Scoring de modèle Machine Learning dans des pools SQL dédiés Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
