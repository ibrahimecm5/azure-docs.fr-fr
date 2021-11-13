---
title: Qu’est-ce qu’Azure DevTest Labs ?
description: Découvrez comment DevTest Labs peut faciliter la création, la gestion et la surveillance des machines virtuelles Azure
ms.topic: overview
ms.date: 10/20/2021
ms.openlocfilehash: f521b1fcd886d56387c9b6c44451d1858f95d62f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229054"
---
# <a name="what-is-azure-devtest-labs"></a>Qu’est-ce qu’Azure DevTest Labs ?
Azure DevTest Labs est un service qui permet aux développeurs de gérer eux-mêmes efficacement les machines virtuelles et les ressources PaaS (platform as a service) sans attendre d’approbations. DevTest Labs crée des labos consistant en bases préconfigurées ou en modèles Azure Resource Manager. Ces labs ont tous les outils et logiciels nécessaires que vous pouvez utiliser pour créer des environnements.

Avec DevTest Labs, vous pouvez tester les dernières versions de vos applications en effectuant les tâches suivantes :

- Créez rapidement des environnements Windows et Linux en utilisant des modèles et des artefacts réutilisables.
- Intégrez facilement votre pipeline de déploiement à DevTest Labs pour créer des environnements à la demande.
- Effectuez un scale-up de votre test de charge en créant plusieurs agents de test, et préparez des environnements en amont pour des formations et des démos.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="cost-control-and-governance"></a>Contrôle des coûts et gouvernance
DevTest Labs facilite le contrôle des coûts en vous permettant d’effectuer les tâches suivantes :

- [Définir des stratégies pour vos labos](devtest-lab-set-lab-policy.md), comme le nombre de machines virtuelles par utilisateur ou par labo. 
- Créer des [stratégies pour arrêter et démarrer automatiquement](devtest-lab-set-lab-policy.md) des machines virtuelles.
- Suivre les coûts sur les machines virtuelles et les ressources PaaS créées au sein des labos de façon à respecter [votre budget](devtest-lab-configure-cost-management.md). Recevoir des avis de coûts élevés anticipés pour les labs de façon à pouvoir prendre les mesures nécessaires.
- Rester dans le contexte de vos labos, de façon à ne pas créer de ressources en dehors de ceux-ci.

## <a name="quickly-get-to-ready-to-test"></a>Vos applications sont prêtes pour le test en un clin d’œil
DevTest Labs vous permet de créer des environnements préprovisionnés pour développer et tester des applications. Il vous suffit de [revendiquer l’environnement](devtest-lab-add-claimable-vm.md) de la dernière build satisfaisante de votre application et de commencer à travailler. Vous pouvez aussi utiliser des conteneurs pour créer encore plus rapidement et plus facilement des environnements.

## <a name="create-once-use-everywhere"></a>Créez, réutilisez
Capturez et partagez des [modèles d’environnement](devtest-lab-create-environment-from-arm.md) PaaS et des [artefacts](add-artifact-repository.md) au sein de votre équipe ou organisation, tout cela dans le contrôle du code source, pour créer facilement des environnements de développement et de test.

## <a name="worry-free-self-service"></a>Libre-service convivial
DevTest Labs permet à vos développeurs et à vos testeurs de [créer des machines virtuelles IaaS](devtest-lab-add-vm.md) et des [ressources PaaS](devtest-lab-create-environment-from-arm.md) rapidement et facilement en utilisant un ensemble de ressources préconfigurées.

## <a name="use-iaas-and-paas-resources"></a>Utiliser des ressources IaaS et PaaS 
Lancez des ressources, comme des clusters Azure Service Fabric ou des batteries SharePoint, en utilisant des modèles Resource Manager. Les modèles proviennent du [dépôt d’environnements publics](devtest-lab-configure-use-public-environments.md) ou vous pouvez [connecter le lab à votre propre dépôt Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Vous pouvez aussi lancer un groupe de ressources vide (bac à sable) en utilisant un modèle Resource Manager pour explorer Azure dans le contexte d’un lab.

## <a name="integrate-with-your-existing-toolchain"></a>Intégrer à votre chaîne d’outils existante
Utilisez des plug-ins préfabriqués ou l’API pour créer des environnement de développement/test directement à partir de votre [outil d’intégration continue (CI)](devtest-lab-integrate-ci-cd.md) par défaut, d’un environnement de développement intégré (IDE) ou d’un pipeline de mise en production automatisé. Vous pouvez également utiliser l’outil en ligne de commande complet.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- Pour en savoir plus sur DevTest Labs, consultez [Concepts de DevTest Labs](devtest-lab-concepts.md).
- Pour obtenir une procédure pas à pas avec des instructions étape par étape, consultez [Tutoriel : configurer un laboratoire à l’aide de Azure DevTest Labs](tutorial-create-custom-lab.md).
