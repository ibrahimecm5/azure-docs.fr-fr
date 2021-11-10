---
title: 'Démarrage rapide : Lier un espace de travail Azure Machine Learning'
description: Lier votre espace de travail Synapse à un espace de travail Azure Machine Learning
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: 7d6b9a81f5e5e948704b4597a7283cd976b124c2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847196"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Démarrage rapide : Créer un nouveau service lié Azure Machine Learning dans Synapse

Dans ce guide de démarrage rapide, vous allez lier un espace de travail Azure Synapse Analytics à un espace de travail Azure Machine Learning. La liaison de ces espaces de travail vous permet de tirer parti d’Azure Machine Learning à partir de diverses expériences dans Synapse.

Par exemple, ce lien vers un espace de travail Azure Machine Learning permet d’effectuer les expériences suivantes :

- Exécuter des pipelines Azure Machine Learning en tant qu’étape dans vos pipelines Synapse. Pour plus d’informations, consultez [Exécuter des pipelines Azure Machine Learning](../../data-factory/transform-data-machine-learning-service.md).

- Enrichissez vos données avec des prédictions en plaçant un modèle Machine Learning à partir du registre de modèle Azure Machine Learning et notez le modèle dans des pools Synapse SQL. Pour plus d’informations, consultez le [Didacticiel : Assistant de notation de modèles Machine Learning pour les pools Synapse SQL](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="two-types-of-authentication"></a>Deux types d’authentification
Il existe deux types d’identités que vous pouvez utiliser lors de la création d’un service lié Azure Machine Learning dans Azure Synapse.
* Identité managée de l’espace de travail Synapse
* Principal de service

Dans les sections suivantes, vous trouverez des conseils sur la création d’un service lié Azure Machine Learning avec ces deux différents types d’authentification.

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/).
- [Espace de travail Synapse Analytics](../get-started-create-workspace.md) avec un compte de stockage ADLS Gen2 configuré comme stockage par défaut. Vous devez être le **contributeur de données Blob du stockage** du système de fichiers ADLS Gen2 que vous utilisez.
- [Espace de travail Azure Machine Learning](../../machine-learning/how-to-manage-workspace.md).
- Si vous avez choisi d’utiliser un principal de service, vous devez disposer d’autorisations (ou demander à une personne disposant d’autorisations) pour créer un principal de service et un secret que vous pouvez utiliser pour créer le service lié. Notez que le rôle de contributeur doit être attribué à ce principal de service dans l’espace de travail Azure Machine Learning.
- Connectez-vous au [portail Azure](https://portal.azure.com/)

## <a name="create-a-linked-service-using-the-synapse-workspace-managed-identity"></a>Créer un service lié avec l’identité managée de l’espace de travail Synapse

Dans cette section, vous allez voir comment créer un service lié Azure Machine Learning dans Azure Synapse avec l’[identité managée de l’espace de travail Azure Synapse](../../data-factory/data-factory-service-identity.md?context=/azure/synapse-analytics/context/context&tabs=synapse-analytics).

### <a name="give-msi-permission-to-the-azure-ml-workspace"></a>Accorder l’autorisation MSI à l’espace de travail Azure ML

1. Accédez à votre ressource d’espace de travail Azure Machine Learning dans le portail Azure, puis sélectionnez **Contrôle d’accès**

1. Créez une attribution de rôle et ajoutez l’identité MSI (Managed Service Identity) de l’espace de travail Synapse en tant que *contributeur* de l’espace de travail Azure Machine Learning. Notez que cette opération nécessite le propriétaire du groupe de ressources auquel appartient l’espace de travail Azure Machine Learning. Si vous ne parvenez pas à trouver l’identité MSI de l’espace de travail Synapse, recherchez le nom de l’espace de travail Synapse.

### <a name="create-an-azure-ml-linked-service"></a>Créer un service lié Azure ML

1. Dans l’espace de travail Synapse dans lequel vous souhaitez créer le service lié Azure Machine Learning, accédez à **Gestion** > **Service lié**, puis créez un service lié de type « Azure Machine Learning ».

   ![Création d’un service lié](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Remplissez le formulaire :

   - Indiquez les détails relatifs à l’espace de travail Azure Machine Learning auquel vous souhaitez établir une liaison. Ces détails incluent des informations sur l’abonnement et le nom de l’espace de travail.
   
   - Sélectionnez la méthode d’authentification : **Identité managée**
  
3. Cliquez sur **Tester la connexion** pour vérifier si la configuration est correcte. Si le test de connexion réussit, cliquez sur **Enregistrer**.

   Si le test de connexion échoue, vérifiez que l’identité MSI de l’espace de travail Azure Synapse dispose des autorisations nécessaires pour accéder à cet espace de travail Azure Machine Learning, puis réessayez.

## <a name="create-a-linked-service-using-a-service-principal"></a>Créer un service lié à l’aide d’un principal de service

Dans cette section, vous allez voir comment créer un service lié Azure Machine Learning avec un principal de service.

### <a name="create-a-service-principal"></a>Créer un principal du service

Cette étape va créer un nouveau principal de service. Si vous souhaitez utiliser un principal de service existant, vous pouvez ignorer cette étape.

1. Ouvrez le portail Azure. 

1. Accédez à **Azure Active Directory** -> **Inscriptions des applications**.

1. Cliquez sur **Nouvelle inscription**. Ensuite, suivez les instructions pour inscrire une nouvelle application.

1. Une fois l’application inscrite, générez un secret pour l’application. Accédez à **Votre application** -> **Certificat et secret**. Cliquez sur **Ajouter une clé secrète client** pour générer un secret. Conservez la sécurité secrète et elle sera utilisée ultérieurement.

   ![Générer le secret](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Créez un principal de service pour l’application. Accédez à **Votre application** -> **Vue d’ensemble**, puis cliquez sur **Créer un principal de service**. Dans certains cas, ce principal de service est automatiquement créé.

   ![Créer un principal du service](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Ajoutez le principal du service en tant que « contributeur » de l’espace de travail Azure Machine Learning. Notez que cette opération nécessite le propriétaire du groupe de ressources auquel appartient l’espace de travail Azure Machine Learning.

   ![Attribuer le rôle contributeur](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

### <a name="create-an-azure-ml-linked-service"></a>Créer un service lié Azure ML

1. Dans l’espace de travail Synapse dans lequel vous souhaitez créer le service lié Azure Machine Learning, accédez à **Gestion** -> **Service lié**, créez un service lié de type « Azure Machine Learning ».

   ![Création d’un service lié](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Remplissez le formulaire :

   - Indiquez les détails relatifs à l’espace de travail Azure Machine Learning auquel vous souhaitez établir une liaison. Ces détails incluent des informations sur l’abonnement et le nom de l’espace de travail.

   - Sélectionnez la méthode d’authentification : **Principal de service**

   - ID de principal de service : Il s’agit de l’**ID d’application (client)** de l’application.

   > [!NOTE]
   > L’ID n’est pas le nom de l’application. Vous pouvez trouver cet ID dans la page de présentation de l’application. Il doit s’agir d’une chaîne longue ressemblant à ce qui suit : « 81707eac-ab38-406u-8f6c-10ce76a568d5 ».

   - Clé du principal du service : Le secret que vous avez généré dans la section précédente.

3. Cliquez sur **Tester la connexion** pour vérifier si la configuration est correcte. Si le test de connexion réussit, cliquez sur **Enregistrer**.

   Si le test de connexion échoue, vérifiez que l’ID et le secret du principal de service sont corrects, puis réessayez.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Assistant Scoring de modèle Machine learning - Pool SQL dédié](tutorial-sql-pool-model-scoring-wizard.md)
- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
