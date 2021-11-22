---
title: Créer un environnement centré sur l’application avec Colony
description: Cet article explique comment créer un environnement orienté applications avec Colony et Azure.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 9ce80a7467ac94a69227cc9616c72c622a6f575f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347240"
---
# <a name="create-an-application-centric-environment-with-colony"></a>Créer un environnement centré sur l’application avec Colony

[Quali CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) est une plateforme SaaS (software-as-a-service) pour assurer l’automatisation de l’infrastructure à grande échelle. Colony travaille pour aider les développeurs à déployer des applications dans des environnements cloud complexes comme Azure et Kubernetes. Colony complète Azure DevTest Labs tout au long du processus de déploiement de l’application, jusqu’à la production. Cet article explique comment créer un environnement orienté applications avec Colony et Azure.

## <a name="set-up-the-environment-with-colony-and-microsoft-azure"></a>Configurer l'environnement avec Colony et Microsoft Azure

1. Inscrivez-vous pour bénéficier d'un essai gratuit de [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Capture d’écran montrant l’inscription d’une version d’évaluation gratuite de Colony.":::
1. [Liez votre compte Azure](https://colonysupport.quali.com/hc/articles/360008222234).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Capture d’écran de l’écran de bienvenue de Colony.":::
1. Invitez des utilisateurs dans votre espace.
1. [Créez votre premier plan à l’aide d’un fichier YAML](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint).
    1. Liez votre référentiel de blueprints GitHub ou BitBucket à Colony.
    1. Utilisez un exemple de blueprint Colony comme base et modifiez-le en fonction de vos besoins.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Capture d’écran qui montre les tests de contrainte.":::
    1. Publiez votre blueprint pour le mettre à la disposition d'autres utilisateurs.
1. Lancez votre environnement d'applications dans un bac à sable à l'aide de Colony.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Capture d’écran de lancement de votre environnement d’application dans un bac à sable (sandbox) à l’aide de Colony.":::

Vous pouvez également intégrer votre blueprint dans le cadre d’un flux de travail d’intégration continue et de livraison continue (CI/CD) dans Azure Pipelines. Pour obtenir des instructions, consultez [Lancement d’un bac à sable (Sandbox) à partir d’Azure DevOps (VSTS)](https://colonysupport.quali.com/hc/articles/360008464234).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Capture d’écran montrant la connexion à un pipeline Azure Pipelines.":::

## <a name="next-steps"></a>Étapes suivantes

[Demander une démo de Colony](https://info.quali.com/cloudshell-colony-demo-request)
