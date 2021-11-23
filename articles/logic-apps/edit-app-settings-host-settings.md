---
title: Modifier les paramètres d’exécution et d’environnement dans Azure Logic Apps monolocataire
description: Modifiez les paramètres d’exécution et d’environnement pour les applications logiques dans Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: eec377641eca7dd31ab04e9f6cdee949352d4a61
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491143"
---
# <a name="edit-host-and-app-settings-for-logic-apps-in-single-tenant-azure-logic-apps"></a>Modifier les paramètres de l’hôte et de l’application pour les applications logiques dans Azure Logic Apps monolocataire

Dans Azure Logic Apps *monolocataire*, les *paramètres d’application* pour une application logique spécifient les options de configuration globale qui affectent *tous les workflows* de cette application logique. Toutefois, ces paramètres s’appliquent *uniquement* lorsque ces workflows s’exécutent dans votre *environnement de développement local*. Lors de l’exécution locale, les workflows peuvent accéder à ces paramètres d’application en tant que *variables d’environnement locales*, qui sont utilisées par les outils de développement locaux pour des valeurs qui peuvent souvent changer d’un environnement à l’autre. Par exemple, ces valeurs peuvent contenir des chaînes de connexion. Lorsque vous déployez dans Azure, les paramètres de l’application sont ignorés et ne sont pas inclus dans votre déploiement.

Votre application logique a également des *paramètres d’hôte* qui spécifient les paramètres de configuration du runtime et les valeurs qui s’appliquent à *tous les workflows* de cette application logique, par exemple, les valeurs par défaut pour le débit, la capacité, la taille des données, etc., qu’*elle s’exécute localement ou dans Azure*.

<a name="app-settings-parameters-deployment"></a>

## <a name="app-settings-parameters-and-deployment"></a>Paramètres de l’application, paramètres et déploiement

Dans Azure Logic Apps *mutualisé*, le déploiement dépend de modèles Azure Resource Manager (modèles ARM), qui combinent et gèrent l’approvisionnement des ressources à la fois pour les applications logiques et l’infrastructure. Cette conception pose un défi lorsque vous devez gérer des variables d’environnement pour des applications logiques dans différents environnements de développement, de test et de production. Tout ce qui se trouve dans un modèle ARM est défini lors du déploiement. Si vous ne devez modifier ne serait-ce qu’une seule variable, vous devez tout redéployer.

Dans Azure Logic Apps *monolocataire*, le déploiement devient plus facile, car vous pouvez séparer l’approvisionnement des ressources entre les applications et l’infrastructure. Vous pouvez utiliser des *paramètres* pour extraire les valeurs qui peuvent changer d’un environnement à l’autre. En définissant des paramètres à utiliser dans vos workflows, vous pouvez d’abord vous concentrer sur la conception de vos workflows, puis insérer ultérieurement vos variables spécifiques à l’environnement. Vous pouvez appeler et référencer vos variables d’environnement au moment de l’exécution à l’aide des paramètres d’application et des paramètres. De cette façon, vous n’avez pas besoin de redéployer aussi souvent.

Les paramètres d’application s’intègrent à Azure Key Vault. Vous pouvez [référencer directement des chaînes sécurisées](../app-service/app-service-key-vault-references.md), telles que des chaînes de connexion et des clés. Comme pour les modèles Azure Resource Manager (modèles ARM), où vous pouvez définir des variables d’environnement au moment du déploiement, vous pouvez définir des paramètres d’application dans la [définition de workflow de votre application logique](/azure/templates/microsoft.logic/workflows). Vous pouvez ensuite capturer des valeurs d’infrastructure générées dynamiquement, comme des points de terminaison de connexion, des chaînes de stockage, etc. Toutefois, les paramètres d’application ont des limitations de taille et ne peuvent pas être référencés à partir de certaines zones dans Azure Logic Apps.

Pour plus d’informations sur la configuration de vos applications logiques pour le déploiement, consultez la documentation suivante :

- [Créer des paramètres pour les valeurs qui changent dans les workflows entre les environnements pour Azure Logic Apps monolocataire](parameterize-workflow-app.md)
- [Présentation du déploiement DevOps pour les applications logiques basées monolocataires](devops-deployment-single-tenant-azure-logic-apps.md)
- [Configurer le déploiement DevOps pour les applications logiques monolocataires](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="visual-studio-code-project-structure"></a>Structure de projet Visual Studio Code

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="reference-local-settings-json"></a>

## <a name="reference-for-app-settings---localsettingsjson"></a>Informations de référence sur les paramètres d’application - local.settings.json

Dans Visual Studio Code, au niveau de la racine de votre projet d’application logique, le fichier **local.settings.json** contient des options de configuration globale qui affectent *tous les workflows* de cette application logique en cours d’exécution dans votre environnement de développement local. Lorsque vos workflows s’exécutent localement, ces paramètres sont accessibles en tant que variables d’environnement locales et leurs valeurs peuvent souvent changer entre les différents environnements dans lesquels vous exécutez vos workflows. Pour afficher et gérer ces paramètres, consultez [Gérer les paramètres d’application - local.settings.json](#manage-app-settings).

Les paramètres de l’application dans Azure Logic Apps fonctionnent de la même façon que les paramètres de l’application dans Azure Functions ou Azure Web Apps. Si vous avez déjà utilisé ces autres services, vous connaissez peut-être déjà les paramètres d’application. Pour plus d’informations, consultez [Référence des paramètres d’application pour Azure Functions](../azure-functions/functions-app-settings.md) et [Utiliser Azure Functions Core Tools - Fichier de paramètres local](../azure-functions/functions-develop-local.md#local-settings-file).

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `AzureWebJobsStorage` | None | Définit la chaîne de connexion pour un compte de stockage Azure. |
| `Workflows.<workflowName>.FlowState` | Aucun | Définit l’état pour <*workflowName*>. |
| `Workflows.<workflowName>.RuntimeConfiguration.RetentionInDays` | Aucun | Définit les options d’opération pour <*workflowName*>. |
| `Workflows.Connection.AuthenticationAudience` | Aucun | Définit le public pour l’authentification d’une connexion hébergée sur Azure. |
| `Workflows.WebhookRedirectHostUri` | Aucun | Définit le nom d’hôte à utiliser pour les URL de rappel de webhook. |
| `WEBSITE_LOAD_ROOT_CERTIFICATES` | Aucun | Définit les empreintes pour que les certificats racines soient approuvés. |
||||

<a name="manage-app-settings"></a>

## <a name="manage-app-settings---localsettingsjson"></a>Gérer les paramètres d’application - local.settings.js

Pour ajouter, mettre à jour ou supprimer des paramètres d’application, sélectionnez et consultez les sections suivantes pour le modèle Visual Studio Code, portail Azure, Azure CLI ou ARM (Bicep). Pour les paramètres d’application spécifiques à Logic Apps, consultez le [Guide de référence des paramètres d’application disponibles - local.settings.json](#reference-local-settings-json).

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pour passer en revue les paramètres d’application de votre application logique dans Visual Studio Code, procédez comme suit :

1. Dans votre projet d’application logique, au niveau du projet racine, recherchez et ouvrez le fichier **local.settings.json**.

1. Dans l’objet `Values`, examinez les paramètres de l’application pour votre application logique.

   Pour plus d’informations sur ces paramètres, consultez le [Guide de référence des paramètres d’application disponibles - local.settings.json](#reference-local-settings-json).

Pour ajouter un paramètre d’application, effectuez les étapes suivantes :

1. Dans le fichier **local.settings.json**, recherchez l’objet `Values`.

1. Dans l’objet `Values`, ajoutez le paramètre d’application que vous souhaitez appliquer lors de l’exécution locale dans Visual Studio Code. Certains paramètres vous permettent d’ajouter un paramètre pour un workflow spécifique, par exemple :

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "Workflows.WorkflowName1.FlowState" : "Disabled",
         <...>
     }
   }
   ```

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Pour passer en revue les paramètres d’application pour votre application logique basée sur un seul locataire dans le portail Azure, procédez comme suit :

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com/), recherchez et ouvrez votre application logique.

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Configuration**.

1. Sur la page **Configuration**, sous l’onglet **Paramètres de l’application**, examinez les paramètres de l’application pour votre application logique.

   Pour plus d’informations sur ces paramètres, consultez le [Guide de référence des paramètres d’application disponibles - local.settings.json](#reference-local-settings-json).

1. Pour afficher toutes les valeurs, sélectionnez **Afficher les valeurs**. Ou, pour afficher une valeur unique, sélectionnez cette valeur.

Pour ajouter un paramètre, effectuez les étapes suivantes :

1. Sous l’onglet **Paramètres de l’application**, sous **Paramètres de l’application**, sélectionnez **Nouveau paramètre d’application**.

1. Pour **Nom**, entrez la *clé* ou le nom de votre nouveau paramètre.

1. Pour **Valeur**, entrez la valeur de votre nouveau paramètre.

1. Quand vous êtes prêt à créer votre paire *clé-valeur*, sélectionnez **OK**.

:::image type="content" source="./media/edit-app-settings-host-settings/portal-app-settings-values.png" alt-text="Capture d’écran montrant le portail Azure et le volet de configuration avec les paramètres et les valeurs d’application pour une application logique monolocataire." lightbox="./media/edit-app-settings-host-settings/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour passer en revue les paramètres actuels de votre application avec Azure CLI, exécutez la commande `az logicapp config appsettings list`. Assurez-vous que votre commande comprend les paramètres `--name -n` et `--resource-group -g`, par exemple :

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

Pour plus d’informations sur ces paramètres, consultez le [Guide de référence des paramètres d’application disponibles - local.settings.json](#reference-local-settings-json).

Pour ajouter ou mettre à jour un paramètre d’application à l’aide d’Azure CLI, exécutez la commande `az logicapp config appsettings set`. Assurez-vous que votre commande comprend les paramètres `--name n` et `--resource-group -g`. Par exemple, la commande suivante crée un paramètre avec une clé nommée `CUSTOM_LOGIC_APP_SETTING` avec la valeur `12345` :

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

---

<a name="reference-host-json"></a>

## <a name="reference-for-host-settings---hostjson"></a>Référence pour les paramètres de l’hôte - host.json

Dans Visual Studio Code, au niveau de la racine de votre projet d’application logique, le fichier de métadonnées **host.json** contient les paramètres d’exécution et les valeurs par défaut qui s’appliquent à *tous les workflows* d’une ressource d’application logique, qu’ils s’exécutent localement ou dans Azure. Pour afficher et gérer ces paramètres, consultez [Gérer les paramètres de l’hôte - host.json](#manage-host-settings). Vous trouverez également des informations sur les limites associées dans la documentation [Limites et configuration d’Azure Logic Apps](logic-apps-limits-and-config.md#definition-limits).

<a name="job-orchestration"></a>

### <a name="job-orchestration-throughput"></a>Débit de l’orchestration de travail

Ces paramètres affectent le débit et la capacité d’Azure Logic Apps monolocataire à exécuter des opérations de workflow.

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Jobs.BackgroundJobs.DispatchingWorkersPulseInterval` | `00:00:01` <br>(1 s) | Définit l’intervalle d’interrogation des répartiteurs de travaux pour la file d’attente des travaux lorsque l’interrogation précédente ne retourne aucun travail. Les répartiteurs de travaux interrogent la file d’attente immédiatement lorsque l’interrogation précédente retourne un travail. |
| `Jobs.BackgroundJobs.NumWorkersPerProcessorCount` | `192` instances de worker répartiteur | Définit le nombre d’*instances de worker répartiteur* ou de *répartiteurs de travail* à avoir par cœur de processeur. Cette valeur affecte le nombre d’exécutions de workflow par cœur. |
| `Jobs.BackgroundJobs.NumPartitionsInJobTriggersQueue` | `1` file d’attente de travaux | Définit le nombre de files d’attente de travaux surveillées par les répartiteurs de travail pour les travaux à traiter. Cette valeur affecte également le nombre de partitions de stockage pour lesquelles des files d’attente de travaux existent. |
| `Jobs.BackgroundJobs.NumPartitionsInJobDefinitionsTable` | `4` partitions de travail | Définit le nombre de partitions de travail dans la table de définition du travail. Cette valeur contrôle la quantité du débit d’exécution qui est affectée par les limites de stockage des partitions. |
||||

<a name="run-duration-history"></a>

### <a name="run-duration-and-history"></a>Durée d’exécution et historique

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRetentionThreshold` | `90.00:00:00` <br>(90 jours) | Définit la durée de conservation de l’historique des exécutions du workflow après le démarrage d’une exécution. |
| `Runtime.Backend.FlowRunTimeout` | `90.00:00:00` <br>(90 jours) | Définit la durée pendant laquelle un workflow peut continuer à s’exécuter avant de forcer un délai d’expiration. <p><p>**Important** : Assurez-vous que cette valeur est inférieure ou égale à la valeur `Runtime.FlowRetentionThreshold`. Sinon, les historiques des exécutions peuvent être supprimés avant la fin des travaux associés. |
||||
   
<a name="run-actions"></a>

### <a name="run-actions"></a>Actions d’exécution

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.ActionJobExecutionTimeout` | `00:10:00` <br>(10 minutes) | Définit la durée d’exécution d’un travail d’action de workflow avant expiration et nouvelle tentative. |
||||

<a name="inputs-outputs"></a>

### <a name="inputs-and-outputs"></a>Entrées et sorties

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRunActionJob.MaximumActionResultSize` | `209715200` octets | Définit la taille maximale, en octets, que les entrées et sorties combinées peuvent avoir dans une action. |
| `Runtime.ContentLink.MaximumContentSizeInBytes` | `104857600` caractères | Définit la taille maximale (en caractères) qu’une entrée ou une sortie peut avoir dans un déclencheur ou une action. |
||||

<a name="pagination"></a>

### <a name="pagination"></a>Pagination

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPageCount` | `1000` pages | Lorsque la pagination est prise en charge et activée sur une opération, définit le nombre maximal de pages à retourner ou à traiter au moment de l’exécution. |
||||

<a name="chunking"></a>

### <a name="chunking"></a>Segmentation

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRunRetryableActionJobCallback.MaximumContentLengthInBytesForPartialContent` | `1073741824` octets | Lorsque la segmentation est prise en charge et activée sur une opération, définit la taille maximale en octets pour le contenu téléchargé ou chargé. |
| `Runtime.FlowRunRetryableActionJobCallback.MaxChunkSizeInBytes` | `52428800` octets | Lorsque la segmentation est prise en charge et activée sur une opération, définit la taille maximale en octets pour chaque bloc. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumRequestCountForPartialContent` | `1000` requêtes | Lorsque la segmentation est prise en charge et activée sur une opération, définit le nombre maximal de requêtes qu’une exécution d’action peut effectuer pour télécharger du contenu. |
||||

<a name="trigger-concurrency"></a>

### <a name="trigger-concurrency"></a>Déclencheur simultané

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Trigger.MaximumRunConcurrency` | `100` exécutions | Définit le nombre maximal d’exécutions simultanées qu’un déclencheur peut démarrer. Cette valeur s’affiche dans la définition de concurrence du déclencheur. |
| `Runtime.Trigger.MaximumWaitingRuns` | `200` exécutions | Définit le nombre maximal d’exécutions qui peuvent être en attente une fois que les exécutions simultanées ont atteint la valeur maximale. Cette valeur s’affiche dans la définition de concurrence du déclencheur. |
||||

<a name="for-each-loop"></a>

### <a name="for-each-loops"></a>Boucles Foreach

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.FlowDefaultForeachItemsLimit` | `100000` <br>(100 000 éléments de tableau) | Pour un *workflow avec état*, définit le nombre maximal d’éléments de tableau à traiter dans une boucle `For each`. |
| `Runtime.Backend.Stateless.FlowDefaultForeachItemsLimit` | `100` éléments | Pour un *workflow sans état*, définit le nombre maximal d’éléments de tableau à traiter dans une boucle `For each`. |
| `Runtime.Backend.ForeachDefaultDegreeOfParallelism` | `20` itérations | Définit le nombre par défaut d’itérations simultanées, ou degrés de parallélisme, dans une boucle `For each`. Pour une exécution séquentielle, définissez la valeur sur `1`. |
| `Runtime.Backend.FlowDefaultSplitOnItemsLimit` | `100000` <br>(100 000 éléments de tableau) | Définit le nombre maximal d’éléments de tableau à décomposer ou à fractionner en plusieurs instances de workflow en fonction du paramètre `SplitOn`. |
||||

<a name="until-loop"></a>

### <a name="until-loops"></a>Boucles until

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.MaximumUntilLimitCount` | `5000` itérations | Pour un *workflow avec état*, définit le nombre maximal possible de la propriété `Count` dans une action `Until`. |
| `Runtime.Backend.Stateless.MaximumUntilLimitCount` | `100` itérations | Pour un *workflow sans état*, définit le nombre maximal possible de la propriété `Count` dans une action `Until`. |
| `Runtime.Backend.Stateless.FlowRunTimeout` | `00:05:00` <br>(5 min) | Définit le délai d’attente maximal pour une boucle `Until` dans un workflow sans état. |
||||

<a name="variables"></a>

### <a name="variables"></a>Variables

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.DefaultAppendArrayItemsLimit` | `100000` <br>(100 000 éléments de tableau) | Définit le nombre maximal d’éléments dans une variable avec le type tableau. |
| `Runtime.Backend.VariableOperation.MaximumVariableSize` | Workflow avec état : `104857600` caractères | Définit la taille maximale, en caractères, du contenu qu’une variable peut stocker quand elle est utilisée dans un flux de travail avec état. |
| `Runtime.Backend.VariableOperation.MaximumStatelessVariableSize` | Workflow sans état : `1024` caractères | Définit la taille maximale, en caractères, du contenu qu’une variable peut stocker quand elle est utilisée dans un flux de travail sans état. |
||||

<a name="http-webhook"></a>

### <a name="http-operations"></a>Opérations HTTP

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.HttpOperation.RequestTimeout` | `00:03:45` <br>(3 min et 45 s) | Définit la valeur du délai d’expiration de la requête pour les déclencheurs et les actions HTTP. |
| `Runtime.Backend.HttpOperation.MaxContentSize` | `104857600` octets | Définit la taille maximale de la requête, en octets, pour les déclencheurs et les actions HTTP. |
| `Runtime.Backend.HttpOperation.DefaultRetryCount` | `4` nouvelles tentatives | Définit le nombre de nouvelles tentatives par défaut pour les déclencheurs et les actions HTTP. |
| `Runtime.Backend.HttpOperation.DefaultRetryInterval` | `00:00:07` <br>(7 s) | Définit l’intervalle avant nouvelle tentative par défaut pour les déclencheurs et les actions HTTP. |
| `Runtime.Backend.HttpOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 heure) | Définit l’intervalle maximal avant nouvelle tentative pour les déclencheurs et les actions HTTP. |
| `Runtime.Backend.HttpOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 s) | Définit l’intervalle minimal avant nouvelle tentative pour les déclencheurs et les actions HTTP. |
||||

<a name="http-webhook"></a>

### <a name="http-webhook-operations"></a>Opérations de webhook HTTP

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.HttpWebhookOperation.RequestTimeout` | `00:02:00` <br>(2 minutes) | Définit la valeur du délai d’expiration de la requête pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.MaxContentSize` | `104857600` octets | Définit la taille maximale de la requête, en octets, pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryCount` | `4` nouvelles tentatives | Définit le nombre de nouvelles tentatives par défaut pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryInterval` | `00:00:07` <br>(7 s) | Définit l’intervalle de nouvelle tentative par défaut pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 heure) | Définit l’intervalle de nouvelle tentative maximal pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 s) | Définit l’intervalle de nouvelle tentative minimal pour les déclencheurs de webhook et les actions HTTP. |
| `Runtime.Backend.HttpWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 heure) | Définit l’intervalle de réveil par défaut pour les travaux d’action et de déclencheur de webhook HTTP. |
||||

<a name="built-in-azure-functions"></a>

### <a name="built-in-azure-functions-operations"></a>Opérations Azure Functions intégrées

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.FunctionOperation.RequestTimeout` | `00:03:45` <br>(3 min et 45 s) | Définit la valeur du délai d’expiration de la requête pour les actions Azure Functions. |
| `Runtime.Backend.FunctionOperation.MaxContentSize` | `104857600` octets | Définit la taille maximale de la requête en octets pour les actions Azure Functions. |
| `Runtime.Backend.FunctionOperation.DefaultRetryCount` | `4` nouvelles tentatives | Définit le nombre de nouvelles tentatives par défaut pour les actions Azure Functions. |
| `Runtime.Backend.FunctionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 s) | Définit l’intervalle avant nouvelle tentative par défaut pour les actions Azure Functions. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 heure) | Définit l’intervalle avant nouvelle tentative maximal pour les actions Azure Functions. |
| `Runtime.Backend.FunctionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 s) | Définit l’intervalle avant nouvelle tentative minimal pour les actions Azure Functions. |
||||

<a name="built-in-sql"></a>

### <a name="built-in-sql-operations"></a>Opérations SQL intégrées

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.ServiceProviders.Sql.QueryExecutionTimeout` | `00:00:30` <br>(30 s) | Définit la valeur du délai d’expiration de la requête pour les opérations du fournisseur de services SQL. |
||||

<a name="built-in-service-bus"></a>

### <a name="built-in-azure-service-bus-operations"></a>Opérations Azure Service Bus intégrées

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `ServiceProviders.ServiceBus.MessageSenderOperationTimeout` | `00:01:00` <br>(1 min) | Définit le délai d’attente pour l’envoi de messages avec l’opération Service Bus intégrée. |
| `Runtime.ServiceProviders.ServiceBus.MessageSenderPoolSizePerProcessorCount` | `64` expéditeurs de messages | Définit le nombre d’expéditeurs de messages Azure Service Bus par cœur de processeur à utiliser dans le pool d’expéditeurs de messages. |
||||

<a name="managed-api-connector"></a>

### <a name="managed-api-connector-operations"></a>Opérations du connecteur d’API géré

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.Backend.ApiConnectionOperation.RequestTimeout` | `00:02:00` <br>(2 minutes) | Définit la valeur du délai d’expiration de la requête pour les déclencheurs et les actions du connecteur d’API géré. |
| `Runtime.Backend.ApiConnectionOperation.MaxContentSize` | `104857600` octets | Définit la taille maximale de la requête, en octets, pour les déclencheurs et les actions du connecteur d’API géré. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryCount` | `4` nouvelles tentatives | Définit le nombre de nouvelles tentatives par défaut pour les déclencheurs et les actions du connecteur d’API géré. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryInterval` | `00:00:07` <br>(7 s) | Définit l’intervalle avant nouvelle tentative par défaut pour les déclencheurs et les actions du connecteur d’API géré. |
| `Runtime.Backend.ApiWebhookOperation.DefaultRetryMaximumInterval` | `01:00:00` <br>(1 jour) | Définit l’intervalle maximal avant nouvelle tentative pour les déclencheurs de webhook et actions du connecteur d’API géré. |
| `Runtime.Backend.ApiConnectionOperation.DefaultRetryMinimumInterval` | `00:00:05` <br>(5 s) | Définit l’intervalle minimal avant nouvelle tentative pour les déclencheurs et les actions du connecteur d’API géré. |
| `Runtime.Backend.ApiWebhookOperation.DefaultWakeUpInterval` | `01:00:00` <br>(1 jour) | Définit l’intervalle de réveil par défaut pour les travaux de déclencheur de webhook et actions du connecteur d’API géré. |
||||

<a name="blob-storage"></a>

### <a name="blob-storage"></a>Stockage d'objets blob

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.ContentStorage.RequestOptionsServerTimeout` | `00:00:30` <br>(30 s) | Définit la valeur du délai d’attente pour les requêtes d’objet blob à partir du runtime Azure Logic Apps. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:02:00` <br>(2 minutes) | Définit la valeur du délai d’attente de l’opération, y compris les nouvelles tentatives, pour les demandes de stockage de file d’attente et de table à partir du runtime Azure Logic Apps. |
| `Runtime.ContentStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 s) | Définit l’intervalle de backoff entre les nouvelles tentatives envoyées au stockage d’objets blob. |
| `Runtime.ContentStorage.RequestOptionsMaximumAttempts` | `4` nouvelles tentatives | Définit le nombre maximal de nouvelles tentatives envoyées au stockage de table et de file d’attente. |
||||

<a name="store-inline-or-blob"></a>

### <a name="store-content-inline-or-use-blobs"></a>Stocker du contenu inline ou utiliser des objets blob

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.FlowRunEngine.ForeachMaximumItemsForContentInlining` | `20` éléments | Lorsqu’une boucle `For each` est en cours d’exécution, la valeur de chaque élément est stockée inline avec d’autres métadonnées dans le stockage de table ou séparément dans le stockage d’objets blob. Définit le nombre d’éléments à stocker inline avec d’autres métadonnées. |
| `Runtime.FlowRunRetryableActionJobCallback.MaximumPagesForContentInlining` | `20` pages | Définit le nombre maximal de pages à stocker en tant que contenu inline dans le stockage de table avant d’être stocké dans le stockage d’objets blob. |
| `Runtime.FlowTriggerSplitOnJob.MaximumItemsForContentInlining` | `40` éléments | Lorsque le paramètre `SplitOn` dégroupe des éléments de tableau en plusieurs instances de workflow, la valeur de chaque élément est stockée inline avec d’autres métadonnées dans le stockage de table ou séparément dans le stockage d’objets blob. Définit le nombre d’éléments à stocker inline. |
| `Runtime.ScaleUnit.MaximumCharactersForContentInlining` | `8192` caractères | Définit le nombre maximal de caractères d’entrée et de sortie d’opération à stocker inline dans le stockage de table avant de les stocker dans le stockage d’objets blob. |
||||

<a name="table-queue-storage"></a>

### <a name="table-and-queue-storage"></a>Stockage Table et File d’attente

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.DataStorage.RequestOptionsServerTimeout` | `00:00:16` <br>(16 s) | Définit la valeur du délai d’attente pour les demandes de stockage de table et de file d’attente à partir du runtime Azure Logic Apps. |
| `Runtime.DataStorage.RequestOptionsMaximumExecutionTime` | `00:00:45` <br>(45 s) | Définit la valeur du délai d’attente de l’opération, y compris les nouvelles tentatives, pour les demandes de stockage de file d’attente et de table à partir du runtime Azure Logic Apps. |
| `Runtime.DataStorage.RequestOptionsDeltaBackoff` | `00:00:02` <br>(2 s) | Définit l’intervalle d’interruption entre les nouvelles tentatives envoyées au stockage de table et de file d’attente. |
| `Runtime.DataStorage.RequestOptionsMaximumAttempts` | `4` nouvelles tentatives | Définit le nombre maximal de nouvelles tentatives envoyées au stockage de table et de file d’attente. |
||||

<a name="retry-policy"></a>

### <a name="retry-policy-for-all-other-operations"></a>Stratégie de nouvelle tentative pour toutes les autres opérations

| Paramètre | Valeur par défaut | Description |
|---------|---------------|-------------|
| `Runtime.ScaleMonitor.MaxPollingLatency` | `00:00:30` <br>(30 s) | Définit la latence d’interrogation maximale pour la mise à l’échelle du runtime. |
| `Runtime.Backend.Operation.MaximumRetryCount` | `90` nouvelles tentatives | Définit le nombre maximal de nouvelles tentatives dans la définition de stratégie de nouvelle tentative pour une opération de workflow. |
| `Runtime.Backend.Operation.MaximumRetryInterval` | `01:00:00:01` <br>(1 jour et 1 seconde) | Définit l’intervalle maximal dans la définition de stratégie de nouvelle tentative pour une opération de workflow. |
| `Runtime.Backend.Operation.MinimumRetryInterval` | `00:00:05` <br>(5 s) | Définit l’intervalle minimal dans la définition de stratégie de nouvelle tentative pour une opération de workflow. |
||||

<a name="manage-host-settings"></a>

## <a name="manage-host-settings---hostjson"></a>Gérer les paramètres de l’hôte - host.json

Vous pouvez ajouter, mettre à jour ou supprimer des paramètres d’hôte, qui spécifient les paramètres de configuration du runtime et les valeurs qui s’appliquent à *tous les workflows* de cette application logique, comme les valeurs par défaut pour le débit, la capacité, la taille des données, etc., qu’*elles s’exécutent localement ou dans Azure*. Pour les paramètres d’hôte spécifiques à Logic Apps, consultez le [Guide de référence pour les paramètres de déploiement et d’exécution disponibles - host.json](#reference-host-json).

### <a name="visual-studio-code---hostjson"></a>Visual Studio Code - host.json

Pour passer en revue les paramètres d’hôte de votre application logique dans Visual Studio Code, procédez comme suit :

1. Dans votre projet d’application logique, au niveau du projet racine, recherchez et ouvrez le fichier **host.json**.

1. Dans l’objet `extensions`, sous `workflows` et `settings`, examinez tous les paramètres d’hôte qui ont été précédemment ajoutés pour votre application logique. Sinon, l’objet `extensions` n’apparaîtra pas dans le fichier.

   Pour plus d’informations sur les paramètres de l’hôte, consultez le [Guide de référence des paramètres d’hôte disponibles - host.json](#reference-host-json).

Pour ajouter un paramètre d’hôte, effectuez les étapes suivantes :

1. Dans le fichier **host.json**, sous l’objet `extensionBundle`, ajoutez l’objet `extensions`, qui comprend les objets `workflow` et `settings`, par exemple :

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. Dans l’objet `settings`, ajoutez une liste plate avec les paramètres d’hôte que vous souhaitez utiliser pour tous les workflows de votre application logique, que ces workflows s’exécutent localement ou dans Azure, par exemple :

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

### <a name="azure-portal---hostjson"></a>Portail Azure - host.json

Pour passer en revue les paramètres d’hôte pour votre application logique basée sur un seul locataire dans le portail Azure, procédez comme suit :

1. Dans la zone de recherche du [portail Azure](https://portal.azure.com/), recherchez et ouvrez votre application logique.

1. Dans le menu de l’application logique, sélectionnez **Outils avancés** sous **Outils de développement**.

1. Dans la page **Outils avancés**, sélectionnez **Accéder**, ce qui ouvre l’environnement **Kudu** pour votre application logique.

1. Dans la barre d’outils Kudu, ouvrez le menu **Console de débogage**, puis sélectionnez **CMD**.

1. Dans le portail Azure, arrêtez votre application logique.

   1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

   1. Dans la barre d’outils du volet **Vue d’ensemble**, sélectionnez **Arrêter**.

1. Dans le menu de l’application logique, sélectionnez **Outils avancés** sous **Outils de développement**.

1. Dans le volet **Outils avancés**, sélectionnez **Accéder**, ce qui ouvre l’environnement Kudu pour votre application logique.

1. Dans la barre d’outils Kudu, ouvrez le menu **Console de débogage**, puis sélectionnez **CMD**.

   Une fenêtre de console s’ouvre pour vous permettre d’accéder au dossier **wwwroot** à l’aide de l’invite de commandes. Sinon, vous pouvez parcourir la structure des répertoires qui s’affiche au-dessus de la fenêtre de console.

1. Suivez le chemin menant au dossier **wwwroot** : `...\home\site\wwwroot`.

1. Au-dessus de la fenêtre de la console, dans la table des répertoires, en regard du fichier **host.json**, sélectionnez **Modifier**.

1. Une fois que le fichier **host.json** s’ouvre, passez en revue tous les paramètres de l’hôte qui ont été ajoutés précédemment pour votre application logique.

   Pour plus d’informations sur les paramètres de l’hôte, consultez le [Guide de référence des paramètres d’hôte disponibles - host.json](#reference-host-json).

Pour ajouter un paramètre, effectuez les étapes suivantes :

1. Avant d’ajouter ou de modifier des paramètres, arrêtez votre application logique dans le portail Azure.

   1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.
   1. Dans la barre d’outils du volet **Vue d’ensemble**, sélectionnez **Arrêter**.

1. Revenez au fichier **host.json**. Sous l’objet `extensionBundle`, ajoutez l’objet `extensions`, qui comprend les objets `workflow` et `settings`, par exemple :

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
            }
         }
      }
   }
   ```

1. Dans l’objet `settings`, ajoutez une liste plate avec les paramètres d’hôte que vous souhaitez utiliser pour tous les workflows de votre application logique, que ces workflows s’exécutent localement ou dans Azure, par exemple :

   ```json
   {
      "version": "2.0",
      "extensionBundle": {
         "id": "Microsoft.Azure.Functions.ExtensionBundle",
         "version": "[1.*, 2.0.0)"
      },
      "extensions": {
         "workflow": {
            "settings": {
               "Runtime.Trigger.MaximumWaitingRuns": "100"
            }
         }
      }
   }
   ```

1. Lorsque vous avez terminé, n’oubliez pas de sélectionner **Enregistrer**.

1. À présent, redémarrez votre application logique. Revenez à la page **Vue d’ensemble** de votre application logique, puis sélectionnez **Redémarrer**.

---

## <a name="next-steps"></a>Étapes suivantes

- [Créer des paramètres pour les valeurs qui changent dans les workflows entre les environnements pour Azure Logic Apps monolocataire](parameterize-workflow-app.md)
- [Configurer le déploiement DevOps pour Azure Logic Apps monolocataire](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
