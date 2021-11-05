---
title: Déclencheurs et actions intégrés
description: Utilisez des déclencheurs et des actions intégrés pour créer des workflows automatisés qui intègrent des applications, des données, des services et des systèmes, pour contrôler des workflows et pour gérer des données à l’aide d’Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 305f23d802324f5d974caaaf49afc072ff9d54f6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071018"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Déclencheurs et actions intégrés dans Azure Logic Apps

Les [déclencheurs et actions intégrés](apis-list.md) vous permettent de [contrôler la planification et la structure de votre workflow](#control-workflow), d’[exécuter votre propre code](#run-code-from-workflows), de [gérer ou manipuler des données](#manage-or-manipulate-data), et d’effectuer d’autres tâches dans vos workflows. Différents des [connecteurs managés](managed.md), de nombreuses opérations intégrées ne sont pas liées à un service, un système ou un protocole spécifique. Par exemple, vous pouvez démarrer presque n’importe quel workflow selon une planification à l’aide du déclencheur Récurrence. Vous pouvez aussi faire en sorte que votre workflow attende jusqu’à ce qu’il soit appelé à l’aide du déclencheur Requête. Toutes les opérations intégrées s’exécutent en mode natif dans Azure Logic Apps et la plupart ne nécessitent pas la création d’une connexion avant leur utilisation.

Azure Logic Apps fournit également des opérations intégrées pour un nombre plus restreint de services, systèmes et protocoles comme Azure App Services, Azure Functions et Gestion des API Azure et pour appeler d’autres workflows d’application logique Azure Logic Apps. Le nombre et la plage disponibles varient selon que vous créez une ressource d’application logique basée sur un plan de consommation qui s’exécute dans Azure Logic Apps mutualisé ou une ressource d’application logique basée sur un plan Standard qui s’exécute dans Azure Logic Apps avec un locataire unique. Pour plus d’informations, consultez [Architecture monolocataire ou multilocataire et environnement de service d’intégration (ISE)](../logic-apps/single-tenant-overview-compare.md). Dans la plupart des cas, la version intégrée offre de meilleures performances, fonctionnalités, conditions tarifaires, et ainsi de suite.

Par exemple, si vous créez une application logique monolocataire, les opérations intégrées et les [opérations de connecteurs managés](managed.md) sont disponibles pour quelques services, à savoir Azure Blob, Azure Event Hubs, Azure Cosmos DB, Azure Service Bus, DB2, MQ et SQL Server. Dans certains cas, certaines opérations intégrées ne sont disponibles que pour un type de ressource d’application logique. Par exemple, les opérations de traitement par lots sont actuellement disponibles uniquement pour la consommation de workflows d’application logique. Dans la plupart des cas, la version intégrée offre de meilleures performances, fonctionnalités, conditions tarifaires, et ainsi de suite.

La liste suivante décrit quelques-unes des tâches que vous pouvez effectuer avec des [déclencheurs et actions intégrés](#general-built-in-triggers-and-actions) :

- Exécuter des workflows selon des planifications personnalisées et avancées. Pour plus d’informations sur la planification, consultez la [section sur le comportement lié à la périodicité dans la vue d’ensemble des connecteurs pour Azure Logic Apps](apis-list.md#recurrence-behavior).

- Organiser et contrôler la structure de votre workflow, par exemple à l’aide de boucles et de conditions

- Utiliser des variables, des dates, des opérations de données, des transformations de contenu et des opérations par lots

- Communiquer avec d’autres points de terminaison à l’aide de déclencheurs et d’actions HTTP

- Recevoir des requêtes et y répondre

- Appeler vos propres fonctions (Azure Functions), applications web (Azure App Services), API (Gestion des API Azure), autres workflows Azure Logic Apps qui peuvent recevoir des requêtes, et ainsi de suite.

## <a name="general-built-in-triggers-and-actions"></a>Déclencheurs et actions intégrés généraux

Azure Logic Apps fournit les déclencheurs et actions intégrés suivants :

:::row:::
    :::column:::
        [![Icône - planification][schedule-icon]][schedule-doc]
        \
        \
        [**Planification**][schedule-doc]
        \
        \
        [**Récurrence**][schedule-recurrence-doc] : déclencher un workflow en fonction de la périodicité spécifiée.
        \
        \
        [**Fenêtre glissante**][schedule-sliding-window-doc] : déclencher un workflow qui doit gérer les données en blocs continus.
        \
        \
        [**Différer**][schedule-delay-doc] : suspendre votre workflow pendant la durée spécifiée.
        \
        \
        [**Différer jusqu’à**][schedule-delay-until-doc] : suspendre votre workflow jusqu’à la date et l’heure spécifiées.
    :::column-end:::
    :::column:::
        [![Icône - déclencheur et action HTTP][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Appeler un point de terminaison HTTP ou HTTPS à l’aide du déclencheur ou de l’action HTTP.
        \
        \
        Vous pouvez également utiliser ces autres déclencheurs et actions HTTP intégrés :
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Icône - déclencheur de requête][http-request-icon]][http-request-doc]
        \
        \
        [**Requête**][http-request-doc]
        \
        \
        [**Lors de la réception d’une demande HTTP**][http-request-doc] : attendre une requête d’un autre workflow, application ou service. Ce déclencheur permet à votre workflow d’être appelé sans avoir à être vérifié ou interrogé selon une planification.
        \
        \
        [**Réponse**][http-request-doc] : répondre à une requête reçue par le déclencheur **Lors de la réception d’une demande HTTP** dans le même workflow.
    :::column-end:::
    :::column:::
        [![Icône - traitement par lot][batch-icon]][batch-doc]<br>(*Application logique de consommation uniquement*)     \
        \
        [**Traitement par lot**][batch-doc]
        \
        \
        [**Traiter les messages par lots**][batch-doc] : déclencher un workflow qui traite les messages par lots.
        \
        \
        [**Envoyer les messages au lot**][batch-doc] : appeler un workflow existant qui démarre actuellement avec un déclencheur **Traiter les messages par lots**.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône STFP-SSH][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Connectez-vous aux serveurs SFTP auxquels vous avez accès à partir d’Internet via une connexion SSH afin de pouvoir utiliser vos fichiers et vos dossiers.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>Déclencheurs et actions intégrés basés sur les services

Azure Logic Apps fournit les actions intégrées suivantes pour les services indiqués :

:::row:::
    :::column:::
        [![Icône - Gestion des API Azure][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Gestion des API Azure**][azure-api-management-doc]
        \
        \
        Appelez vos propres déclencheurs et actions dans des API que vous définissez, gérez et publiez à l’aide de [Gestion des API Azure](../api-management/api-management-key-concepts.md). <p><p>**Remarque** : Ceci n’est pas pris en charge lors de l’utilisation du [niveau Consommation pour Gestion des API](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Icône - Azure App Services][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Appelez des applications que vous créez et hébergez sur [Azure App Services](../app-service/overview.md), par exemple API Apps et Web Apps.
        \
        \
        Lorsque Swagger est inclus, les déclencheurs et les actions définis par ces applications apparaissent comme les autres déclencheurs et actions de première classe dans Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Icône Azure Blob][azure-blob-storage-icon]][azure-app-services-doc]
        \
        \
        [**Objets blob Azure**][azure-blob-storage-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Connectez-vous à votre compte de stockage Azure afin de créer et de gérer du contenu d’objet blob.
    :::column-end:::
    :::column:::
        [![Icône Azure Cosmos DB][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB**][azure-cosmos-db-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Connectez-vous à Azure Cosmos DB afin de pouvoir accéder aux documents Azure Cosmos DB et les gérer.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône - Azure Functions][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Appelez des [fonctions hébergées par Azure](../azure-functions/functions-overview.md) pour exécuter vos propres *extraits de code* (C# ou Node.js) dans votre workflow.
    :::column-end:::
    :::column:::
        [![Icône - Azure Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Appelez d’autres workflows qui démarrent avec le déclencheur Requête nommé **Lors de la réception d’une demande HTTP**.
    :::column-end:::
    :::column:::
        [![Icône Azure Service Bus][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Gérez les messages asynchrones, les files d’attente, les sessions, les rubriques et les abonnements à une rubrique.
    :::column-end:::
    :::column:::
        [![Icône IBM DB2][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**DB2**][ibm-db2-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Se connecter à IBM DB2 dans le nuage ou sur site. Mettez à jour une ligne, obtenez une table et bien plus encore.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône Azure Event Hubs][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Event Hubs**][azure-event-hubs-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Consommez et publiez des événements via un Event Hub. Par exemple, obtenez une sortie à partir de votre application logique à l’aide des Event Hubs, puis envoyez-la à un fournisseur d’analyses en temps réel.
    :::column-end:::
    :::column:::
        [![Icône IBM MQ][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**MQ**][ibm-mq-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Connectez-vous à IBM MQ en local ou dans Azure pour envoyer et recevoir des messages.
    :::column-end:::
    :::column:::
        [![Icône SQL Server][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]<br>(*Application logique Standard uniquement*)     \
        \
        Connectez-vous à votre serveur SQL local ou à Azure SQL Database dans le cloud pour gérer les enregistrements, exécuter des procédures stockées ou exécuter des requêtes. <p>**Remarque** : Azure Logic Apps monolocataire fournit des opérations intégrées SQL et de connecteurs managés, tandis qu’Azure Logic Apps multilocataire fournit uniquement des opérations de connecteurs managés. <p>Pour plus d’informations, consultez [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md).
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Exécuter du code à partir de workflows

Azure Logic Apps fournit les actions intégrées suivantes vous permettant d’exécuter votre propre code dans votre workflow :

:::row:::
    :::column:::
        [![Icône - Azure Functions][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Appelez des [fonctions hébergées par Azure](../azure-functions/functions-overview.md) pour exécuter vos propres *extraits de code* (C# ou Node.js) dans votre workflow.
    :::column-end:::
    :::column:::
        [![Icône - action de code inclus][inline-code-icon]][inline-code-doc]
        \
        \
        [**Code inclus**][inline-code-doc]
        \
        \
        [**Exécuter du code JavaScript**][inline-code-doc] : ajoutez et exécutez vos propres *extraits de code* JavaScript inline dans votre workflow.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Contrôler le flux de travail

Azure Logic Apps fournit les actions intégrées suivantes qui permettent de structurer et de contrôler les actions de votre workflow :

:::row:::
    :::column:::
        [![Icône - action Condition][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        Évaluez une condition et exécutez différentes actions selon que la condition est true ou false.
    :::column-end:::
    :::column:::
        [![Icône - action For Each][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Effectuez les mêmes actions sur chaque élément dans un tableau.
    :::column-end:::
    :::column:::
        [![Icône - action Scope][scope-icon]][scope-doc]
        \
        \
        [**Nom**][scope-doc]
        \
        \
        Regroupez les actions en *étendues*, qui obtiennent leur propre état à la fin de l’exécution des actions dans l’étendue.
    :::column-end:::
    :::column:::
        [![Icône - action Switch][switch-icon]][switch-doc]
        \
        \
        [**Switch**][switch-doc]
        \
        \
        Regroupez les actions en *cas*, auxquels sont affectées des valeurs uniques à l’exception du cas par défaut. Exécutez uniquement le cas dont la valeur affectée correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécutez le cas par défaut.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône - action Terminate][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        Arrêtez un flux de travail d’application logique qui fonctionne activement.
    :::column-end:::
    :::column:::
        [![Icône - action Until][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        Répétez les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Gérer ou manipuler des données

Azure Logic Apps fournit les actions intégrées suivantes qui permettent de manipuler les sorties de données et leur format :

:::row:::
    :::column:::
        [![Icône - opérations de données][data-operations-icon]][data-operations-doc]
        \
        \
        [**Opérations de données**][data-operations-doc]
        \
        \
        Effectuez des opérations avec les données.
        \
        \
        **Composer** : créez une sortie unique à partir de plusieurs entrées avec différents types.
        \
        \
        **Créer un tableau CSV** : créez un tableau CSV (valeurs séparées par des virgules) à partir d’un tableau avec des objets JSON.
        \
        \
        **Créer un tableau HTML** : créez un tableau HTML à partir d’un tableau avec des objets JSON.
        \
        \
        **Filtrer un tableau** : créez un tableau à partir des éléments d’un autre tableau qui correspondent à vos critères.
        \
        \
        **Joindre** : créez une chaîne à partir de tous les éléments d’un tableau et séparez ces éléments à l’aide du séparateur spécifié.
        \
        \
        **Analyser JSON** : Créez des jetons conviviaux à partir des propriétés et de leurs valeurs dans le contenu JSON afin de pouvoir utiliser ces propriétés dans votre workflow.
        \
        \
        **Select** : créez un tableau avec des objets JSON en transformant les éléments ou les valeurs d’un autre tableau et en mappant ces éléments sur les propriétés spécifiées.
    :::column-end:::
    :::column:::
        ![Icône - action de date et heure][date-time-icon]
        \
        \
        **Date et heure**
        \
        \
        Effectuez des opérations avec des horodatages.
        \
        \
        **Ajouter à l’heure** : ajoutez le nombre spécifié d’unités à un timestamp.
        \
        \
        **Convertir le fuseau horaire** : Convertit un horodatage du fuseau horaire source au fuseau horaire cible.
        \
        \
        **Heure actuelle** : Renvoyer le timestamp actuel sous forme de chaîne.
        \
        \
        **Obtenir l’heure future** : Retourne l’horodatage actuel plus les unités de temps spécifiées.
        \
        \
        **Obtenir l’heure passée** : Retourne l’horodatage actuel moins les unités de temps spécifiées.
        \
        \
        **Soustraire de l’heure** : Soustrait un nombre d’unités de temps d’un horodatage.
    :::column-end:::
    :::column:::
        [![Icône - action Variables][variables-icon]][variables-doc]
        \
        \
        [**Variables**][variables-doc]
        \
        \
        Effectuez des opérations avec des variables.
        \
        \
        **Ajouter à une variable tableau** : insérez une valeur en tant que dernier élément dans un tableau stocké par une variable.
        \
        \
        **Ajouter à une variable chaîne** : insérez une valeur en tant que dernier caractère dans une chaîne stockée par une variable.
        \
        \
        **Décrémenter une variable** : diminuez une variable d’une valeur constante.
        \
        \
        **Incrémenter une variable** : augmentez une variable d’une valeur constante.
        \
        \
        **Initialiser la variable** : créez une variable et déclarez son type de données et sa valeur initiale.
        \
        \
        **Définir une variable** : attribuez une autre valeur à une variable existante.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-built-in-actions"></a>Actions intégrées de compte d’intégration

Azure Logic Apps fournit les actions intégrées suivantes, qui nécessitent un compte d’intégration si vous utilisez Azure Logic Apps multilocataire basée sur un plan Consommation, mais pas si vous utilisez Azure Logic Apps monolocataire basé sur un plan Standard :

> [!NOTE]
> Avant de pouvoir utiliser une action de compte d’intégration dans Azure Logic Apps multilocataire basé sur un plan Consommation, vous devez [lier votre ressource d’application logique à un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Toutefois, dans Azure Logic Apps monolocataire basé sur un plan Standard, certaines opérations de compte d’intégration ne nécessitent pas la liaison de votre ressource d’application logique à un compte d’intégration, par exemple, les opérations Liquid et XML. Pour utiliser ces actions, vous devez disposer de cartes Liquid, de cartes XML ou de schémas XML que vous pouvez charger par le biais des actions respectives dans le portail Azure ou ajouter au dossier **Artefacts** de votre projet Visual Studio Code à l’aide des dossiers **Cartes** et **Schémas**.

:::row:::
    :::column:::
        [![Icône - décodage de fichier plat][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Décodage de fichiers plats**][flat-file-decode-doc]
        \
        \
        Codez du contenu XML avant de l’envoyer à un partenaire commercial.
    :::column-end:::
    :::column:::
        [![Icône - codage de fichier plat][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Encodage de fichier plat**][flat-file-encode-doc]
        \
        \
        Décodez du contenu XML après l’avoir reçu d’un partenaire commercial.
    :::column-end:::
    :::column:::
        [![Icône - compte d’intégration][integration-account-icon]][integration-account-doc]
        \
        \
        [**Recherche d’artefact de compte d’intégration**<br>(*Multilocataire uniquement*)][integration-account-doc]
        \
        \
        Récupérez des métadonnées personnalisées pour les artefacts dans votre compte d’intégration : partenaires commerciaux, accords, schémas, etc.
    :::column-end:::
    :::column:::
        [![Icône - opérations Liquid][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Opérations Liquid**][json-liquid-transform-doc]
        \
        \
        Convertissez les formats suivants à l’aide de modèles Liquid : <p><p>- JSON en JSON <br>- JSON en texte <br>- XML en JSON <br>- XML en texte
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icône - transformation XML][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**Transformation XML**][xml-transform-doc]
        \
        \
        Convertissez le format XML source en un autre format XML.
    :::column-end:::
    :::column:::
        [![Icône - validation XML][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**Validation XML**][xml-validate-doc]
        \
        \
        Validez des documents XML par rapport au schéma spécifié.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des API personnalisées que vous pouvez appeler à partir d’Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Créer une instance du service Gestion des API Azure pour gérer et publier vos API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Permet d’intégrer des applications logiques à App Service API Apps"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gérer les fichiers de votre conteneur d’objets blob avec le connecteur Stockage Blob Azure"
[azure-cosmos-db-doc]: ./connectors-create-api-cosmos-db.md "Se connecter à Azure Cosmos DB afin de pouvoir accéder aux documents Azure Cosmos DB et les gérer"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Se connecter à Azure Event Hubs pour l’envoi et la réception d’événements entre vos applications logiques et Event Hubs"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Permet d’intégrer des applications logiques à Azure Functions"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Gérer les messages des files d’attente, rubriques et abonnements aux rubriques Service Bus"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Traiter les messages en groupes ou sous forme de lots"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Évaluer une condition et exécuter différentes actions selon que la condition est true ou false"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Effectuer des opérations avec les données telles que le filtrage de tableaux ou la création de tableaux CSV et HTML"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Effectuer les mêmes actions sur chaque élément dans un tableau"
[http-doc]: ./connectors-native-http.md "Appeler des points de terminaison HTTP ou HTTPS à partir de vos applications logiques"
[http-request-doc]: ./connectors-native-reqres.md "Recevoir des requêtes HTTP dans vos applications logiques"
[http-response-doc]: ./connectors-native-reqres.md "Répondre aux requêtes HTTP à partir de vos applications logiques"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Appeler des points de terminaison REST à partir de vos applications logiques"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendre des événements à partir de points de terminaison HTTP ou HTTPS"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Se connecter à IBM DB2 dans le nuage ou sur site. Mettre à jour une ligne, obtenir une table et bien plus encore"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Se connecter à IBM MQ en local ou dans Azure pour envoyer et recevoir des messages"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Ajouter et exécuter des extraits de code JavaScript à partir de vos applications logiques"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Intégrer des applications logiques à des flux de travail imbriqués"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Sélectionner et filtrer des tableaux avec l’action de requête"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Exécuter des applications logiques selon une planification"
[schedule-delay-doc]: ./connectors-native-delay.md "Retarder l’exécution de l’action suivante"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Retarder l’exécution de l’action suivante"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Exécuter des applications logiques selon une planification périodique"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Exécuter des applications logiques qui doivent gérer des données en blocs contigus"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organiser les actions en groupes, qui obtiennent leur propre état à la fin de l’exécution des actions dans le groupe"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Se connecter à votre compte SFTP via SSH. Télécharger, obtenir, supprimer des fichiers et bien plus encore"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Se connecter à Azure SQL Database ou SQL Server. Créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organiser les actions en cas, auxquels sont affectées des valeurs uniques. Exécuter uniquement le cas dont la valeur correspond au résultat d’une expression, d’un objet ou d’un jeton. Si aucune correspondance n’existe, exécuter le cas par défaut"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrêter ou annuler un flux de travail qu fonctionne activement pour votre application logique"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Répéter les actions jusqu’à ce que la condition spécifiée ait la valeur true ou qu’un état ait changé"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Effectuer des opérations avec des variables, comme initialiser, définir, incrémenter, décrémenter et ajouter à une variable de chaîne ou de tableau"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrir le fichier plat d’intégration d’entreprise"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrir le fichier plat d’intégration d’entreprise"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gérer les métadonnées des artefacts de compte d’intégration"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Transformer du code JSON avec des modèles Liquid"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Transformer des messages XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Valider des messages XML"
