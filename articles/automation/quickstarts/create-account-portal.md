---
title: Démarrage rapide - Créer un compte Azure Automation en utilisant le portail
description: Ce guide de démarrage rapide vous aide à créer un compte Azure Automation en utilisant le portail.
services: automation
ms.date: 10/26/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7497362ccd71e6efabc873015796855bd989d33c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470670"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>Démarrage rapide : Créer un compte Automation en utilisant le portail Azure

Vous pouvez créer un [compte Azure Automation](../automation-security-overview.md) en utilisant le portail Azure, une interface utilisateur basée sur un navigateur permettant d’accéder à un certain nombre de ressources. Un compte Automation peut gérer les ressources sur l’ensemble des régions et abonnements pour un locataire donné. Ce guide de démarrage rapide vous guide tout au long de la création d’un compte Automation.

## <a name="prerequisites"></a>Prérequis

Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Créer un compte Automation

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le menu du haut, sélectionnez **+ Créer une ressource**.

1. Sous **Catégories**, sélectionnez **Informatique et outils de gestion**, puis **Automation**.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="Recherche de comptes Automation dans le portail.":::

Les options de votre nouveau compte Automation sont organisées sous forme d’onglets dans la page **Créer un compte Automation**. Les sections suivantes décrivent chacun des onglets et ses options.

### <a name="basics"></a>Notions de base

Dans l’onglet **Informations de base**, fournissez les informations essentielles pour votre compte Automation. Une fois que vous avez terminé l’onglet **Informations de base**, vous pouvez choisir de personnaliser davantage votre compte Automation en définissant les options sous les autres onglets, ou vous pouvez sélectionner **Vérifier + créer** pour accepter les options par défaut, puis procéder à la validation et à la création du compte.

> [!NOTE]
> Par défaut, une identité managée affectée par le système est activée pour le compte Automation.

Le tableau suivant décrit les champs de l’onglet **Informations de base**.

| **Champ** | **Obligatoire**<br> **or**<br> **facultatif** |**Description** |
|---|---|---|
|Abonnement|Obligatoire |Dans la liste déroulante, sélectionnez l’abonnement Azure pour le compte.|
|Resource group|Obligatoire |Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
|Nom du compte Automation|Obligatoire |Entrez un nom unique pour l’emplacement et le groupe de ressources. Il est possible que les noms de comptes Automation qui ont été supprimés ne soient pas immédiatement disponibles. Vous ne pouvez pas changer le nom du compte une fois qu’il a été entré dans l’interface utilisateur. |
|Région|Obligatoire |Dans la liste déroulante, sélectionnez une région pour le compte. Pour obtenir une liste mise à jour des emplacements sur lesquels vous pouvez déployer un compte Automation, consultez [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|

L’illustration suivante montre une configuration standard pour un nouveau compte Automation.

:::image type="content" source="./media/create-account-portal/create-account-basics.png" alt-text="Champs requis pour la création du compte Automation sous l’onglet Général":::

### <a name="advanced"></a>Avancé

Dans l’onglet **Avancé**, vous pouvez configurer l’option d’identité managée pour votre nouveau compte Automation. L’option d’identité managée affectée par l’utilisateur peut également être configurée après la création du compte Automation.

Pour obtenir des instructions sur la création d’une identité managée affectée par l’utilisateur, consultez [Créer une identité managée affectée par l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Le tableau suivant décrit les champs de l’onglet **Avancé**.

| **Champ** | **Obligatoire**<br> **or**<br> **facultatif** |**Description** |
|---|---|---|
|Attribué par le système |Facultatif |Une identité Azure Active Directory liée au cycle de vie du compte Automation. |
|Affecté par l’utilisateur |Facultatif |Identité managée représentée comme une ressource Azure autonome gérée séparément des ressources qui l’utilisent.|

Vous pouvez choisir d’activer les identités managées ultérieurement, et le compte Automation est créé sans. Pour activer une identité managée une fois le compte créé, consultez [Activer une identité managée](enable-managed-identity.md). Si vous sélectionnez les deux options, pour l’identité affectée par l’utilisateur, sélectionnez l’option **Ajouter des identités affectées par l’utilisateur**. Dans la page **Sélectionner une identité managée affectée par l’utilisateur**, sélectionnez un abonnement et ajoutez une ou plusieurs identités affectées par l’utilisateur créées dans cet abonnement à attribuer au compte Automation.

L’illustration suivante montre une configuration standard pour un nouveau compte Automation.

:::image type="content" source="./media/create-account-portal/create-account-advanced.png" alt-text="Champs requis pour la création du compte Automation sous l’onglet Avancé":::

### <a name="tags-tab"></a>Onglet balises

Dans l’onglet **Balises**, vous pouvez spécifier des balises Resource Manager pour faciliter l’organisation de vos ressources Azure. Pour plus d’informations, consultez [Baliser les ressources, les groupes de ressources et les abonnements pour l’organisation logique](../../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Onglet Vérifier + créer

Lorsque vous accédez à l’onglet **Vérifier + créer**, Azure exécute la validation sur les paramètres du compte Automation que vous avez choisis. Si la validation réussit, vous pouvez procéder à la création du compte Automation.

Si la validation échoue, le portail indique les paramètres qui doivent être modifiés.

Passez en revue votre nouveau compte Automation.

:::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Page Vue d’ensemble du compte Automation":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de continuer à utiliser le compte Automation, sélectionnez **Supprimer** dans la page **Vue d’ensemble**, puis sélectionnez **Oui** quand vous y êtes invité.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte Automation. Pour utiliser des identités managées avec votre compte Automation, passez au guide de démarrage rapide suivant :

> [!div class="nextstepaction"]
> [Tutoriel : Créer un runbook PowerShell Automation avec une identité managée](../learn/powershell-runbook-managed-identity.md)