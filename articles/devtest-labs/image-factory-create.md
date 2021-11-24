---
title: Créer une fabrique d’images
description: Cet article vous montre comment créer une fabrique d’images personnalisées à l’aide d’exemples de scripts disponibles dans le dépôt Git (Azure DevTest Labs).
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 82662ee3b7127fe52d8a91d71652e906800b61b2
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397260"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Créer une fabrique d’images personnalisées dans Azure DevTest Labs
Cet article vous montre comment créer une fabrique d’images personnalisées à l’aide d’exemples de scripts disponibles dans le [dépôt Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Qu’est-ce qu’une fabrique d’images ?
Une fabrique d’images est une solution avec une configuration sous forme de code qui crée et distribue automatiquement et régulièrement des images avec toutes les configurations souhaitées. Les images dans la fabrique d’images sont toujours à jour, et vous n’avez quasiment plus à vous en occuper une fois que l’ensemble du processus a été automatisé. De plus, comme toutes les configurations nécessaires sont incorporées aux images, vous n’avez plus besoin de configurer manuellement le système après avoir créé une machine virtuelle avec le système d’exploitation de base.

L’utilisation d’images personnalisées permet de préparer un bureau de développeur dans DevTest Labs en un temps record. L’inconvénient est que les images personnalisées doivent être tenues à jour dans le lab. Par exemple, quand des essais gratuits de produits expirent (ou) quand de nouvelles mises à jour de sécurité publiées ne sont pas appliquées, vous devez actualiser régulièrement les images personnalisées. Dans une fabrique d’images, vous avez une définition des images enregistrée dans le contrôle de code source et vous pouvez utiliser un processus automatisé pour créer des images personnalisées basées sur la définition.

La solution accélère la création de machines virtuelles à partir d’images personnalisées tout en éliminant les coûts supplémentaires d’une maintenance régulière. Avec cette solution, vous pouvez automatiquement créer des images personnalisées, les distribuer à d’autres labs DevTest Labs et les mettre hors service quand vous n’en n’avez plus besoin. Regardez la vidéo ci-dessous pour en savoir plus sur la fabrique d’images et comprendre son implémentation avec DevTest Labs.  Tous les scripts Azure PowerShell sont disponibles gratuitement à cet emplacement : [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Vue d’ensemble de la solution
La solution accélère la création de machines virtuelles à partir d’images personnalisées tout en éliminant les coûts supplémentaires d’une maintenance régulière. Avec cette solution, vous pouvez automatiquement créer des images personnalisées et les distribuer à d’autres labs DevTest Labs. Vous utilisez Azure DevOps (anciennement Visual Studio Team Services) comme moteur d’orchestration pour automatiser toutes les opérations dans DevTest Labs.

![Vue d’ensemble de la solution.](./media/create-image-factory/high-level-view-of-solution.png)

Il existe une [extension VSTS pour DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) qui vous permet d’effectuer chacune de ces étapes :

- Créer une image personnalisée
- Créer une machine virtuelle
- Supprimer une machine virtuelle
- Créer un environnement
- Supprimer un environnement
- Configurer un environnement

L’extension pour DevTest Labs offre un moyen simple de commencer à créer automatiquement des images personnalisées dans DevTest Labs.

Pour les scénarios plus complexes, il existe une autre implémentation à l’aide d’un script PowerShell. Avec PowerShell, vous pouvez entièrement automatiser une fabrique d’images dans DevTest Labs et l’utiliser ensuite dans votre chaîne d’outils d’intégration continue et de livraison continue (CI/CD). Cette autre solution est fondée sur les principes suivants :

- Les mises à jour courantes ne doivent pas nécessiter de changements dans la fabrique d’images (par exemple, l’ajout d’un nouveau type d’image personnalisée, la mise hors service automatique des images inutiles, l’ajout d’un nouveau « point de terminaison » DevTest Labs pour recevoir les images personnalisées, etc.).
- Les changements courants sont assurés par le contrôle de code source (infrastructure en tant que code)
- Les labs DevTest Labs qui reçoivent les images personnalisées peuvent être dans des abonnements Azure différents (abonnements couvrant plusieurs labs)
- Les scripts PowerShell doivent être réutilisables afin que vous puissiez créer d’autres fabriques le cas échéant.

## <a name="next-steps"></a>Étapes suivantes
Passez à l’article suivant de cette section : [Exécuter une fabrique d’images depuis Azure DevOps](image-factory-set-up-devops-lab.md).
