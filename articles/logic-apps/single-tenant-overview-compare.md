---
title: Comparaison entre l’architecture monolocataire et l’architecture multilocataire d’Azure Logic Apps
description: Découvrez les différences entre une architecture monolocataire ou mutualisée et un service d’intégration (ISE) pour Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e7c77093ba3ddd2496d2e8ac8a78481d71fd1f6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088157"
---
# <a name="single-tenant-versus-multi-tenant-and-integration-service-environment-for-azure-logic-apps"></a>Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps

Azure Logic Apps est une plateforme cloud pour la création et l’exécution de *workflows d’application logique* automatisés qui intègrent vos applications, données, services et systèmes. Avec cette plateforme, vous pouvez rapidement développer des solutions d’intégration hautement scalables pour vos scénarios d’entreprise et B2B (Business to business). Pour créer une application logique, vous utilisez le type de ressource **Application logique (Consommation)** ou **Application logique (Standard)** . Le type de ressource Consommation s’exécute dans l’environnement *mutualisé* Azure Logic Apps ou de *service d’intégration*, tandis que le type de ressource standard s’exécute dans un environnement *monolocataire* Azure Logic Apps.

Avant de choisir le type de ressource à utiliser, consultez cet article pour découvrir comment les types de ressources et les environnements de service sont comparés les uns aux autres. Vous pouvez ensuite décider du type le plus approprié à utiliser, en fonction des besoins de votre scénario, des exigences de la solution et de l’environnement dans lequel vous souhaitez déployer, héberger et exécuter vos workflows.

Si vous débutez avec Azure Logic Apps, consultez la documentation suivante :

* [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md)
* [Qu’est-ce qu’un *workflow d’application logique* ?](logic-apps-overview.md#logic-app-concepts)

<a name="resource-environment-differences"></a>

## <a name="resource-types-and-environments"></a>Types de ressources et environnements

Pour créer des workflows d’application logique, vous choisissez le type de ressource d’**application logique** en fonction de votre scénario, les spécifications de la solution, les fonctionnalités souhaitées et l’environnement dans lequel vous voulez exécuter vos workflows.

Le tableau suivant résume brièvement les différences entre le type de ressource d’**application logique (Standard)** et le type de ressource d’**application logique (Consommation)** . Vous apprendrez également comment l’environnement *monolocataire* est comparé aux environnements *mutualisés* et de *service d’intégration (ISE)* pour le déploiement, l’hébergement et l’exécution de vos workflows d’application logique.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="resource-type-introduction"></a>

## <a name="logic-app-standard-resource"></a>Ressource Application logique (Standard)

Le type de ressource **Application logique (Standard)** repose sur le runtime Azure Logic Apps monolocataire remanié. Ce runtime utilise le [modèle d’extensibilité d’Azure Functions](../azure-functions/functions-bindings-register.md) et est hébergé en tant qu’extension sur le runtime d’Azure Functions. Cette conception offre une portabilité, une flexibilité et des performances accrues pour vos workflows d’application logique, ainsi que d’autres fonctionnalités et avantages hérités de la plateforme Azure Functions et de l’écosystème Azure App Service. Par exemple, vous pouvez créer, déployer et exécuter des applications logiques monolocataires et leurs flux de travail dans [Azure App Service Environment v3](../app-service/environment/overview.md).

Le type de ressource Standard introduit une structure de ressources qui peut héberger plusieurs workflows, de la même manière qu’une application de fonction Azure peut héberger plusieurs fonctions. Avec un mappage de 1 à plusieurs, les workflows dans la même application logique et le même client partagent les ressources de calcul et de traitement, ce qui améliore les performances en raison de leur proximité. Cette structure diffère de la ressource **Application logique (Consommation)** où vous avez un mappage 1 à 1 entre une ressource d’application logique et un workflow.

Pour en savoir plus sur la portabilité, la flexibilité et les améliorations des performances, consultez les sections suivantes. Ou, pour plus d’informations sur le runtime Azure Logic Apps monolocataire et l’extensibilité d’Azure Functions, consultez la documentation suivante :

* [Azure Logic Apps s’exécutant partout – Présentation approfondie du runtime](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)
* [Présentation d’Azure Functions](../azure-functions/functions-overview.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](../azure-functions/functions-triggers-bindings.md)

<a name="portability"></a>
<a name="flexibility"></a>

### <a name="portability-and-flexibility"></a>Portabilité et flexibilité

Lorsque vous créez des applications logiques à l’aide du type de ressource **Application logique (Standard)** , vous pouvez déployer et exécuter vos flux de travail dans d’autres environnements, tels qu’[Azure App Service Environment v3](../app-service/environment/overview.md). Si vous utilisez Visual Studio Code avec l’extension **Azure Logic Apps (Standard)** , vous pouvez développer, générer et exécuter *localement* des flux de travail dans votre environnement de développement sans avoir à effectuer de déploiement sur Azure. Si votre scénario requiert des conteneurs, [créez des applications logiques monolocataires utilisant des Logic Apps Azure Arc ](azure-arc-enabled-logic-apps-create-deploy-workflows.md). Pour plus d’informations, consultez [Qu’est-ce que Logic Apps avec Azure Arc ?](azure-arc-enabled-logic-apps-overview.md)

Ces fonctionnalités fournissent des améliorations majeures et des avantages substantiels par rapport au modèle mutualisé, ce qui vous oblige à développer sur une ressource en cours d’exécution existante dans Azure. En outre, le modèle multi-locataire pour automatiser le déploiement des ressources **Application logique (Consommation)** est entièrement basé sur des modèles Azure Resource Manager (modèles ARM), qui combinent et gèrent l’approvisionnement des ressources pour les applications et l’infrastructure.

Avec le type de ressource **Application logique (Standard)** , le déploiement devient plus facile, car vous pouvez séparer le déploiement d’applications du déploiement de l’infrastructure. Vous pouvez empaqueter le runtime et les workflows d’une instance Azure Logic Apps monolocataire dans le cadre de votre application logique. Vous pouvez utiliser des étapes ou des tâches génériques qui génèrent, assemblent et compressent vos ressources d’application logique dans des artefacts prêts à être déployés. Pour déployer votre infrastructure, vous pouvez toujours utiliser des modèles ARM pour approvisionner séparément ces ressources avec d’autres processus et pipelines que vous utilisez à ces fins.

Pour déployer votre application, copiez les artefacts dans l’environnement hôte, puis démarrez vos applications pour exécuter vos workflows. Ou bien, intégrez vos artefacts dans des pipelines de déploiement à l’aide des outils et processus que vous connaissez et utilisez déjà. De cette façon, vous pouvez déployer à l’aide des outils de votre choix, quelle que soit la pile de technologies que vous utilisez pour le développement.

Avec les options de génération et de déploiement standard, vous pouvez vous concentrer sur le développement d’applications séparément du déploiement d’infrastructure. Ainsi, vous obtenez un modèle de projet plus générique dans lequel vous pouvez appliquer de nombreuses options de déploiement similaires ou identiques à celles que vous utilisez pour une application générique. Vous bénéficierez également d’une expérience plus cohérente pour créer des pipelines de déploiement autour de vos projets d’application et pour exécuter les tests et validations requis avant la publication en production.

<a name="performance"></a>

### <a name="performance"></a>Performances

Avec le type de ressource **Application logique (Standard)** , vous pouvez créer et exécuter plusieurs workflows dans la même application logique et le même locataire. Avec ce mappage de type 1-à-plusieurs, ces workflows partagent des ressources, telles que le calcul, le traitement, le stockage et le réseau, offrant de meilleures performances en raison de leur proximité.

Le type de ressource **Application logique (Standard)** et le runtime Azure Logic Apps monolocataire offrent une autre amélioration significative en rendant les connecteurs managés plus populaires disponibles en tant qu’opérations intégrées. Par exemple, vous pouvez utiliser des opérations intégrées pour Azure Service Bus, Azure Event Hubs, SQL et autres. Dans le même temps, les versions du connecteur managé sont toujours disponibles et continuent de fonctionner.

Lorsque vous utilisez les nouvelles opérations intégrées, vous créez des connexions appelées *connexions intégrées* ou *connexions de fournisseur de services*. Leurs équivalents en connexion managée sont appelés *connexions d’API*. Elles sont créées et exécutées séparément en tant que ressources Azure que vous devez également déployer à l’aide de modèles ARM. Les opérations intégrées et leurs connexions s’exécutent localement dans le même processus que celui qui exécute vos workflows. Les deux sont hébergées sur le runtime Azure Logic Apps monolocataire. Par conséquent, les opérations intégrées et leurs connexions offrent de meilleures performances en raison de la proximité de vos workflows. Cette conception fonctionne également bien avec les pipelines de déploiement, car les connexions du fournisseur de services sont empaquetées dans le même artefact de build.

<a name="data-residency"></a>

### <a name="data-residency"></a>Résidence des données

Les ressources d’application logique créées avec le type de ressource **Application logique (Standard)** sont hébergées dans une instance Azure Logic Apps monolocataire, qui [ne stocke, ne traite ni ne réplique les données en dehors de la région où vous déployez ces ressources d’application logique](https://azure.microsoft.com/global-infrastructure/data-residency), ce qui signifie que les données de vos flux de travail d’applications logiques restent dans la région où vous créez et déployez leurs ressources parentes.

## <a name="create-build-and-deploy-options"></a>Options de création, de génération et de déploiement

Pour créer une application logique basée sur l’environnement souhaité, vous avez plusieurs options, par exemple :

**Environnement monolocataire**

| Option | Outils et ressources | Informations complémentaires |
|--------|---------------------|------------------|
| Portail Azure | Type de ressource **Application logique (Standard)** | [Créer des workflows d’intégration pour un client Logic Apps monolocataire - Portail Azure](create-single-tenant-workflows-azure-portal.md) |
| Visual Studio Code | [Extension **Azure Logic Apps (Standard)**](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps) | [Créer des workflows d’intégration pour un client Logic Apps monolocataire - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) |
| Azure CLI | Extension Azure CLI Logic Apps | Pas encore disponible |
||||

**Environnement multilocataire**

| Option | Outils et ressources | Informations complémentaires |
|--------|---------------------|------------------|
| Portail Azure | Type de ressource **Application logique (Consommation)** | [Démarrage rapide : Créer des workflows d’intégration dans une instance mutalisée Azure Logic Apps - Portail Azure](quickstart-create-first-logic-app-workflow.md) |
| Visual Studio Code | [Extension **Azure Logic Apps (Consommation)**](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) | [Démarrage rapide : Créer des workflows d’intégration dans une instance mutalisée Azure Logic Apps - Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
| Azure CLI | [Extension **Azure CLI Logic Apps**](https://github.com/Azure/azure-cli-extensions/tree/master/src/logic) | - [Démarrage rapide : Créer et gérer des workflows d’intégration dans une instance mutalisée Azure Logic Apps - Azure CLI](quickstart-logic-apps-azure-cli.md) <p><p>- [az logic](/cli/azure/logic) |
| Azure Resource Manager | [**Créer une application logique** Modèle ARM](https://azure.microsoft.com/resources/templates/logic-app-create/) | [Démarrage rapide : Créer et déployer des workflows d’intégration dans une instance mutalisée Azure Logic Apps - Modèle ARM](quickstart-create-deploy-azure-resource-manager-template.md) |
| Azure PowerShell | [Az.LogicApp module](/powershell/module/az.logicapp) | [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps) |
| API REST Azure | [API REST Azure Logic Apps](/rest/api/logic) | [Référence Bien démarrer avec l’API REST Azure](/rest/api/azure) |
||||

**Environnement de service d’intégration (ISE)**

| Option | Outils et ressources | Informations complémentaires |
|--------|---------------------|------------------|
| Portail Azure | Type de ressource **Application logique (Consommation)** avec une ressource ISE existante | Identique au [Guide de démarrage rapide : Créer des workflows d’intégration dans Azure Logic Apps mutualisé - Portail Azure](quickstart-create-first-logic-app-workflow.md), mais vous sélectionnez une région ISE au lieu d’une région mutualisée. |
||||

Bien que vos expériences de développement diffèrent selon que vous créez des ressources d’application logique **Consommation** ou **Standard**, vous pouvez rechercher et accéder à toutes vos applications logiques déployées dans votre abonnement Azure.

Par exemple, dans le portail Azure, la page **Applications logiques** affiche à la fois les types de ressources **Consommation** et **Standard**. Dans Visual Studio Code, les applications logiques déployées s’affichent sous votre abonnement Azure, mais elles sont regroupées par l’extension que vous avez utilisée, à savoir **Azure : Application logique (Consommation)** et **Azure : Application logique (Standard)** .

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Workflows avec état et sans état

Avec le type de ressource **Application logique (Standard)** , vous pouvez créer ces types de workflows dans la même application logique :

* *Avec état*

  Créez un workflow avec état lorsque vous devez conserver, examiner ou référencer des données d’événements précédents. Ces workflows enregistrent et transfèrent toutes les entrées et les sorties de chaque action, ainsi que leurs états, dans un stockage externe qui permet d’examiner les détails et l’historique d’exécution à l’issue de chaque exécution. Les workflows avec état offrent une haute résilience en cas d’interruption. Une fois les services et systèmes restaurés, vous pouvez reconstituer les exécutions interrompues à partir de l’état enregistré et réexécuter les workflows jusqu’à leur terme. Les workflows avec état peuvent continuer à s’exécuter plus longtemps que les workflows sans état.

  Par défaut, les flux de travail avec état dans les Apps Logic Azure à plusieurs locataires et à locataire unique s’exécutent de façon asynchrone. Toutes les actions basées sur HTTP suivent le [modèle d’opération asynchrone](/azure/architecture/patterns/async-request-reply)standard. Ce modèle spécifie qu’après l’appel d’une action HTTP ou l’envoi d’une requête à un point de terminaison, un service, un système ou une API, le récepteur retourne immédiatement la réponse [« 202 ACCEPTED »](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3). Ce code confirme que le récepteur a accepté la requête, mais indique qu’il n’a pas terminé le traitement. La réponse peut inclure un `location` en-tête qui spécifie l’URI et un ID d’actualisation que l’appelant peut utiliser pour interroger ou vérifier l’état de la demande asynchrone jusqu’à ce que le récepteur arrête le traitement et retourne une réponse de réussite [« 200 OK »](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou une autre réponse non-202. Toutefois, l’appelant n’a pas besoin d’attendre la fin du traitement de la requête et peut exécuter l’action suivante. Pour plus d’informations, consultez [L’intégration asynchrone des microservices permet l’autonomie des microservices](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* *Sans état*

  Créez un workflow sans état lorsque vous n’avez pas besoin de conserver, d’examiner ou de référencer des données d’événements précédents dans un stockage externe après chaque exécution pour les consulter ultérieurement. Ces workflows enregistrent toutes les entrées et les sorties pour chaque action et leurs états *uniquement en mémoire*, et non dans un stockage externe. Par conséquent, les workflows sans état offrent des temps d’exécution plus courts généralement inférieurs à cinq minutes, des performances plus rapides avec des temps de réponse plus courts, un débit plus élevé, et des coûts d’exécution réduits, car les détails et l’historique d’exécution ne sont pas enregistrés dans un stockage externe. Toutefois, en cas de panne, les exécutions interrompues ne sont pas automatiquement restaurées, de sorte que l’appelant doit relancer manuellement les exécutions interrompues.

  > [!IMPORTANT]
  > Un workflow sans état offre les meilleures performances lors de la gestion des données ou du contenu, tel qu’un fichier, qui ne dépasse pas 64 Ko au *total*. Des tailles de contenu plus volumineuses, telles que plusieurs pièces jointes imposantes, peuvent ralentir considérablement les performances de votre workflow ou même provoquer le blocage de votre workflow en raison d’exceptions de mémoire insuffisante. Si votre workflow peut être amené à gérer des tailles de contenu supérieures, utilisez un workflow avec état à la place.

  Les flux de travail sans état s’exécutent uniquement de façon synchrone, donc ils n’utilisent pas le [modèle d’opération asynchrone](/azure/architecture/patterns/async-request-reply) standard utilisé par les flux de travail avec état. Au lieu de cela, toutes les actions basées sur HTTP qui renvoient une réponse [« 202 ACCEPTÉ »](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) passent à l’étape suivante de l’exécution du flux de travail. Si la réponse comprend un `location` en-tête, un flux de travail sans état n’interrogera pas l’URI spécifié pour vérifier l’état. Pour suivre le modèle d’opération asynchrone standard, utilisez un flux de travail avec état à la place.

  Pour faciliter le débogage, vous pouvez activer l’historique des exécutions pour un workflow sans état (ce qui a un certain impact sur les performances), puis le désactiver lorsque vous avez terminé. Pour plus d’informations, consultez [Créer des flux de travail basés sur un locataire dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless) ou [Créer des flux de travail basés sur un locataire dans le portail Azure](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Actuellement, les flux de travail sans état prennent en charge uniquement les *actions* pour les [connecteurs gérés](../connectors/managed.md) déployés dans Azure, pas pour des déclencheurs. Pour démarrer votre workflow, sélectionnez le [déclencheur intégré Requête, Event Hubs ou Service Bus](../connectors/built-in.md). Ces déclencheurs s’exécutent en mode natif dans le runtime d’Azure Logic Apps. Pour plus d’informations sur les déclencheurs, actions et connecteurs limités, non disponibles ou non pris en charge, consultez [Capacités modifiées, limitées, non disponibles ou non prises en charge](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Différences de comportement imbriqué entre les workflows avec état et sans état

Vous pouvez [faire en sorte qu’un workflow puisse être appelé](logic-apps-http-endpoint.md) à partir d’autres workflows qui existent dans la même ressource **Application logique (Standard)** à l’aide du [déclencheur Requête](../connectors/connectors-native-reqres.md), du [déclencheur Webhook HTTP](../connectors/connectors-native-webhook.md) ou de déclencheurs de connecteur géré de [type ApiConnectionWebhook](logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) pouvant recevoir des requêtes HTTPS.

Voici les modèles de comportement que les workflows imbriqués peuvent suivre après qu’un workflow parent a appelé un workflow enfant :

* Modèle d’interrogation asynchrone

  Le flux de travail parent n’attend pas de réponse à son appel initial, mais vérifie continuellement l’historique des exécutions du flux de travail enfant jusqu’à la fin de l’exécution de celui-ci. Par défaut, les flux de travail avec état suivent ce modèle, qui est idéal pour les flux de travail enfants de longue durée susceptibles de dépasser les [limites de délai d’expiration de demande](logic-apps-limits-and-config.md).

* Modèle synchrone (« fire and forget »)

  Le flux de travail enfant accuse réception de l’appel en retournant immédiatement une réponse `202 ACCEPTED`, et le flux de travail parent continue jusqu’à l’action suivante sans attendre les résultats du flux de travail enfant. Au lieu de cela, le flux de travail parent reçoit les résultats lorsque le flux de travail enfant termine son exécution. Les flux de travail enfants avec état qui n’incluent pas d’action Réponse suivent toujours le modèle synchrone. Pour les flux de travail enfants avec état, vous pouvez consulter l’historique des exécutions.

  Pour activer ce comportement, dans la définition JSON du flux de travail, définissez la propriété `operationOptions` sur la valeur `DisableAsyncPattern`. Pour plus d’informations, consultez [Types d’action et de déclencheur – Options d’opération](logic-apps-workflow-actions-triggers.md#operation-options).

* Déclencher et attendre

  Pour un flux de travail enfant sans état, le flux de travail parent attend une réponse retournant les résultats du flux de travail enfant. Ce modèle fonctionne de manière similaire à l’utilisation [du déclencheur ou de l’action HTTP](../connectors/connectors-native-http.md) intégrés pour appeler un flux de travail enfant. Les flux de travail enfants sans état qui n’incluent pas d’action Réponse retournent immédiatement une réponse `202 ACCEPTED`, mais le flux de travail parent attend que le flux de travail enfant se termine avant de passer à l’action suivante. Ces comportements s’appliquent uniquement aux flux de travail enfants sans état.

Ce tableau spécifie le comportement du flux de travail enfant selon que les flux de travail parent et enfant sont avec état, sans état ou mixtes :

| Flux de travail parent | Flux de travail enfant | Comportement du flux de travail enfant |
|-----------------|----------------|----------------|
| Avec état | Avec état | Asynchrone ou synchrone avec le paramètre `"operationOptions": "DisableAsyncPattern"` |
| Avec état | Sans état | Déclencher et attendre |
| Sans état | Avec état | Synchrone |
| Sans état | Sans état | Déclencher et attendre |
||||

<a name="other-capabilities"></a>

## <a name="other-single-tenant-model-capabilities"></a>Autres fonctionnalités du modèle monolocataire

Le modèle monolocataire et le type de ressource **Application logique (Standard)** incluent de nombreuses fonctionnalités actuelles et nouvelles, par exemple :

* Créez des applications logiques et leurs workflows à partir de [plus de 400 connecteurs managés](/connectors/connector-reference/connector-reference-logicapps-connectors) pour les applications et services SaaS (Software-as-a-service) et PaaS (Platform-as-a-service), ainsi que des connecteurs pour les systèmes locaux.

  * D’autres connecteurs gérés sont désormais disponibles en tant qu’opérations intégrées et s’exécutent de la même façon que d’autres opérations intégrées, comme Azure Functions. Les opérations intégrées s’exécutent en mode natif sur le runtime monolocataire Azure Logic Apps. Par exemple, les nouvelles opérations intégrées incluent le Service Bus Azure, les Hubs Azure Event, le Serveur SQL et MQ, DB2 et le Fichier hôte IBM .

    > [!NOTE]
    > Pour la version SQL Server intégrée, seule l’action **Exécuter une requête** peut se connecter directement aux réseaux virtuels Azure sans utiliser la [passerelle de données locale](logic-apps-gateway-connection.md).

  * Vous pouvez créer vos propres connecteurs intégrés pour tous les services dont vous avez besoin à l’aide du [framework d’extensibilité Azure Logic Apps monolocataire](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). À l’instar des opérations intégrées telles qu’Azure Service Bus et SQL Server, mais contrairement aux [connecteurs managés personnalisés](../connectors/apis-list.md#custom-apis-and-connectors), qui ne sont actuellement pas pris en charge, les connecteurs intégrés personnalisés offrent un débit plus élevé, une faible latence et une connectivité locale, car ils s’exécutent dans le même processus que le runtime monolocataire.

    La fonctionnalité de création n’est actuellement disponible que dans Visual Studio Code, mais elle n’est pas activée par défaut. Pour créer ces connecteurs, [changez votre projet basé sur bundle d’extensions (Node.js) en projet basé sur package NuGet (.NET)](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Pour plus d’informations, consultez [Azure Logic Apps s’exécutant partout – Extensibilité de connecteur intégré](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Vous pouvez utiliser les actions suivantes pour Liquid Operations et XML Operations sans compte d’intégration. Ces opérations peuvent être notamment les actions suivantes :

    * XML : **Transformation XML** et **Validation XML**

    * Liquid : **Transformer JSON en JSON**, **Transformer JSON en TEXT**, **Transformer XML en JSON** et **Transformer XML en Text**

    > [!NOTE]
    > Pour utiliser ces actions dans des Azure Logic Apps monolocataires (Standard), vous devez disposer de mappages Liquid, de mappages XML ou de schémas XML. Vous pouvez charger ces artefacts sur le portail Azure à partir du menu des ressources de votre application logique, sous **Artefacts**, qui inclut les sections **Schémas** et **Mappages**. Vous pouvez également ajouter ces artefacts à votre dossier **Artefacts** du projet Visual Studio Code, en utilisant les **Mappages** et les **Schémas** correspondants. Vous pouvez ensuite utiliser ces artefacts sur plusieurs workflows au sein de la *même ressource d’application logique*.

  * Les ressources **Application logique (Standard)** peuvent s’exécuter en tout lieu, car les Apps Logic Azure génèrent des chaînes de connexion avec signature d’accès partagé (SAP) que ces applications logiques peuvent utiliser pour envoyer des demandes au point de terminaison du runtime de connexion cloud. Le service Logic Apps enregistre ces chaînes de connexion avec d’autres paramètres de l’application pour vous permettre de stocker facilement ces valeurs dans Azure Key Vault quand vous opérez un déploiement sur Azure.

    > [!NOTE]
    > Par défaut, l’[identité managée assignée par le système](create-managed-service-identity.md) d’une ressource **Application logique (Standard)** est automatiquement activée pour authentifier les connexions au moment de l’exécution. Cette identité diffère des informations d’identification d’authentification ou de la chaîne de connexion que vous utilisez lors de la création d’une connexion. Si vous désactivez cette identité, les connexions ne fonctionneront pas au moment de l’exécution. Pour afficher ce paramètre, dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Identité**.
    >
    > L’identité managée affectée par l’utilisateur n’est pas disponible actuellement sur le type de ressource **Application logique (standard)** .

* Vous pouvez exécuter, tester et déboguer localement vos applications logiques et leurs workflows dans l’environnement de développement Visual Studio Code.

  Avant d’exécuter et de tester votre application logique, vous pouvez faciliter le débogage en ajoutant et en utilisant des points d’arrêt dans le fichier **workflow.json** d’un workflow. Toutefois, les points d’arrêt sont pris en charge uniquement pour les actions à l’heure actuelle, et non pour les déclencheurs. Pour plus d’informations, consultez [Créer des workflows monolocataires dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints).

* Publiez ou déployez directement des applications logiques et leurs workflows de Visual Studio Code vers divers environnements d’hébergement tels qu’Azure et des Logic Apps Azure Arc.

* Activez les capacités de journalisation et de suivi des diagnostics pour votre application logique en utilisant [Application Insights](../azure-monitor/app/app-insights-overview.md) lorsque la fonctionnalité est prise en charge par votre abonnement Azure et les paramètres de l’application logique.

* Accédez à des fonctionnalités réseau, comme la connexion et l’intégration privées aux réseaux virtuels Azure, de la même façon qu’Azure Functions quand vous créez et déployez vos applications logiques, en utilisant le [plan Azure Functions Premium](../azure-functions/functions-premium-plan.md). Pour plus d’informations, consultez la documentation suivante :

  * [Options de mise en réseau d’Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps s’exécutant partout – Capacités réseau avec Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Régénérez les clés d’accès pour les connexions gérées utilisées par les workflows individuels dans une ressource **Application logique (Standard)** . Pour cette tâche, [suivez les mêmes étapes que pour la ressource **Logic Apps (Consommation)** , mais au niveau du workflow individuel](logic-apps-securing-a-logic-app.md#regenerate-access-keys), et non au niveau de la ressource de l’application logique.

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Capacités modifiées, limitées, non disponibles ou non prises en charge

Pour la ressource **Application logique (Standard)** , ces capacités ont changé, ou elles sont actuellement limitées, non disponibles ou non prises en charge :

* **Déclencheurs et actions** : Les déclencheurs et actions intégrés s’exécutent en mode natif dans le runtime des Apps Logic Azure, tandis que les connecteurs managés sont hébergés et exécutés dans Azure. Certains déclencheurs et actions intégrés ne sont pas disponibles, tels que la fenêtre glissante, le traitement par lots, les services Azure App et la gestion des API Azure. Pour démarrer un workflow avec ou sans état, utilisez le [déclencheur intégré Périodicité, Requête, HTTP, Webhook HTTP, Event Hubs ou Service Bus](../connectors/apis-list.md). Dans le concepteur, les déclencheurs et actions intégrés apparaissent sous l’onglet **Intégré**.

  Pour les workflows *avec état*, les [déclencheurs et les actions des connecteurs managés](../connectors/managed.md) s’affichent sous l’onglet **Azure**, à l’exception des opérations non disponibles répertoriées ci-dessous. Pour les workflows *sans état*, l’onglet **Azure** n’apparaît pas lorsque vous souhaitez sélectionner un déclencheur. Vous pouvez sélectionner uniquement des [*actions* de connecteur managé, et non des déclencheurs](../connectors/managed.md). Bien que vous puissiez activer les connecteurs gérés hébergés par Azure pour les workflows sans état, le concepteur n’affiche aucun déclencheur de connecteur géré que vous pouvez ajouter.

  > [!NOTE]
  > Pour s’exécuter localement dans Visual Studio Code, les déclencheurs et les actions basés sur un webhook nécessitent une configuration supplémentaire. Pour plus d’informations, consultez [Créer des workflows monolocataires dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#webhook-setup).

  * Ces déclencheurs et actions ont été modifiés ou sont actuellement limités, non pris en charge ou non disponibles :

    * Les [*déclencheurs* de la passerelle de données locale](../connectors/managed.md#on-premises-connectors) ne sont pas disponibles, mais les actions de la passerelle *le sont*.

    * L’action intégrée, [Azure Functions – Choisir une fonction Azure](logic-apps-azure-functions.md) est désormais **Azure Function Operations – Appeler une fonction Azure**. Cette action ne fonctionne actuellement que pour les fonctions créées à partir du modèle **Déclencheur HTTP**.

      Dans le portail Azure, vous pouvez sélectionner une fonction de déclencheur HTTP à laquelle vous pouvez accéder en créant une connexion via l’expérience utilisateur. Si vous inspectez la définition JSON de l’action de fonction en mode code ou le fichier **workflow.json** avec Visual Studio Code, l’action fait référence à la fonction à l’aide d’une référence `connectionName`. Cette version extrait les informations de la fonction en tant que connexion, que vous pouvez trouver dans le fichier **connections.json** de votre projet d’application logique, qui est disponible après la création d’une connexion dans Visual Studio Code.

      > [!NOTE]
      > Dans le modèle monolocataire, l’action de la fonction ne prend en charge que l’authentification par chaîne de requête. Azure Logic Apps obtient la clé par défaut de la fonction lors de l’établissement de la connexion, stocke cette clé dans les paramètres de votre application et utilise la clé pour l’authentification lors de l’appel de la fonction.
      >
      > Comme dans le modèle multilocataire, si vous renouvelez cette clé, par exemple par le biais de l’expérience Azure Functions dans le portail, l’action de la fonction ne marchera plus en raison de la clé devenue non valide. Pour résoudre ce problème, vous devez recréer la connexion à la fonction que vous souhaitez appeler ou mettre à jour les paramètres de votre application avec la nouvelle clé.

    * L’action intégrée [Code inline](logic-apps-add-run-inline-code.md) intégrée est renommée **Opérations de code inline**, n’a plus besoin d’un compte d’intégration et a des [limites mises à jour](logic-apps-limits-and-config.md).

    * L’action intégrée, [Azure Logic Apps – Choisir un workflow d’application logique](logic-apps-http-endpoint.md) est désormais **Workflow Operations – Appeler un workflow dans cette application de workflow**.

    * Certains [déclencheurs et actions pour les comptes d’intégration](../connectors/managed.md#integration-account-connectors) ne sont pas disponibles, par exemple, les actions AS2 (v2) et les actions RosettaNet.

    * Les [connecteurs gérés personnalisés](../connectors/apis-list.md#custom-apis-and-connectors) ne sont actuellement pas pris en charge. Toutefois, vous pouvez créer des *opérations intégrées personnalisées* lorsque vous utilisez Visual Studio Code. Pour plus d’informations, consultez [Créer des workflows monolocataires avec Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring).

* **Authentification**: les types d’authentification suivants ne sont actuellement pas disponibles pour le type de ressource **Application logique (standard)** :

  * Authentification ouverte du Répertoire actif Azure (Azure AD OAuth) pour les appels entrants vers les déclencheurs basés sur une demande, tels que le déclencheur de Requête et le déclencheur Webhook HTTP.

  * Identité managée affectée par l’utilisateur. À l’heure actuelle, seule l’identité managée affectée par le système est disponible et automatiquement activée.

* **Transformation XML**: Prise en charge du référencement des ensembles à partir de mappages n’est pas disponible actuellement. Par ailleurs, seul XSLT 1.0 est actuellement pris en charge.

* **Débogage des points d’arrêt dans Visual Studio Code** : Bien que vous puissiez ajouter et utiliser des points d’arrêt à l’intérieur du fichier **workflow.json** pour un workflow, les points d’arrêt sont pris en charge uniquement pour les actions pour le moment, et non pour les déclencheurs. Pour plus d’informations, consultez [Créer des workflows monolocataires dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints).

* **Historique des déclencheurs et historique des exécutions** : Pour le type de ressource **Application logique (Standard)** , l’historique des déclencheurs et l’historique des exécutions s’affichent dans le portail Azure au niveau du workflow, et non au niveau de l’application logique. Pour plus d’informations, consultez [Créer des workflows monolocataires avec le portail Azure](create-single-tenant-workflows-azure-portal.md).

* **Contrôle de zoom** : Le contrôle de zoom n’est pas disponible actuellement sur le concepteur.

* **Cibles de déploiement** : Vous ne pouvez pas déployer le type de ressource **Application logique (Standard)** dans un [environnement de service d’intégration (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) ni dans les emplacements de déploiement Azure.

* **Gestion des API Azure** : Vous ne pouvez actuellement pas importer le type de ressource **Application logique (Standard)** dans Gestion des API Azure. Toutefois, vous pouvez importer le type de ressource **Application logique (Consommation)** .

<a name="firewall-permissions"></a>

## <a name="strict-network-and-firewall-traffic-permissions"></a>Autorisations strictes de trafic réseau et de pare-feu

Si votre environnement a des exigences réseau strictes ou des pare-feu qui limitent le trafic, vous devez autoriser l’accès pour toutes les connexions de déclencheur ou d’action dans vos workflows d’application logique. Pour trouver les noms de domaine complets (FQDN) de ces connexions, consultez les sections correspondantes dans les rubriques suivantes :

* [Autorisations de pare-feu pour les applications logiques monolocataires - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)
* [Autorisations de pare-feu pour les applications logiques monolocataires - Portail Azure](create-single-tenant-workflows-azure-portal.md#firewall-setup)

## <a name="next-steps"></a>Étapes suivantes

* [Créer des workflows monolocataires dans le portail Azure](create-single-tenant-workflows-azure-portal.md)
* [Créer des workflows monolocataires dans Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md)

Nous aimerions également que vous nous fassiez part de votre expérience avec $$$Azure Logic Apps monolocataire !

* Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
* Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/lafeedback).
