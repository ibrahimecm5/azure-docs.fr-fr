---
title: Concepts d’Azure DevTest Labs
description: Découvrez les concepts de base de DevTest Labs et comment il peut faciliter la création, la gestion et la surveillance des machines virtuelles Azure
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: e66e3180900495d0d481742ab0fed172e25d9f3b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475898"
---
# <a name="devtest-labs-concepts"></a>Concepts de DevTest Labs

Cet article répertorie les principaux concepts et définitions DevTest Labs :

## <a name="lab"></a>Laboratoire
Un laboratoire est l’infrastructure qui comprend un groupe de ressources, telles que des machines virtuelles, qui vous permet de mieux gérer ces ressources en spécifiant des limites et des quotas.

## <a name="virtual-machine"></a>Machine virtuelle
Une machine virtuelle Azure est l’un des types de [ressources informatiques évolutives et à la demande](/azure/architecture/guide/technology-choices/compute-decision-tree) proposés par Azure. Les machines virtuelles Azure vous apportent la souplesse de la virtualisation sans vous obliger à acheter le matériel qui exécute la machine virtuelle ni à en assurer la maintenance.

L’article [Vue d’ensemble des machines virtuelles Windows dans Azure](../virtual-machines/windows/overview.md) vous informe sur les points à prendre en compte avant de créer une machine virtuelle, sur sa création et sur sa gestion.

## <a name="claimable-vm"></a>Machine virtuelle revendicable
Une machine virtuelle Azure exigible est une machine virtuelle qui peut être utilisée par n’importe quel utilisateur de laboratoire disposant d’autorisations. Les administrateurs de laboratoire peuvent préparer des machines virtuelles avec des images de base et des artefacts spécifiques, et les enregistrer dans un pool partagé. Les utilisateurs de laboratoire peuvent alors exiger une machine virtuelle fonctionnelle à partir du pool lorsqu’ils ont besoin d’une machine virtuelle avec cette configuration spécifique.

Une machine virtuelle exigible n’est pas initialement affectée à un utilisateur spécifique, mais s’affiche dans la liste de tous les utilisateurs sous « Machines virtuelles exigibles ». Une fois qu’une machine virtuelle est revendiquée par un utilisateur, elle est placée dans la zone **Mes machines virtuelles** et n’est plus exigible par un autre utilisateur.

## <a name="environment"></a>Environnement
Dans DevTest Labs, un environnement fait référence à une collection de ressources Azure dans un laboratoire. [Créer un environnement](./devtest-lab-create-environment-from-arm.md) explique comment créer des environnements à plusieurs machines virtuelles à partir de vos modèles Azure Resource Manager.

## <a name="base-images"></a>Images de base
Les images de base sont des images de machine virtuelle dont tous les outils et paramètres sont préinstallés et configurés pour créer rapidement une machine virtuelle. Vous pouvez créer une machine virtuelle en sélectionnant une base existante, puis en ajoutant un artefact pour installer l’agent de test. L’utilisation d’images de base réduit l’heure de création de la machine virtuelle.

## <a name="artifacts"></a>Artefacts
Les artefacts sont utilisés pour déployer et configurer votre application après l’approvisionnement d’une machine virtuelle. Les artefacts peuvent être :

* des outils que vous voulez installer sur la machine virtuelle, tels que des agents, Fiddler, Visual Studio ;
* des actions que vous souhaitez exécuter sur la machine virtuelle, telles que le clonage d’un dépôt ;
* des applications que vous voulez tester.

Les artefacts sont des fichiers JSON [Azure Resource Manager](../azure-resource-manager/management/overview.md) qui contiennent des instructions pour déployer et appliquer des configurations.

## <a name="artifact-repositories"></a>Référentiels d’artefact
Les référentiels d’artefact sont des dépôts Git dans lesquels les artefacts sont archivés. Vous pouvez ajouter des référentiels d’artefacts à plusieurs laboratoires de votre organisation pour les réutiliser et les partager.

## <a name="formulas"></a>Formules
Les formules fournissent un mécanisme de provisionnement rapide des machines virtuelles. Dans DevTest Labs, une formule est une liste de valeurs de propriétés par défaut utilisée pour créer une machine virtuelle de laboratoire.
Avec les formules, les machines virtuelles ayant le même ensemble de propriétés, comme l’image de base, la taille de machine virtuelle, le réseau virtuel et les artefacts, peuvent être créées sans avoir à spécifier ces propriétés à chaque fois. Quand vous créez une machine virtuelle à partir d’une formule, les valeurs par défaut peuvent être utilisées telles quelles ou modifiées.

## <a name="policies"></a>Stratégies
Les stratégies vous aident à contrôler les coûts dans votre laboratoire. Par exemple, vous pouvez créer une stratégie pour arrêter automatiquement les machines virtuelles selon une planification définie.

## <a name="caps"></a>Limites
Les plafonds constituent un mécanisme permettant de réduire le gaspillage dans votre laboratoire. Par exemple, vous pouvez définir un plafond pour limiter le nombre de machines virtuelles qui peuvent être créées par un utilisateur ou dans un laboratoire.

## <a name="security-levels"></a>Niveaux de sécurité
L’accès à la sécurité est déterminé par le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Pour comprendre comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue tels que définis par Azure RBAC.

|Terme | Description |
|---|---|
|Autorisation|Accès défini à une action spécifique (par exemple, accès en lecture à tous les ordinateurs virtuels).|
|Role| Un jeu d’autorisations qui peuvent être regroupées et attribuées à un utilisateur. Par exemple, le rôle *propriétaire de l’abonnement* a accès à toutes les ressources au sein d’un abonnement.|
|Étendue| Un niveau dans la hiérarchie d’une ressource Azure, comme un groupe de ressources, un simple laboratoire ou l’ensemble de l’abonnement.|


Dans l’étendue de DevTest Labs, il existe deux types de rôles pour définir des autorisations utilisateur : propriétaire de laboratoire et utilisateur de laboratoire.

|Role | Description |
|---|---|
|Propriétaire de laboratoire&nbsp;| A accès à toutes les ressources au sein du laboratoire. Le propriétaire d’un laboratoire peut modifier les stratégies, lire et écrire sur toutes les machines virtuelles, changer le réseau virtuel, etc.|
|Utilisateur de laboratoire | Peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels, mais il ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs.|

Pour découvrir comment créer des rôles personnalisés dans DevTest Labs, consultez l’article [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Étant donné que les étendues sont hiérarchiques, lorsqu’un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit ces autorisations pour chaque niveau d’étendue inférieur. Les propriétaires d’abonnements ont accès à toutes les ressources d’un abonnement, notamment les machines virtuelles, les réseaux virtuels et les laboratoires. Un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Ainsi, un propriétaire de laboratoire ne peut pas voir les machines virtuelles ou réseaux virtuels ou toutes les ressources qui sont en dehors du laboratoire.

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager
Les concepts abordés dans cet article peuvent être configurés à l’aide de modèles Azure Resource Manager (ARM). Les modèles ARM vous permettent de définir l’infrastructure/la configuration de votre solution Azure et de la déployer plusieurs fois de manière cohérente.

Le [format de modèle](../azure-resource-manager/templates/syntax.md#template-format) décrit la structure d’un modèle Azure Resource Manager et les propriétés disponibles dans les différentes sections d’un modèle.

## <a name="next-steps"></a>Étapes suivantes

[Créer un laboratoire dans DevTest Labs](devtest-lab-create-lab.md)