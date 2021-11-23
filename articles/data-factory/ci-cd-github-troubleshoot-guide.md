---
title: Résoudre les problèmes liés à CI-CD, Azure DevOps et GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: Utilisez différentes méthodes pour résoudre les problèmes de CI-CD dans Azure Data Factory et Synapse Analytics.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 11/09/2021
ms.openlocfilehash: d3d792f6b51dc24b17d86d6a6fecc83697445a5a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157719"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-azure-data-factory-and-synapse-analytics"></a>Résoudre les problèmes de CD-rom, de Azure DevOps et de GitHub dans Azure Data Factory et Synapse Analytics 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dans cet article, nous allons explorer les méthodes courantes pour résoudre les problèmes d’intégration continue et déploiement continu (CI/CD), d’Azure DevOps et de GitHub dans Azure Data Factory et Synapse Analytics.

Si vous avez des questions ou des problèmes lors de l’utilisation de techniques de contrôle de code source ou DevOps, voici quelques articles qui pourront vous être utiles :

- Reportez-vous à [Contrôle de code source](source-control.md) pour savoir comment le contrôle de code source est pratiqué dans le service. 
- Reportez-vous à  [CI-CD](continuous-integration-delivery.md) pour en savoir plus sur la façon dont DevOps CI-CD est utilisé dans le service.

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Échec de la connexion au référentiel Git en raison d’un locataire différent

#### <a name="issue"></a>Problème

Vous rencontrez parfois des problèmes d’authentification, par exemple l’état HTTP 401. En particulier, si vous avez plusieurs locataires avec un compte invité, les choses peuvent devenir plus compliquées.

#### <a name="cause"></a>Cause

Nous avons observé que le jeton a été obtenu à partir du locataire d’origine, mais que le service se trouve dans le locataire invité et tente d’utiliser le jeton pour accéder à DevOps dans le locataire invité. Ce n’est pas le comportement normal.

#### <a name="recommendation"></a>Recommandation

Utilisez le jeton émis par le locataire invité. Par exemple, vous devez affecter le même service Azure Active Directory à votre locataire invité et à votre DevOps, pour qu’il puisse définir correctement le comportement des jetons et utiliser le bon locataire.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Les paramètres de modèle dans le fichier de paramètres ne sont pas valides

#### <a name="issue"></a>Problème

Si vous supprimez un déclencheur dans la branche Dev, qui est déjà disponible dans une branche Test ou Production avec la **même** configuration (par exemple, la fréquence et l’intervalle), le déploiement du pipeline de mise en production se déroule correctement et le déclencheur correspondant est supprimé dans les environnements respectifs. Toutefois, si vous utilisez une configuration **différente** (par exemple la fréquence et l’intervalle) pour le déclencheur dans les environnements Test/Production et supprimez le même déclencheur dans Dev, le déploiement échoue avec une erreur.

#### <a name="cause"></a>Cause

Le pipeline CI/CD échoue avec l’erreur suivante :

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recommandation

L’erreur se produit parce qu’il arrive souvent qu’un déclencheur, qui est paramétré, soit supprimé. Les paramètres ne sont pas disponibles dans le modèle Azure Resource Manager (ARM), car le déclencheur n’existe plus. Comme le paramètre ne figure plus dans le modèle ARM, nous devons mettre à jour les paramètres substitués dans le pipeline DevOps. Sinon, chaque fois que les paramètres du modèle ARM changent, ils doivent mettre à jour les paramètres substitués dans le pipeline DevOps (dans la tâche de déploiement).

### <a name="updating-property-type-is-not-supported"></a>La mise à jour du type de propriété n’est pas prise en charge

#### <a name="issue"></a>Problème

Le pipeline de mise en production CI/CD échoue avec l’erreur suivante :

```output
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
```

#### <a name="cause"></a>Cause

Cette erreur est due à la présence d’un runtime d’intégration portant le même nom, mais d’un type différent, dans l’instance de service cible. Le runtime d’intégration doit être du même type lors du déploiement.

#### <a name="recommendation"></a>Recommandation

- Consultez les [meilleures pratiques de CI/CD](continuous-integration-delivery.md#best-practices-for-cicd).

- Les runtimes d’intégration ne changent pas souvent et sont similaires dans toutes les phases de CI/CD. Ainsi, le service s’attend à ce que vous ayez le même nom et le même type de runtime d’intégration dans toutes les phases de CI/CD. Si le nom, les types et les propriétés diffèrent, assurez-vous qu’ils correspondent à la configuration du runtime d’intégration source et cible, puis déployez le pipeline de mise en production.

- Si vous voulez partager les runtimes d’intégration dans toutes les phases, envisagez d’utiliser une fabrique ternaire qui contiendra uniquement les runtimes d’intégration partagés. Vous pouvez utiliser cette fabrique partagée dans tous vos environnements en tant que type de runtime d’intégration lié.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Échec de la création ou de la mise à jour du document en raison d’une référence non valide

#### <a name="issue"></a>Problème

Lorsque vous tentez de publier des modifications, vous recevez le message d’erreur suivant :

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Cause

Vous avez détaché la configuration Git et l’avez reconfigurée avec l’indicateur « Importer des ressources » sélectionné, qui définit le service comme étant « synchronisé ». Cela signifie qu’aucune modification n’a été apportée pendant la publication.

#### <a name="resolution"></a>Résolution

Détachez la configuration Git et redéfinissez-la en veillant à ne PAS activer la case à cocher « Importer des ressources existantes ».

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>La fabrique de données n’a pas pu passer d’un groupe de ressources à un autre

#### <a name="issue"></a>Problème

Vous ne parvenez pas à déplacer une fabrique de données d’un groupe de ressources à un autre, l’opération échoue avec l’erreur suivante : `
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Résolution

Vous pouvez supprimer le runtime SSIS-IR et les runtimes d’intégration partagés pour permettre l’opération de déplacement. Si vous ne souhaitez pas supprimer les runtimes d’intégration, le meilleur moyen consiste à copier et cloner le document pour effectuer la copie et, une fois la copie terminée, à supprimer l’ancienne fabrique de données.

###  <a name="unable-to-export-and-import-arm-template"></a>Impossible d’exporter et importer un modèle ARM

#### <a name="issue"></a>Problème

Impossible d’exporter et importer un modèle ARM. Aucune erreur ne s’est produite sur le portail. Toutefois, dans la trace du navigateur, vous pouvez voir l’erreur suivante :

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Cause

Vous avez créé un rôle client en tant qu’utilisateur et il n’a pas l’autorisation nécessaire. Lorsque l'interface utilisateur est chargée, une série de valeurs de contrôle d'exposition est vérifiée. Dans ce cas, le rôle d’accès de l’utilisateur n’a pas l’autorisation d’accéder à l’API *queryFeaturesValue*. Pour accéder à cette API, la fonctionnalité des paramètres globaux est désactivée. Le chemin d’accès du code d’exportation ARM repose en partie sur la fonctionnalité des paramètres globaux.

#### <a name="resolution"></a>Résolution

Pour résoudre le problème, vous devez ajouter l’autorisation suivante à votre rôle : *Microsoft.DataFactory/factories/queryFeaturesValue/action*. Cette autorisation est incluse par défaut dans le rôle **contributeur Data Factory** pour Data Factory et le rôle **contributeur** dans Synapse Analytics.

###  <a name="cannot-automate-publishing-for-cicd"></a>Impossible d’automatiser la publication pour CI/CD 

#### <a name="cause"></a>Cause

Jusqu’à récemment, il était uniquement possible de publier un pipeline pour les déploiements en cliquant sur l’interface utilisateur dans le portail. À présent, ce processus peut être automatisé.

#### <a name="resolution"></a>Résolution

Le processus CI/CD a été amélioré. La fonctionnalité **Publication automatisée** prend, valide et exporte toutes les fonctionnalités du modèle ARM à partir de l’interface utilisateur. Elle rend la logique consommable par le biais d’un package npm disponible publiquement [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities). Cette méthode vous permet de déclencher ces actions programmatiquement et de ne pas avoir à accéder à l’interface utilisateur puis à cliquer sur un bouton. Elle confère à vos pipelines de CI/CD une **véritable** expérience d’intégration continue. Pour en savoir plus, suivez les [améliorations apportées au processus de publication pour CI/CD](./continuous-integration-delivery-improvements.md). 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>Publication impossible en raison de la limite de 4 Mo du modèle ARM  

#### <a name="issue"></a>Problème

Vous ne pouvez pas effectuer de déploiement, car vous avez atteint la limite Azure Resource Manager de 4 Mo pour la taille totale du modèle. Vous avez besoin d’une solution pour que le déploiement puisse avoir lieu une fois la limite franchie. 

#### <a name="cause"></a>Cause

Azure Resource Manager limite la taille du modèle à 4 Mo. Limitez la taille de votre modèle à 4 Mo et celle de chaque fichier de paramètres à 64 ko. La limite de 4 Mo s’applique à l’état final du modèle une fois développé avec les définitions de ressources itératives et les valeurs des variables et des paramètres. Toutefois, vous avez franchi la limite. 

#### <a name="resolution"></a>Résolution

Pour les solutions petites et moyennes, un modèle unique est plus facile à comprendre et à gérer. Vous pouvez voir toutes les ressources et valeurs dans un même fichier. Pour des scénarios avancés, les modèles liés permettent de diviser la solution en composants ciblés. Suivez les meilleures pratiques indiquées à la page [Utilisation de modèles liés et imbriqués](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise"></a>Impossible de se connecter à GIT Enterprise  

##### <a name="issue"></a>Problème

Vous ne pouvez pas vous connecter à GIT Enterprise en raison de problèmes d’autorisation. Vous pouvez voir une erreur telle que **422 - Impossible de traiter l’entité**.

#### <a name="cause"></a>Cause

* Vous n’avez pas configuré Oauth pour le service. 
* Votre URL est mal configurée. Le type de repoConfiguration doit être [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true).

#### <a name="resolution"></a>Résolution 

D’abord, vous accordez l’accès Oauth au service. Ensuite, vous devez utiliser l’URL correcte pour vous connecter à GIT Enterprise. La configuration doit être définie sur la ou les organisations clientes. Par exemple, le service essaiera d’abord *https://hostname/api/v3/search/repositories?q=user%3&lt;customer credential&gt;....* et échouera. Ensuite, il tentera  *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* , et réussira. 
 
### <a name="cannot-recover-from-a-deleted-instance"></a>Impossible de récupérer à partir d’une instance supprimée

#### <a name="issue"></a>Problème
Une instance du service, ou le groupe de ressources qui le contient, a été supprimée et doit être récupérée.

#### <a name="cause"></a>Cause

Il est possible de récupérer l’instance uniquement si le contrôle de code source a été configuré avec DevOps ou Git. Cette action récupère les dernières ressources publiées. Elle **ne restaure pas** le pipeline, le jeu de données ni le service lié non publiés. S’il n’existe aucun contrôle de code source, la récupération d’une instance supprimée à partir du serveur principal Azure n’est pas possible, car une fois que le service reçoit la commande Delete, l’instance est définitivement supprimée sans aucune sauvegarde.

#### <a name="resolution"></a>Résolution

Pour récupérer une instance de service supprimée pour laquelle le contrôle de code source est configuré, suivez les étapes ci-dessous :

 * Créez une nouvelle instance de service.

 * Reconfigurez Git avec les mêmes paramètres, mais veillez à importer les ressources existantes dans le référentiel sélectionné, puis choisissez Nouvelle branche.

 * Créez une demande de tirage pour fusionner les modifications apportées à la branche de collaboration, puis publier.

 * S’il existait une Integration Runtime auto-hébergée dans un espace de travail de la fabrique de données ou de la synapse, une nouvelle instance du runtime d’intégration doit être créée dans une nouvelle fabrique ou un nouvel espace de travail.  L’instance infrarouge locale ou de machine virtuelle doit être désinstallée et réinstallée, ainsi qu’une nouvelle clé obtenue. Une fois l’installation du nouveau Runtime d’intégration (IR) terminée, le service lié doit être mis à jour pour pointer vers le nouveau Runtime d’intégration (IR) et le test connecté à nouveau, sinon il échouera en indiquant une erreur de **référence non valide.**

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>Déploiement impossible vers une autre phase avec la méthode de publication automatique

#### <a name="issue"></a>Problème
Le client a suivi toute la procédure nécessaire, notamment l’installation du package NPM et la configuration d’une phase supérieure avec Azure DevOps mais le déploiement a échoué.

#### <a name="cause"></a>Cause

Bien que les packages npm puissent être consommés de différentes façons, l’un des principaux avantages est d’être consommé via Azure Pipelines. À chaque fusion dans votre branche de collaboration, vous pouvez déclencher un pipeline qui valide d’abord tout le code, puis exporte le modèle ARM dans un artefact de build qui peut être consommé par un pipeline de mise en production. Dans le pipeline de démarrage, le fichier YAML doit être valide et complet.


#### <a name="resolution"></a>Résolution

La section suivante est incorrecte, car le dossier package.json n’est pas valide.

```
- task: Npm@1
  inputs:
    command: 'custom'
    workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
    customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
  displayName: 'Validate'
```
DataFactory doit être inclus dans customCommand :
 *’run build validate $(Build.Repository.LocalPath)/DataFactory/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName’* . Veillez à ce que le fichier YAML généré pour une phase supérieure possède les artefacts JSON requis.

### <a name="git-repository-or-purview-connection-disconnected"></a>Connexion au référentiel Git ou à Purview déconnectée

#### <a name="issue"></a>Problème
Lors du déploiement d’une instance de service, le dépôt git ou la connexion portée est déconnectée.

#### <a name="cause"></a>Cause
Si l’option **Inclure dans le modèle  ARM** est sélectionnée pour le déploiement des paramètres globaux, votre instance de service est incluse dans le modèle ARM. Par conséquent, d’autres propriétés seront supprimées lors du déploiement.

#### <a name="resolution"></a>Résolution
Désélectionnez l’option **Inclure dans le modèle ARM** et déployez les paramètres globaux avec PowerShell, comme décrit dans Paramètres globaux dans CI/CD. 
 
### <a name="extra--left--displayed-in-published-json-file"></a>« [ » gauche supplémentaire affiché dans le fichier JSON publié

#### <a name="issue"></a>Problème
Lors de la publication avec DevOps, un « [ » supplémentaire s'affiche. Le service ajoute automatiquement un caractère « [ » supplémentaire dans ARMTemplate dans DevOps. Vous verrez une expression telle que « [[ » dans le fichier JSON.

#### <a name="cause"></a>Cause
Étant donné que « [ » est un caractère réservé pour ARM, un « [ » supplémentaire est ajouté automatiquement pour échapper « [ ».

#### <a name="resolution"></a>Résolution
Il s’agit d’un comportement normal lors du processus de publication pour CI/CD.
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>Exécuter **CI/CD** lors de la progression/mise en file d’attente de l’exécution du pipeline

#### <a name="issue"></a>Problème
Vous souhaitez exécuter CI/CD lors de la progression et de la mise en file d’attente de l’exécution du pipeline.

#### <a name="cause"></a>Cause
Lorsque le pipeline est en progression/file d’attente, vous devez d’abord le monitorer, ainsi que ses activités. Ensuite, vous pouvez décider d’attendre la fin de l’exécution du pipeline ou d’annuler cette exécution. 
 
#### <a name="resolution"></a>Résolution
Vous pouvez également monitorer le pipeline à l’aide du **Kit de développement logiciel (SDK)** , d’**Azure Monitor** ou de [Monitor](./monitor-visually.md). Pour obtenir d’autres conseils, référez-vous aux [meilleures pratiques CI/CD](./continuous-integration-delivery.md#best-practices-for-cicd). 

### <a name="perform-unit-testing-during-development-and-deployment"></a>Effectuer des **TESTS UNITAIRES** pendant le développement et le déploiement

#### <a name="issue"></a>Problème
Vous souhaitez effectuer des tests unitaires pendant le développement et le déploiement de pipelines.

#### <a name="cause"></a>Cause
Pendant les cycles de développement et de déploiement, vous souhaiterez peut-être effectuer un test unitaire de votre pipeline avant de publier manuellement ou automatiquement votre pipeline. L’automatisation des tests vous permet d’en exécuter davantage, plus rapidement, avec une répétabilité garantie. En retestant automatiquement tous vos pipelines avant le déploiement, vous bénéficiez d’une protection contre les erreurs de régression. Le test automatisé est un composant clé des approches de développement de logiciels CI/CD : l’inclusion de tests automatisés dans des pipelines de déploiement CI/CD peut améliorer considérablement la qualité. Les artefacts de pipeline testés sont réutilisés à long terme, pour vous permettre d’économiser du temps et de l’argent.  
 
#### <a name="resolution"></a>Résolution
Étant donné que les clients peuvent avoir des exigences différentes en matière de tests unitaires avec des ensembles de compétences différents, la pratique habituelle consiste à procéder comme suit :

1. Configurez un projet Azure DevOps CI/CD ou développez une stratégie de test pilotée par un Kit de développement logiciel (SDK) de type .NET/PYTHON/REST.
2. Pour CI/CD, créez un artefact de build contenant tous les scripts, puis déployez des ressources dans le pipeline de mise en production. Pour une approche pilotée par un Kit de développement logiciel (SDK), développez des unités de test avec PyTest dans Python, Nunit dans C# à l’aide d’un SDK NET, etc.
3. Exécutez des tests unitaires dans le cadre d’un pipeline de mise en production ou indépendamment avec un Kit de développement logiciel (SDK) Python/PowerShell/.NET/REST. 

Par exemple, si vous souhaitez supprimer des doublons dans un fichier, puis stocker le fichier organisé sous forme de table dans une base de données : pour tester le pipeline, configurez un projet CI/CD en utilisant Azure DevOps.
Configurez une étape de pipeline « TEST » où vous déployez votre pipeline développé. Configurez l’étape « TEST » pour exécuter des tests Python afin de vérifier que les données de la table sont celles que vous attendiez. Si vous n’utilisez pas CI/CD, utilisez **Nunit** pour déclencher des pipelines déployés avec les tests de votre choix. Lorsque vous êtes satisfait des résultats, vous pouvez enfin publier le pipeline dans une instance de production. 


### <a name="pipeline-runs-temporarily-fail-after-cicd-deployment-or-authoring-updates"></a>Les exécutions de pipeline échouent temporairement après le déploiement de CI/CD ou les mises à jour de création

#### <a name="issue"></a>Problème
Après un certain temps, les nouvelles exécutions du pipeline commencent à se dérouler sans aucune action de l’utilisateur après des échecs temporaires.

#### <a name="cause"></a>Cause

Il existe plusieurs scénarios qui peuvent déclencher ce comportement, qui impliquent tous une nouvelle version d’une ressource dépendante appelée par l’ancienne version de la ressource parente. Par exemple, supposons qu’un pipeline enfant existant appelé par le « pipeline d’exécution » soit mis à jour pour avoir les paramètres requis et que le pipeline parent existant soit mis à jour pour passer ces paramètres. Si le déploiement se produit pendant l’exécution d’un pipeline parent, mais avant l’activité d'**exécution du pipeline**, l’ancienne version du pipeline appellera la nouvelle version du pipeline enfant, et les paramètres attendus ne seront pas transmis. Cela entraîne l’échec du pipeline avec un *UserError*. Cela peut également se produire avec d’autres types de dépendances, par exemple, si une modification avec rupture est apportée au service lié pendant une exécution de pipeline qui y fait référence. 

#### <a name="resolution"></a>Résolution

Les nouvelles exécutions du pipeline parent démarrent automatiquement, donc aucune action n’est généralement nécessaire. Toutefois, pour éviter ces erreurs, les clients doivent prendre en compte les dépendances lors de la création et de la planification des déploiements afin d’éviter toute modification avec rupture. 

### <a name="cannot-parameterize-integration-run-time-in-linked-service"></a>Impossible de paramétrer l’exécution de l’intégration dans le service lié

#### <a name="issue"></a>Problème
Vous devez paramétriser l’exécution de l’intégration du service lié

#### <a name="cause"></a>Cause :
Cette fonctionnalité n'est pas prise en charge. 

#### <a name="resolution"></a>Résolution
Vous devez sélectionner manuellement et définir un runtime d’intégration. Vous pouvez également utiliser l’API PowerShell pour changer.  Cette modification peut avoir des implications en aval. 

### <a name="updatechange-integration-runtime-during-cicd"></a>Mise à jour/modification du runtime d’intégration au cours de CI/CD. 
 
#### <a name="issue"></a>Problème
Modification du nom du runtime d’intégration pendant le déploiement de CI/CD.  
 
#### <a name="cause"></a>Cause :
Le paramétrage d’une référence d’entité (runtime d’intégration dans le service lié, jeu de données dans l’activité, service lié dans le jeu de données) n’est pas pris en charge.  Si vous modifiez le nom du runtime pendant le déploiement, la ressource dépendante (qui référence le runtime d’intégration) devient incorrecte avec une référence non valide.  
 
#### <a name="resolution"></a>Résolution
Data Factory s’attend à ce que vous ayez le même nom et le même type de runtime d’intégration dans toutes les phases de CI/CD. 

### <a name="arm-template-deployment-failing-with-error-datafactorypropertyupdatenotsupported"></a>Échec de déploiement de modèle ARM avec l’erreur DataFactoryPropertyUpdateNotSupported

##### <a name="issue"></a>Problème
Le déploiement de modèle ARM échoue avec une erreur telle que DataFactoryPropertyUpdateNotSupported : La mise à jour du type de propriété n’est pas prise en charge. 

##### <a name="cause"></a>Cause
Le déploiement de modèle ARM tente de modifier le type d’un runtime d’intégration existant. Cela n’est pas autorisé et entraîne un échec de déploiement, car la fabrique de données requiert les mêmes nom et type de runtime d’intégration à toutes les étapes du processus d’intégration continue et livraison continue (CI/CD).

##### <a name="resolution"></a>Résolution
Si vous voulez partager les runtimes d’intégration dans toutes les phases, envisagez d’utiliser une fabrique ternaire qui contiendra uniquement les runtimes d’intégration partagés. Vous pouvez utiliser cette fabrique partagée dans tous vos environnements en tant que type de runtime d’intégration lié. Pour plus d’informations, consultez [Intégration continue et livraison continue dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-delivery#best-practices-for-cicd).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
