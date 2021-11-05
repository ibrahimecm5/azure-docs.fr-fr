---
title: Migrer depuis Azure Allemagne vers Azure global
description: Introduction à la migration de vos ressources Azure depuis Azure Allemagne vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 89a65c133a2478b38755b05a8c2fe5d1bb6623a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081293"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Présentation des recommandations en matière de migration pour Azure Allemagne

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Les articles de cette section ont été créés pour vous aider à migrer vos charges de travail depuis Azure Allemagne vers Azure global. Bien que le [Centre de migration Azure](https://azure.microsoft.com/migration/) fournisse des outils vous permettant de migrer des ressources, certains des outils du centre de migration Azure sont utiles uniquement pour les migrations qui se produisent dans le même locataire ou dans la même région.

Les deux régions d’Allemagne sont totalement distinctes d’Azure global. Les clouds dans Azure global et Azure Allemagne ont leurs propres instances distinctes Azure Active Directory (Azure AD). C’est pourquoi les locataires Azure Allemagne sont distincts des locataires Azure global. Cet article décrit les outils de migration disponibles lorsque vous effectuez une migration entre *différents* locataires.

Les conseils sur l’identité/les locataires sont destinées aux clients Azure uniquement. Si vous utilisez des locataires Azure Active Directory (Azure AD) courants pour Azure et Microsoft 365 (ou d’autres produits Microsoft), il existe des complexités lors de la migration de l’identité. Vous devez d’abord lire les [Actions et impacts des phases de la migration à partir de Microsoft Cloud Allemagne](/microsoft-365/enterprise/ms-cloud-germany-transition-phases). Si vous avez des questions, contactez votre gestionnaire de comptes ou le Support Microsoft.

Les fournisseurs de solutions Azure Cloud doivent effectuer des étapes supplémentaires pour prendre en charge les clients pendant et après la transition vers la nouvelle région des centres de données allemands. Découvrez-en plus sur ces [étapes supplémentaires](/microsoft-365/enterprise/ms-cloud-germany-transition-add-csp).

## <a name="migration-process"></a>Processus de migration

Le processus qui vous permet généralement de migrer une charge de travail à partir d’Azure Allemagne vers Azure global est similaire au processus qui permet de migrer des applications dans le cloud. Les étapes du processus de migration sont les suivantes :

![Image montrant les quatre étapes du processus de migration :Évaluer, planifier, migrer et valider](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>Évaluer

Il est important de comprendre l’empreinte Azure Allemagne de votre organisation en rassemblant les propriétaires de compte Azure, les administrateurs d’abonnements, les administrateurs de locataires et les équipes de finances et de comptabilité. Les personnes qui remplissent ces rôles peuvent fournir une image complète de l’utilisation d’Azure pour une grande organisation.

Durant la phase d’évaluation, compilez un inventaire des ressources :
  - Chaque administrateur d’abonnements et administrateur de locataires doit exécuter une série de scripts pour répertorier les groupes de ressources, les ressources contenues dans chaque groupe de ressources et les paramètres de déploiement des groupes de ressources.
  - Documentez des dépendances entre les applications dans Azure et avec des systèmes externes.
  - Documentez le nombre de chaque ressource Azure et la quantité de données associée à chaque instance que vous souhaitez migrer.
  - Assurez-vous que les documents d’architecture de l’application sont cohérents avec la liste de ressources Azure.

À la fin de cette phase, vous devez disposer :

- D’une liste complète des ressources Azure en cours d’utilisation.
- D’une liste des dépendances entre les ressources.
- D’informations concernant la complexité de l’effort de migration.

### <a name="plan"></a>Planifier

Durant la phase de planification, effectuez les tâches suivantes :

- Utilisez la sortie de l’analyse des dépendances réalisée durant la phase d’évaluation pour définir les composants associés. Envisagez de migrer ensemble les composants associés dans un *package de migration*.
- (Facultatif) Profitez de cette migration pour appliquer les [5 critères de Gartner](https://www.gartner.com/en/documents/3873016/evaluation-criteria-for-cloud-management-platforms-and-t) et optimiser votre charge de travail.
- Déterminez l’environnement cible dans Azure global :
  1. Identifiez le locataire Azure global cible (créez-en un, si nécessaire).
  1. Créez des abonnements.
  1. Choisissez l’emplacement Azure global vers lequel vous souhaitez migrer.
  1. Exécutez des scénarios de migration de test qui font correspondre votre architecture dans Azure Allemagne avec l’architecture dans Azure global.
- Déterminez la chronologie et la planification appropriées pour la migration. Créez un plan de test d’acceptation utilisateur pour chaque package de migration.

### <a name="migrate"></a>Migrate

Durant la phase de migration, utilisez les outils, techniques et recommandations décrits dans les articles de migration Azure Allemagne pour créer des ressources dans Azure global. Puis configurez les applications.

### <a name="validate"></a>Validate

Durant la phase de validation, effectuez les tâches suivantes :

1. Effectuez le test d’acceptation utilisateur.
1. Assurez-vous que les applications fonctionnent correctement.
1. Synchronisez les données les plus récentes avec l’environnement cible, le cas échéant.
1. Basculez vers une nouvelle instance d’application dans Azure global.
1. Vérifiez que l’environnement de production fonctionne correctement.
1. Désactivez des ressources dans Azure Allemagne.

## <a name="terms"></a>Termes

Ces termes sont utilisés dans les articles de migration Azure Allemagne :

**Source** décrit l’emplacement à partir duquel vous migrez les ressources (par exemple, Azure Allemagne) :

- **Nom du locataire source** : nom du locataire dans Azure Allemagne (tout ce qui suit **\@** dans le nom du compte). Dans Azure Allemagne, tous les noms de locataire terminent par **microsoftazure.de**.
- **ID du locataire source** : ID du locataire dans Azure Allemagne. L’ID du locataire apparaît dans le Portail Azure lorsque vous passez la souris sur le nom du compte dans le coin supérieur droit.
- **ID d’abonnement source** : ID de l’abonnement à des ressources dans Azure Allemagne. Vous pouvez disposer de plusieurs abonnements dans un même locataire. Assurez-vous toujours d’utiliser l’abonnement approprié.
- **Région source** : Allemagne Centre (**germanycentral**) ou Allemagne Nord-Est (**germanynortheast**), selon l’emplacement où se trouve la ressource que vous souhaitez migrer.

**Cible** ou **Destination** décrit l’emplacement vers lequel vous migrez les ressources :

- **Nom du locataire cible** : nom du locataire dans Azure international.
- **ID du locataire cible** : ID du locataire dans Azure international.
- **ID d’abonnement cible** : ID d’abonnement de la ressource dans Azure international.
- **Région cible** : vous pouvez utiliser presque toutes les régions dans Azure international. Vous souhaiterez probablement migrer vos ressources vers les régions Europe Ouest (**westeurope**) ou Europe Nord (**northeurope**).

> [!NOTE]
> Vérifiez que le service Azure que vous migrez est disponible dans la région cible. Tous les services Azure disponibles dans Azure Allemagne sont disponibles dans la région Europe Ouest. Tous les services Azure disponibles dans Azure Allemagne sont disponibles dans la région Europe Nord, à l’exception de Machine Learning Studio (classique) et de la série de machine virtuelle G/GS dans les machines virtuelles Azure.

Il est judicieux d’ajouter les portails source et cible à vos favoris dans votre navigateur :

- Le Portail Azure Allemagne se trouve à l’adresse [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/).
- Le Portail Azure global se trouve à l’adresse [https://portal.azure.com/](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
