---
title: Création d’un compte Automation à l’aide d’un modèle Resource Manager
titleSuffix: Azure Automation
description: Cet article montre comment créer un compte Automation à l’aide du modèle Azure Resource Manager.
services: automation
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom: mvc, subject-armqs, mode-arm
ms.openlocfilehash: 66fb9a88761b47f0cc704fcf20dd29dd7768777a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487879"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>Création d’un compte Automation à l’aide d’un modèle Resource Manager

Azure Automation offre un service d’automatisation et de configuration cloud prenant en charge une gestion cohérente de vos environnements Azure et non-Azure. Cet article vous montre comment déployer un modèle Azure Resource Manager (modèle ARM) qui crée un compte Automation. L’utilisation d’un modèle ARM comprend moins d’étapes que les autres méthodes de déploiement. Le modèle JSON spécifie une valeur par défaut pour les paramètres susceptibles d’être utilisés comme configuration standard dans votre environnement. Vous pouvez stocker le modèle dans un compte de stockage Azure pour mettre en place un accès partagé dans votre organisation. Pour plus d’informations sur l’utilisation des modèles, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ce modèle échantillon effectue les actions suivantes :

* Automatise la création d’un espace de travail Azure Monitor Log Analytics.
* Automatise la création d’un compte Azure Automation.
* Lie le compte Automation à l’espace de travail Log Analytics.
* Ajoute des exemples de runbooks Automation au compte.

> [!NOTE]
> La création du compte d’identification Automation n’est pas prise en charge quand vous utilisez un modèle ARM. Pour créer manuellement un compte d’identification à partir du portail ou à l’aide de PowerShell, consultez [Créer un compte d’identification](create-run-as-account.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Si vous ne connaissez pas encore Azure Automation et Azure vous devez bien comprendre les détails de configuration. Ils peuvent vous aider à éviter des erreurs quand vous essayez de créer, de configurer et d’utiliser un espace de travail Log Analytics lié à votre nouveau compte Automation.

* Examinez les [détails supplémentaires](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) pour comprendre pleinement les options de configuration de l’espace de travail, telles que le mode de contrôle d’accès, le niveau tarifaire, la conservation des données et le niveau de réservation de capacité.

* Passez en revue les [mappages d’espace de travail](how-to/region-mappings.md) pour spécifier les régions prises en charge incluses ou figurant dans un fichier de paramètres. Seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation dans votre abonnement.

* Si vous ne connaissez pas les journaux Azure Monitor et n’avez pas encore déployé un espace de travail, consultez les [conseils pour la conception de l’espace de travail](../azure-monitor/logs/design-logs-deployment.md). Ce document vous aidera à vous familiariser avec le contrôle d’accès et à comprendre les stratégies d’implémentation de conception recommandées pour votre organisation.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans cet article provient des [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

Ressources Azure définies dans le modèle :

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces) : crée un espace de travail Log Analytics
* [**Microsoft.Automation/AutomationAccounts**](/azure/templates/microsoft.automation/automationaccounts) : crée un compte Azure Automation.
* [**Microsoft.Automation/automationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks) : crée un runbook de compte Azure Automation.

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez le bouton **Déployer sur Azure** ci-dessous pour vous connecter à Azure et ouvrir le modèle Resource Manager.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. Entrez ou sélectionnez les valeurs suivantes :

    |Propriété |Description |
    |---|---|
    |Abonnement |Sélectionnez votre abonnement Azure dans la liste déroulante.|
    |Resource group |Dans la liste déroulante, sélectionnez votre groupe de ressources existant ou **Créer**.|
    |Région |Cette valeur sera renseignée.|
    |Nom de l’espace de travail |Entrez un nom pour votre nouvel espace de travail Log Analytics.|
    |Sku | La valeur par défaut du niveau de tarification par Go publié dans le modèle de tarification d’avril 2018. Si vous voulez créer ou configurer un espace de travail Log Analytics dans un abonnement pour lequel le modèle de tarification d’avril 2018 a été choisi, le seul niveau tarifaire Log Analytics valide est `PerGB2018`.|
    |Conservation des données |La valeur par défaut est 30 jours.|
    |Emplacement |La valeur est renseignée automatiquement à l’aide de l’emplacement utilisé pour le groupe de ressources.|
    |Nom du compte Automation | Entrez un nom pour votre nouveau compte Automation.|
    |Nom de l’exemple de runbook graphique | Laissez la valeur actuelle.|
    |Description de l’exemple de runbook graphique | Laissez la valeur actuelle.|
    |Nom de l’exemple de runbook PowerShell | Laissez la valeur actuelle.|
    |Description de l’exemple de runbook PowerShell | Laissez la valeur actuelle.|
    |Nom de l’exemple de Python2Runbook |Laissez la valeur actuelle.|
    |Description de l’exemple de Python2Runbook |Laissez la valeur actuelle.|
    |Emplacement des artefacts |Laissez tel quel. <sup>*</sup> URI vers l’emplacement des artefacts.|
    |Jeton SAS de l’emplacement des artefacts | Laisser vide. SasToken requis pour accéder à `_artifactsLocation`. Lorsque le modèle est déployé à l’aide des scripts fournis, un `sasToken` est généré automatiquement.|

    <sup>*</sup>Lorsque vous tentez d’exécuter le modèle ARM à partir de PowerShell, de l’interface CLI ou de la fonction Modèles du portail, si le paramètre `_artifactsLocation` n’est pas correctement défini, vous recevez un message d’erreur similaire au suivant :
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    Pour éviter cette erreur,, lors de l’exécution à partir de la fonction Modèles du portail, indiquez ce qui suit pour le `_artifactsLocation`paramètre - `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Quand vous exécutez à partir de PowerShell, incluez le paramètre et sa valeur `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Quand vous exécutez à partir d’Azure CLI, incluez le paramètre et sa valeur `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Pour des références sur PowerShell/CLI, consultez [Créer un compte Azure Automation (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/) sous la section **Utiliser le modèle**.

1. Sélectionnez **Vérifier + créer**, puis **Créer**. Le déploiement peut prendre plusieurs minutes. Une fois l’opération terminée, le résultat ressemble à l’image suivante :

    ![Exemple de résultat lorsque le déploiement est achevé](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

1. Une fois le déploiement achevé, vous recevez une notification **Déploiement réussi** avec un lien **Accéder à la ressource**. La page de votre **groupe de ressources** affiche vos nouvelles ressources. Dans la liste, sélectionnez votre nouveau compte Automation.

1. À gauche, sous **Automatisation de processus**, sélectionnez **Runbooks**. Dans la page **Runbooks**, trois exemples de runbooks créés avec le compte Automation sont répertoriés.

    ![Runbooks de tutoriel créés avec le compte Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. À gauche, sous **Ressources associées**, sélectionnez **Espace de travail lié**. La page **Espace de travail lié** affiche l’espace de travail Log Analytics que vous avez spécifié précédemment, lié à votre compte Automation.

    ![Compte Automation lié à l’espace de travail Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurez des paramètres de diagnostic](automation-manage-send-joblogs-log-analytics.md) pour votre compte Automation, afin d’envoyer l’état de la tâche du Runbook et les flux de tâches à l’espace de travail Log Analytics lié.
