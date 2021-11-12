---
title: Création d’un compte Azure Automation autonome
description: Cet article explique comment créer un compte Azure Automation autonome.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: eedee6af181a0dc99635dc455dfda6a154fe5eb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451033"
---
# <a name="create-a-standalone-azure-automation-account"></a>Création d’un compte Azure Automation autonome

Cet article vous montre comment créer un compte Azure Automation à l’aide du portail Azure. Vous pouvez utiliser le compte Automation pour évaluer Automation et vous familiariser avec la solution sans recourir à des fonctionnalités de gestion supplémentaires ou à une intégration aux journaux Azure Monitor. Vous pouvez ajouter des fonctionnalités de gestion ou procéder à une intégration avec les journaux Azure Monitor pour une supervision avancée des tâches de runbook ultérieurement.

Avec un compte Automation, vous pouvez authentifier des runbooks en gérant des ressources dans Azure Resource Manager ou dans le modèle de déploiement classique. Un compte Automation peut gérer les ressources sur toutes les régions et abonnements pour un locataire donné.

Grâce à ce compte créé pour vous, vous pouvez rapidement commencer à créer et à déployer des runbooks pour répondre à vos besoins d'automatisation.

## <a name="permissions-required-to-create-an-automation-account"></a>Autorisations requises pour créer un compte Automation

Pour créer ou mettre à jour un compte Automation et effectuer les tâches décrites dans cet article, vous devez disposer des privilèges et autorisations suivants :

Pour créer un compte Automation, votre compte d'utilisateur Azure AD doit être ajouté à un rôle disposant d'autorisations équivalentes à celles du rôle Propriétaire des ressources `Microsoft.Automation`. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Créer un compte Automation dans le portail Azure

Pour créer un compte Azure Automation dans le portail Azure, suivez les étapes suivantes :

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et Coadministrateur de l’abonnement.
1. Sélectionnez **+ Créer une ressource**.
1. Recherchez **Automation**. Dans les résultats de recherche, sélectionnez **Automation**.

   :::image type="content" source="./media/automation-create-standalone-account/automation-account-portal.png" alt-text="Recherche de comptes Automation dans le portail":::

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

:::image type="content" source="./media/automation-create-standalone-account/create-account-basics.png" alt-text="Champs requis pour la création du compte Automation sous l’onglet Général":::

### <a name="advanced"></a>Avancé

Dans l’onglet **Avancé**, vous pouvez configurer l’option d’identité managée pour votre nouveau compte Automation. L’option d’identité managée affectée par l’utilisateur peut également être configurée après la création du compte Automation.

Pour obtenir des instructions sur la création d’une identité managée affectée par l’utilisateur, consultez [Créer une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Le tableau suivant décrit les champs de l’onglet **Avancé**.

| **Champ** | **Obligatoire**<br> **or**<br> **facultatif** |**Description** |
|---|---|---|
|Attribué par le système |Facultatif |Une identité Azure Active Directory liée au cycle de vie du compte Automation. |
|Affecté par l’utilisateur |Facultatif |Identité managée représentée comme une ressource Azure autonome gérée séparément des ressources qui l’utilisent.|

Vous pouvez choisir d’activer les identités managées ultérieurement, et le compte Automation est créé sans. Pour activer une identité managée une fois le compte créé, consultez [Activer une identité managée](enable-managed-identity-for-automation.md). Si vous sélectionnez les deux options, pour l’identité affectée par l’utilisateur, sélectionnez l’option **Ajouter des identités affectées par l’utilisateur**. Dans la page **Sélectionner une identité managée affectée par l’utilisateur**, sélectionnez un abonnement et ajoutez une ou plusieurs identités affectées par l’utilisateur créées dans cet abonnement à attribuer au compte Automation.

L’illustration suivante montre une configuration standard pour un nouveau compte Automation.

:::image type="content" source="./media/automation-create-standalone-account/create-account-advanced.png" alt-text="Champs requis pour la création du compte Automation sous l’onglet Avancé":::

### <a name="tags-tab"></a>Onglet balises

Dans l’onglet **Balises**, vous pouvez spécifier des balises Resource Manager pour faciliter l’organisation de vos ressources Azure. Pour plus d’informations, consultez [Baliser les ressources, les groupes de ressources et les abonnements pour l’organisation logique](../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Onglet Vérifier + créer

Lorsque vous accédez à l’onglet **Vérifier + créer**, Azure exécute la validation sur les paramètres du compte Automation que vous avez choisis. Si la validation réussit, vous pouvez procéder à la création du compte Automation.

Si la validation échoue, le portail indique les paramètres qui doivent être modifiés.

Passez en revue votre nouveau compte Automation.

:::image type="content" source="./media/automation-create-standalone-account/automation-account-overview.png" alt-text="Page Vue d’ensemble du compte Automation":::

Une fois le compte Automation créé, plusieurs ressources vous sont automatiquement créées. Suite à cette création, ces runbooks peuvent être supprimés en toute sécurité si vous ne souhaitez pas les conserver. Les identités managées peuvent être utilisées pour authentifier votre compte dans un runbook et doivent être conservées sauf si vous en créez une autre ou si vous n’en avez pas besoin. Le tableau ci-dessous récapitule les ressources du compte.

| Ressource | Description | |------| | ------| | Runbook AzureAutomationTutorial | Exemple de runbook graphique illustrant comment s’authentifier à l’aide d’un compte d’identification. Le runbook récupère toutes les ressources Resource Manager. | |AzureAutomationTutorialScript |Exemple de runbook PowerShell qui illustre comment s’authentifier à l’aide d’un compte d’identification. Le runbook obtient toutes les ressources de Resource Manager. | | AzureAutomationTutorialPython2Runbook | Exemple de runbook Python qui montre comment s’authentifier à l’aide d’un compte d’identification. Le runbook répertorie l’ensemble des groupes de ressources présents dans l’abonnement.|

> [!NOTE]
> Les runbooks de tutoriel n’ont pas été mis à jour pour s’authentifier à l’aide d’une identité managée. Consultez [Utilisation d’une identité managée affectée par le système](enable-managed-identity-for-automation.md#give-access-to-azure-resources-by-obtaining-a-token) ou [Utilisation d’une identité managée affectée par l’utilisateur](add-user-assigned-identity.md#give-identity-access-to-azure-resources-by-obtaining-a-token) pour savoir comment accorder l’accès à l’identité managée aux ressources et configurer vos runbooks pour l’authentification à l’aide de l’un ou l’autre type d’identité managée.

## <a name="next-steps"></a>Étapes suivantes

* Pour commencer à utiliser des runbooks PowerShell, consultez [Tutoriel : Créer un runbook PowerShell](./learn/powershell-runbook-managed-identity.md).
* Pour commencer à utiliser les runbooks de Flux de travail PowerShell, consultez [Tutoriel : Créer un runbook de Flux de travail PowerShell](learn/automation-tutorial-runbook-textual.md).
* Pour commencer à utiliser des runbooks Python 3, consultez le [Tutoriel : Créer un runbook Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
* Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](/powershell/module/az.automation).