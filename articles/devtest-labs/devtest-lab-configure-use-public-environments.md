---
title: Configurer et utiliser des environnements publics
description: Cet article explique comment configurer et utiliser des environnements publics (modèles Azure Resource Manager dans un référentiel Git) dans Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 3570f88ae8fe88740721b04783a8689e22c7bb7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286371"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configurer et utiliser des environnements publics dans Azure DevTest Labs
Azure DevTest Labs a un [référentiel public de modèles Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que vous pouvez utiliser pour créer des environnements. Vous n’êtes pas obligé de vous connecter à une source GitHub externe par vous-même. Ce dépôt inclut des modèles fréquemment utilisés, comme Azure Web Apps, Cluster Service Fabric et une batterie de serveurs SharePoint de développement. Cette fonctionnalité est similaire au dépôt public d’artefacts qui est inclus pour chaque lab que vous créez. Le référentiel de l’environnement fournit des modèles d’environnement précréés avec des paramètres d’entrée minimum. Les modèles vous offrent une expérience de prise en main sans heurts pour les ressources PaaS (Platform-as-a-service) au sein des labos.
  
## <a name="configuring-public-environments"></a>Configuration des environnements publics
En tant que propriétaire d’un lab, vous pouvez activer le dépôt public d’environnements pour votre lab lors de la création du lab. Pour activer des environnements publics pour votre lab, sélectionnez **Activé** pour le champ **Environnements publics** lors de la création d’un lab. 

![Activer un environnement public pour un nouveau lab](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)

Pour les labos existants, le dépôt d’environnements publics n’est pas activé. Activez manuellement le référentiel pour utiliser les modèles. Pour les labos créés avec des modèles Resource Manager, le dépôt est également désactivé par défaut.

Vous pouvez activer ou désactiver des environnements publics pour votre labo, ainsi que mettre seulement certains environnements spécifiques à la disposition des utilisateurs d’un lab en effectuant les étapes suivantes : 

1. Sélectionnez **Configuration et stratégies** pour votre lab. 
2. Dans la section **Bases de machine virtuelle**, sélectionnez **Environnements publics**.
3. Pour activer les environnements publics pour le lab, sélectionnez **Oui**. Sinon, sélectionnez **Non**. 
4. Si vous avez activé les environnements publics, tous les environnements du dépôt sont activés par défaut. Vous pouvez désélectionner un environnement afin que celui-ci ne soit plus disponible pour les utilisateurs de votre labo. 

![Capture d’écran montrant la page Environnements publics.](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Utiliser des modèles d’environnement en tant qu’utilisateur de lab
En tant qu’utilisateur de lab, vous pouvez créer un environnement à partir de la liste activée des modèles d’environnement en sélectionnant **Ajouter** dans la barre d’outils de la page du lab. La liste des bases inclut les modèles d’environnements publics activés par l’administrateur de votre lab en haut de la liste.

![Capture d’écran montrant les modèles d’environnement public.](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Étapes suivantes
Ce référentiel est un référentiel open source auquel vous pouvez contribuer. Pour ajouter vos propres modèles Resource Manager souvent utilisés et utiles, envoyez une demande de tirage (pull request) au référentiel.
