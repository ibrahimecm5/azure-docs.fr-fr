---
title: Créer une expérience qui utilise une erreur de service direct avec Azure Chaos Studio
description: Créer une expérience qui utilise une erreur de service direct
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: df9dc3bc114f486b86b3621a6aa8d0fbddd73431
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097035"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-to-fail-over-an-azure-cosmos-db-instance"></a>Créer une expérience de chaos qui utilise une erreur service-direct pour basculer une instance Azure Cosmos DB

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Dans ce guide, vous allez provoquer un basculement Azure Cosmos DB en lecture seule et en écriture à l’aide d’une expérience de chaos et d’Azure Chaos Studio. L’exécution de cette expérience peut vous aider à vous prémunir contre la perte de données lorsqu’un événement de basculement se produit.

Ces mêmes étapes peuvent être utilisées pour configurer et exécuter une expérience d’erreur de service direct. Une erreur de **service direct** s’exécute directement sur une ressource Azure sans nécessiter d’instrumentation, contrairement aux erreurs basées sur l’agent qui demandent l’installation de l’agent Chaos.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Un compte Azure Cosmos DB. Si vous ne disposez pas d’un compte Cosmos DB, vous pouvez [suivre ces étapes pour en créer un](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Au moins deux configurations de région, une de lecture et une d’écriture pour votre compte Azure Cosmos DB.


## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Activer Chaos Studio sur votre compte Azure Cosmos DB

Chaos Studio ne peut pas injecter d’erreurs sur une ressource, sauf si elle a d’abord été intégrée à Chaos Studio. Vous intégrez une ressource à Chaos Studio en créant une [cible et des fonctionnalités](chaos-studio-targets-capabilities.md) sur la ressource. Les comptes Azure Cosmos DB n’ont qu’un seul type de cible (service direct) et qu’une seule fonctionnalité (basculement), mais d’autres ressources peuvent comporter jusqu’à deux types de cibles (l’un pour les erreurs de service direct, l’autre pour les erreurs basées sur l’agent) et de nombreuses fonctionnalités.

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Recherchez **Chaos Studio (version préliminaire)** dans la barre de recherche.
3. Cliquez sur **Cibles** et accédez à votre compte Azure Cosmos DB.
![Vue des cibles dans le Portail Azure](images/tutorial-service-direct-targets.png)
4. Cochez la case en regard de votre compte Azure Cosmos DB et cliquez sur **Activer les cibles**, puis sur **Activer les cibles de service direct** dans le menu déroulant.
![Activation des cibles dans le Portail Azure](images/tutorial-service-direct-targets-enable.png)
5. Une notification s’affiche pour indiquer que la ou les ressources sélectionnées ont été activées correctement.
![Notification indiquant que la cible a été activée correctement](images/tutorial-service-direct-targets-enable-confirm.png)

Vous venez d’achever l’intégration de votre compte Azure Cosmos DB à Chaos Studio. Dans la vue **Cibles**, vous pouvez également gérer les fonctionnalités activées sur cette ressource. En cliquant sur le lien **Gérer les actions** à côté d’une ressource, vous affichez les fonctionnalités activées pour cette ressource.

## <a name="create-an-experiment"></a>Créer une expérience
Votre compte Azure Cosmos DB étant intégré, vous pouvez créer votre expérience. Une expérience de chaos définit les actions que vous souhaitez effectuer sur les ressources cibles, organisées en étapes, qui s’exécutent de manière séquentielle, et les branches qui s’exécutent en parallèle.

1. Cliquez sur l’onglet **Expériences** dans Chaos Studio. Dans cette vue, vous pouvez afficher et gérer toutes vos expériences de chaos. Cliquez sur **Ajouter une expérience**
![Vue des expériences dans le Portail Azure](images/tutorial-service-direct-add.png)
2. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, là où vous souhaitez déployer l’expérience de chaos. Donnez un **nom** à votre expérience. Cliquez sur **Suivant : concepteur d’expérience >** 
![Ajout des détails de l’expérience de base](images/tutorial-service-direct-add-basics.png)
3. Vous êtes maintenant dans le concepteur d’expérience Chaos Studio. Le concepteur d’expérience vous permet de créer votre expérience en ajoutant des étapes, des branches et des erreurs. Donnez un nom convivial à votre **étape** et votre **branche**, puis cliquez sur **Ajouter une erreur**.
![Concepteur d’expériences](images/tutorial-service-direct-add-designer.png)
4. Sélectionnez **Basculer CosmosDB** dans la liste déroulante, puis renseignez la **Durée** en précisant le nombre de minutes pendant lesquelles vous souhaitez que dure la défaillance, et la **Région de lecture** en précisant la région de lecture de votre compte Azure Cosmos DB. Cliquez sur **Suivant : ressources cibles >** 
![Propriétés d’erreur](images/tutorial-service-direct-add-fault.png)
5. Sélectionnez votre compte Azure Cosmos DB, puis cliquez sur **Suivant**
![Ajouter une cible](images/tutorial-service-direct-add-target.png).
6. Vérifiez que votre expérience semble correcte, puis cliquez sur **Vérifier + créer**, puis sur **Créer.** 
![Vérifier et créer une expérience](images/tutorial-service-direct-add-review.png)

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Accorder une autorisation d’expérience à votre compte Azure Cosmos DB
Lorsque vous créez une expérience de chaos, Chaos Studio crée une identité managée affectée par le système qui exécute des erreurs sur vos ressources cibles. Cette identité doit avoir les [autorisations appropriées](chaos-studio-fault-providers.md) sur la ressource cible pour que l’expérience s’exécute correctement.

1. Accédez à votre compte Azure Cosmos DB, puis cliquez sur **Contrôle d’accès (IAM)** .
![Page de la vue d’ensemble d’Azure Cosmos DB](images/tutorial-service-direct-access-resource.png)
2. Cliquez sur **Ajouter**, puis sur **Ajouter une attribution de rôle**.
![Présentation du contrôle d’accès](images/tutorial-service-direct-access-iam.png)
3. Recherchez **Opérateur Cosmos DB** et sélectionnez le rôle. Cliquez sur **Suivant**
![Affectation du rôle Opérateur Azure Cosmos DB](images/tutorial-service-direct-access-role.png).
4. Cliquez sur **Sélectionner des membres** et recherchez le nom de votre expérience. Sélectionnez votre expérience, puis cliquez sur **Sélectionner**. S’il existe plusieurs expériences dans le même locataire avec le même nom, le nom de votre expérience sera tronqué avec des caractères aléatoires ajoutés.
![Ajout de l’expérience au rôle](images/tutorial-service-direct-access-experiment.png)
5. Cliquez sur **Vérifier + attribuer**, puis sur **Vérifier + attribuer**.

## <a name="run-your-experiment"></a>Exécuter votre expérience
Vous pouvez désormais exécuter votre service. Pour voir l’impact, nous vous recommandons d’ouvrir la vue d’ensemble de votre compte Azure Cosmos DB et d’accéder à **Répliquer les données globalement** dans un onglet de navigateur distinct. L’actualisation périodique pendant l’expérimentation affichera l’échange de régions.

1. Dans **Expériences**, cliquez sur votre expérience, sur **Démarrer**, puis sur **OK**.
2. Lorsque l'**État** passe à **En cours d’exécution**, cliquez sur **Détails** pour la dernière exécution sous **Historique** pour afficher les détails de l’expérience en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté une expérience de service direct Azure Cosmos DB, vous êtes prêt à :
- [Créer une expérience qui utilise des erreurs basées sur un agent](chaos-studio-tutorial-agent-based.md)
- [Choisir votre expérience](chaos-studio-run-experiment.md)
