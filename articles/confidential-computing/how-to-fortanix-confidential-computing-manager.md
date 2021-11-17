---
title: Fortanix Confidential Computing Manager dans une application managée Azure
description: Découvrez comment déployer Fortanix Confidential Computing Manager (CCM) dans une application managée dans le portail Azure.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fa811a3f528fb0d1bf20e053910540a6204eb724
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315907"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix Confidential Computing Manager dans une application managée Azure

Cet article explique comment déployer une application gérée par Fortanix Confidential Computing Manager dans le portail Azure.

Fortanix est un fournisseur de logiciels tiers dont les produits et services reposent sur l’infrastructure Azure. D’autres fournisseurs tiers proposent des services informatiques confidentiels similaires sur Azure.

> [!NOTE]
>Microsoft n’a aucun contrôle sur les produits référencés dans ce document. Microsoft vous fournit ces informations uniquement à titre de commodité, et la référence à ces produits non Microsoft n’implique aucune approbation de la part de Microsoft.

## <a name="prerequisites"></a>Prérequis

- Un registre Docker privé pour transmettre les images des applications converties.
- Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Déployer une instance de Confidential Computing Manager par le biais d’une application managée Azure

1. Accédez au [portail Azure](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/Azure-portal.png" alt-text="Portail Azure.":::

2. Dans la barre de recherche, recherchez « Fortanix Confidential Computing Manager » et vous trouverez l’offre du marketplace pour Fortanix CCM. Sélectionnez **Fortanix Confidential Computing Manager sur Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Offre sur le marketplace.":::

3. La page sur laquelle vous créez l’application managée par CCM s’ouvre. Sélectionnez **Créer**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Créer une application.":::

4. Renseignez tous les champs obligatoires.
   1. Dans la section Détails de l’application managée, le champ **Groupe de ressources managé** aura une valeur par défaut que l’utilisateur peut modifier si nécessaire.
   2. Dans le champ **Région**, sélectionnez **Australie Est**, **Australie Sud-Est**, **USA Est**, **USA Ouest 2**, **Europe Ouest**, **Europe Nord**, **Canada Centre**, **Canada Est** ou **USA Est 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Champs obligatoires":::

   Sélectionnez **Vérifier + créer** pour créer l’application managée par Fortanix CCM.

5. Passez en revue les détails et, une fois la validation réussie, cochez la case **J’accepte les conditions générales ci-dessus**, puis sélectionnez **Créer** pour créer l’application managée.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Passer en revue les détails.":::

6. Le déploiement de Fortanix CCM démarre et indique que le déploiement est en cours.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Progression du déploiement.":::

7. Une fois le déploiement terminé, sélectionnez le bouton **Accéder à la ressource** pour accéder à la page « Vue d’ensemble » de l’application gérée par CCM déployée pour inscrire le nœud de calcul.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Capture d’écran montrant un déploiement réussi dans le portail Azure.":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Capture d’écran montrant une vue d’ensemble de la ressource d’informatique confidentielle dans le portail Azure.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Inscrire le nœud de calcul dans Fortanix CCM

1. Dans le menu de navigation gauche, sélectionnez **Confidential Computing Manager**. Connectez-vous à Fortanix CCM et créez un compte comme illustré dans la **figure 9**.

    Pour plus d’informations sur l’inscription, la connexion et la création d’un compte dans CCM, reportez-vous à la page de [prise en main de CCM](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Capture d’écran montrant la connexion à Fortanix Confidential Computing Manager.":::
    
2. Pour récupérer le jeton de jointure auprès de la console de gestion de CCM, commencez par sélectionner le bouton **INSCRIRE UN NŒUD**. Ensuite, dans la fenêtre INSCRIRE UN NŒUD, sélectionnez le bouton **COPIER** pour copier le jeton de jointure.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Capture d’écran qui montre l’obtention du jeton de jointure.":::

3. À présent, pour inscrire un agent de nœud, sélectionnez l’onglet **Agent du nœud d’informatique confidentielle** et sélectionnez **Ajouter** pour ajouter un agent de nœud CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Capture d’écran illustrant l’ajout de l’agent du nœud.":::

4.  Dans le formulaire de l’agent du nœud CCM, renseignez tous les champs obligatoires. Collez le jeton de jointure que vous avez copié à l’étape 2 dans **Jeton de jointure**. Sélectionnez **Vérifier + envoyer** pour confirmer.

    Pour plus d’informations sur l’inscription d’un nœud de calcul CCM, consultez [Inscrire un nœud de calcul](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Capture d’écran montrant l’inscription du nœud de calcul.":::
    
5. Une fois la validation effectuée, sélectionnez **Envoyer** pour terminer la création de l’agent du nœud.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Capture d’écran montrant que l’agent du nœud est créé.":::

6. Pour vérifier l’état du déploiement, accédez à l’onglet **Vue d’ensemble**, puis sélectionnez le lien **Groupe de ressources managé**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Capture d’écran montrant que le nœud est inscrit.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Capture d’écran montrant la vérification de l’état du déploiement.":::

7. À présent, vous remarquerez que l’état du déploiement est toujours en cours et qu’il faudra quelques minutes pour que l’agent du nœud soit correctement inscrit.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Capture d’écran montrant le déploiement en cours.":::

8. Lorsque l’inscription de l’agent du nœud est réussie, l’état passe à « Réussi ».

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Capture d’écran montrant que le déploiement a réussi.":::

9. Dans l’application gérée par CCM, accédez aux pages Nœuds de calcul. Vous remarquerez que le nœud est dans un état **Actif** et inscrit avec succès.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Capture d’écran montrant le nœud correctement inscrit.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

L’utilisateur peut également supprimer un agent de nœud CCM à partir de la page Agent du nœud d’informatique confidentielle. Pour supprimer l’agent du nœud, sélectionnez-le et sélectionnez le bouton **Supprimer** dans la barre supérieure.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Capture d’écran montrant la suppression de l’agent du nœud.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez inscrit un nœud à l’aide d’une application managée Azure auprès de Confidential Computing Manager de Fortanix. L’inscription de nœud vous permet de convertir l’image de votre application afin qu’elle s’exécute sur une machine virtuelle de l’informatique confidentielle. Pour plus d’informations sur les machines virtuelles d’informatique confidentielle sur Azure, consultez [Solutions sur les machines virtuelles](virtual-machine-solutions-sgx.md).

Pour en savoir plus sur les offres d’Azure en matière d’informatique confidentielle, consultez [Informatique confidentielle Azure](overview.md).

Apprenez à effectuer des tâches similaires à l'aide d'autres offres de tiers disponibles sur Azure, comme [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) et [Scone](https://sconedocs.github.io).
