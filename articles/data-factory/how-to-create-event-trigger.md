---
title: Créer un déclencheur basé sur des événements
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment créer un déclencheur dans Azure Data Factory ou Azure Synapse Analytics qui exécute un pipeline en réponse à un événement.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: da1348f0e31fbe0c3e2528692d40675d799e43da
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469689"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Créer un déclencheur qui exécute un pipeline en réponse à un événement de stockage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit les déclencheurs d’événement de stockage que vous pouvez créer dans vos pipelines Data Factory ou Synapse.

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d’intégration de données requièrent souvent que les clients déclenchent des pipelines basés sur des événements se produisant dans un compte de stockage, tels que l’arrivée ou la suppression d’un fichier dans votre compte Stockage Blob Azure. Les pipelines Data Factory et Synapse s’intègrent en mode natif avec [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), ce qui vous permet de déclencher des pipelines sur de tels événements.

Pour afficher une présentation de dix minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> L’intégration décrite dans cet article dépend [d’Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Vérifiez que votre abonnement est inscrit auprès du fournisseur de ressources Event Grid. Pour plus d’informations, consultez [Types et fournisseurs de ressources](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Vous devez pouvoir effectuer l’action *Microsoft.EventGrid/eventSubscriptions/* *. Cette action fait partie du rôle intégré Contributeur EventGrid EventSubscription.

> [!NOTE]
> Si le compte de stockage d’objets blob réside derrière un [point de terminaison privé](../storage/common/storage-private-endpoints.md) et bloque l’accès au réseau public, vous avez besoin de configurer des règles de réseau pour autoriser les communications entre le stockage d’objets blob et Azure Event Grid. Vous pouvez accorder l’accès au stockage à des services Azure approuvés, comme Event Grid, conformément à la [documentation de Stockage](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services), ou bien configurer des points de terminaison privés pour Event Grid qui correspondent à l’espace d’adressage du réseau virtuel, conformément à la [documentation d’Event Grid](../event-grid/configure-private-endpoints.md).

## <a name="create-a-trigger-with-ui"></a>Créer un déclencheur avec l’interface utilisateur

Cette section vous montre comment créer un déclencheur d’événements de stockage dans l’interface utilisateur des pipelines Azure Data Factory et Synapse.

1. Passez à l’onglet **Modification** de Data Factory ou à l’onglet **Intégrer** d’Azure Synapse.

1. Sélectionnez **Déclencheur** dans le menu, puis sélectionnez **Nouveau/Modifier**.

1. Sur la page **Ajouter des déclencheurs**, sélectionnez **Choisir un déclencheur…** , puis **+Nouveau**.

1. Sélectionnez le type de déclencheur **Événement de stockage**

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1.png" alt-text="Capture d’écran de la page auteur pour créer un nouveau déclencheur d’événement de stockage dans l’interface utilisateur Data Factory.":::
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1-synapse.png" alt-text="Capture d’écran de la page Auteur pour créer un nouveau déclencheur d’événement de stockage dans l’interface utilisateur Azure Synapse.":::

5. Sélectionnez votre compte de stockage dans la liste déroulante abonnement Azure ou manuellement à l’aide de son ID de ressource de compte de stockage. Choisissez le conteneur sur lequel vous souhaitez que les événements se produisent. La sélection du conteneur est obligatoire, mais gardez à l’esprit que la sélection de tous les conteneurs peut entraîner un grand nombre d’événements.

   > [!NOTE]
   > Le déclencheur d’événements de stockage ne prend actuellement en charge qu’Azure Data Lake Storage Gen2 et des comptes de stockage version 2 à usage général. En raison d’une limitation d’Azure Event Grid, Azure Data Factory ne prend en charge qu’un maximum de 500 déclencheurs d’événements de stockage par compte de stockage. Si vous atteignez la limite, contactez le support technique pour obtenir des suggestions et accroitre la limite au moment de l’évaluation par l’équipe Event Grid. 

   > [!NOTE]
   > Pour créer un déclencheur d’événements de stockage ou le modifier, le compte Azure utilisé pour se connecter au service et publier le déclencheur d’événements de stockage doit disposer de l’autorisation de contrôle d’accès en fonction du rôle (Azure RBAC) appropriée sur le compte de stockage. Aucune autorisation supplémentaire n’est requise : le principal de service pour Azure Data Factory et Azure Synapse n’a _pas_ besoin d’une autorisation spéciale sur le compte de stockage ni sur Event Grid. Pour plus d’informations sur le contrôle d'accès, consultez la page [Contrôle d'accès en fonction du rôle](#role-based-access-control).

1. Avec les propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), vous pouvez spécifier les conteneurs, les dossiers et les noms d’objets blob pour lesquels vous souhaitez recevoir des événements. Votre déclencheur d’événements de stockage requiert la définition d’au moins une de ces propriétés. Vous pouvez utiliser divers modèles pour les deux propriétés **Blob path begins with** (Chemin d’accès de l’objet blob commence par) et **Blob path ends with** (Chemin d’accès de l’objet blob se termine par), comme indiqué dans les exemples plus loin dans cet article.

    * **Blob path begins with:** (Chemin d’accès de l’objet blob commence par) Le chemin d’accès de l’objet blob doit commencer par un chemin d’accès au dossier. Les valeurs autorisées comprennent `2018/` et `2018/april/shoes.csv`. Ce champ ne peut pas être sélectionné si aucun conteneur n’est sélectionné.
    * **Blob path ends with:** (Chemin d’accès de l’objet blob se termine par) Le chemin d’accès de l’objet blob doit se terminer par un nom de fichier ou une extension. Les valeurs autorisées comprennent `shoes.csv` et `.csv`. Le nom du conteneur et du dossier, s’ils sont spécifiés, doivent être séparés par un segment `/blobs/`. Par exemple, un conteneur nommé « Orders » peut avoir la valeur `/orders/blobs/2018/april/shoes.csv`. Pour spécifier un dossier dans n’importe quel conteneur, omettez le caractère « / » de début. Par exemple, `april/shoes.csv` déclenche un événement sur tout fichier nommé `shoes.csv` dans le dossier appelé « avril » dans n’importe quel conteneur.
    * Notez que seuls les critères spéciaux suivants sont autorisés dans le déclencheur d’événements de stockage : le chemin de l’objet blob **commence par** et **se termine par**. Les autres types de correspondance par caractères génériques ne sont pas pris en charge pour le type de déclencheur.

1. Indiquez si votre déclencheur répondra à un événement **créé par un objet Blob**, **supprimé par un objet Blob** ou les deux. Dans l’emplacement de stockage spécifié, chaque événement déclenche les pipelines Data Factory et Synapse associés au déclencheur.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-2.png" alt-text="Capture d’écran de la page de création du déclencheur d’événements de stockage.":::

1. Indiquez si votre déclencheur ignore ou non les objets blob de zéro octet.

1. Une fois que vous avez configuré le déclencheur, cliquez sur **Suivant : Aperçu des données**. Cet écran affiche les blobs existants correspondant à la configuration de votre déclencheur d’événements de stockage. Assurez-vous d’avoir des filtres spécifiques. La configuration de filtres trop larges peut correspondre à un grand nombre de fichiers créés/supprimés et peut avoir un impact significatif sur vos coûts. Une fois que vos conditions de filtre ont été vérifiées, cliquez sur **Terminer**.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-3.png" alt-text="Capture d’écran de la page d’aperçu du déclencheur d’événements de stockage.":::

1. Pour joindre un pipeline à ce déclencheur, accédez aux canevas du pipeline et cliquez sur **Déclencheur**, puis sélectionnez **Nouveau/Modifier**. Lorsque la navigation latérale s’affiche, cliquez sur le menu déroulant **Choisir le déclencheur...** et sélectionnez le déclencheur que vous avez créé. Cliquez sur **Suivant : Aperçu des données** pour confirmer que la configuration est correcte, puis cliquez sur **Suivant** pour valider que l’aperçu des données est correct.

1. Si votre pipeline possède des paramètres, vous pouvez les spécifier dans la barre de navigation latérale du paramètre d’exécution du déclencheur. Le déclencheur d’événements de stockage capture le chemin de dossier et le nom de fichier du blob dans les propriétés `@triggerBody().folderPath` et `@triggerBody().fileName`. Pour utiliser les valeurs de ces propriétés dans un pipeline, vous devez mapper les propriétés aux paramètres de pipeline. Après le mappage des propriétés aux paramètres, vous pouvez accéder aux valeurs capturées par le déclencheur à l’aide de l’expression `@pipeline().parameters.parameterName` tout au long du pipeline. Pour plus d’informations, consultez [Référencer des métadonnées de déclencheur dans des pipelines](how-to-use-trigger-parameterization.md)

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-4.png" alt-text="Capture d’écran des propriétés de mappage du déclencheur d’événements de stockage aux paramètres de pipeline.":::

   Dans l’exemple précédent, le déclencheur est configuré pour se déclencher lorsqu’un chemin d’accès à un objet Blob se terminant par .csv est créé dans le dossier _event-testing_ du conteneur _sample-data_. Les propriétés **folderPath** et **filename** capturent l’emplacement du nouvel objet Blob. Par exemple, lorsque MoviesDB.csv est ajouté au chemin d’accès sample-data/event-testing, `@triggerBody().folderPath` a la valeur de `sample-data/event-testing` et `@triggerBody().fileName` a la valeur de `moviesDB.csv`. Ces valeurs sont mappées dans l’exemple aux paramètres de pipeline `sourceFolder` et `sourceFile` peuvent être utilisés dans l’ensemble du pipeline en tant que `@pipeline().parameters.sourceFolder` et `@pipeline().parameters.sourceFile` respectivement.

   > [!NOTE]
   > Si vous créez votre pipeline et votre déclencheur dans [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md), vous devez utiliser `@trigger().outputs.body.fileName` et `@trigger().outputs.body.folderPath` en guise de paramètres. Ces deux propriétés capturent les informations BLOB. Utilisez ces propriétés au lieu de `@triggerBody().fileName` et `@triggerBody().folderPath`.

1. Une fois que vous avez terminé, cliquez sur **Terminer**.

## <a name="json-schema"></a>Schéma JSON

Le tableau suivant fournit une vue d’ensemble des éléments de schéma associés aux déclencheurs d’événements de stockage :

| **Élément JSON** | **Description** | **Type** | **Valeurs autorisées** | **Obligatoire** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID de ressource Azure Resource Manager du compte de stockage. | String | ID d’Azure Resource Manager | Oui |
| **events** | Type des événements qui entraîne l’activation de ce déclencheur. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Oui, n’importe quelle combinaison de ces valeurs. |
| **blobPathBeginsWith** | Le chemin d’accès de l’objet blob doit commencer par le modèle fourni pour activer le déclencheur. Par exemple, `/records/blobs/december/` active uniquement le déclencheur pour les objets blob du dossier `december` sous le conteneur `records`. | String   | | Vous devez indiquer une valeur pour l’une de ces propriétés au moins : `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **blobPathEndsWith** | Le chemin d’accès de l’objet blob doit se terminer par le modèle fourni pour activer le déclencheur. Par exemple, `december/boxes.csv` active uniquement le déclencheur pour les objets blob nommés `boxes` dans un dossier `december`. | String   | | Vous devez indiquer une valeur pour l’une de ces propriétés au moins : `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Indique si des objets blob de zéro octet déclenchent ou non une exécution de pipeline. Valeur par défaut : true. | Boolean | True ou False | Non |

## <a name="examples-of-storage-event-triggers"></a>Exemples de déclencheurs d’événements de stockage

Cette section fournit des exemples de paramètres de déclencheur d’événements de stockage.

> [!IMPORTANT]
> Vous devez inclure le segment `/blobs/` du chemin, comme indiqué dans les exemples suivants, chaque fois que vous spécifiez conteneur et dossier, conteneur et fichier, ou conteneur, dossier et fichier. Pour **blobPathBeginsWith**, l’interface utilisateur ajoute automatiquement `/blobs/` entre le nom du dossier et du conteneur dans le JSON du déclencheur.

| Propriété | Exemple | Description |
|---|---|---|
| **Blob path begins with** | `/containername/` | Reçoit les événements de tout objet blob du conteneur. |
| **Blob path begins with** | `/containername/blobs/foldername/` | Reçoit les événements de tout objet blob du conteneur `containername` et du dossier `foldername`. |
| **Blob path begins with** | `/containername/blobs/foldername/subfoldername/` | Vous pouvez également référencer un sous-dossier. |
| **Blob path begins with** | `/containername/blobs/foldername/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` dans le dossier `foldername`, sous le conteneur `containername`. |
| **Blob path ends with** | `file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` dans n’importe quel chemin d’accès. |
| **Blob path ends with** | `/containername/blobs/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt` sous le conteneur `containername`. |
| **Blob path ends with** | `foldername/file.txt` | Reçoit les événements d’un objet blob nommé `file.txt`, dans le dossier `foldername`, sous n’importe quel conteneur. |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Les pipelines Azure Data Factory et Synapse utilisent le contrôle d’accès en fonction du rôle (Azure RBAC) pour garantir que l’accès non autorisé pour écouter, s’abonner à des mises à jour et déclencher des pipelines liés à des événements blob, est strictement interdit.

* Pour créer un déclencheur d’événements de stockage ou mettre à jour un existant, le compte Azure connecté au service doit avoir un accès approprié au compte de stockage concerné. Dans le cas contraire, l’opération échouera avec le message _Accès refusé_.
* Azure Data Factory et Azure Synapse n’ont besoin d’aucune autorisation spéciale sur votre Event Grid et vous n’avez _pas_ besoin d’attribuer une autorisation RBAC spéciale au principal de service Data Factory ou Azure Synapse pour l’opération.

L’un des paramètres RBAC suivants fonctionne pour le déclencheur d’événements de stockage :

* Rôle de propriétaire pour le compte de stockage
* Rôle de contributeur au compte de stockage
* Autorisation _Microsoft.EventGrid/EventSubscriptions/Write_ sur le compte de stockage _/subscriptions/####/resourceGroups/####/providers/Microsoft.Storage/storageAccounts/storageAccountName_

Pour comprendre comment le service offre les deux promesses, nous allons reprendre une étape et jeter un coup d’œil en coulisses. Voici les flux de travail de haut niveau pour l’intégration entre Azure Data Factory/Azure Synapse, Stockage et Event Grid.

### <a name="create-a-new-storage-event-trigger"></a>Créer un déclencheur d’événements de stockage

Ce flux de travail de haut niveau décrit comment Azure Data Factory interagit avec Event Grid pour créer un déclencheur d’événements de stockage.  Pour Azure Synapse, le flux de données est le même : les pipelines Synapse prennent le rôle de Data Factory dans le diagramme ci-dessous.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Flux de travail de création du déclencheur d’événements de stockage.":::

Deux éléments notables dans les flux de travail :

* Azure Data Factory et Azure Synapse n’ont _aucun_ contact direct avec le compte de stockage. La demande de création d’un abonnement est relayée et traitée par Event Grid. Par conséquent, le service n’a besoin d’aucune autorisation sur le compte de stockage pour cette étape.

* Le contrôle d’accès et la vérification des autorisations se produisent au sein du service. Avant que le service n’envoie une requête pour s’abonner à un événement de stockage, il vérifie l’autorisation pour l’utilisateur. Plus précisément, il vérifie si le compte Azure connecté et qui tente de créer le déclencheur d’événements de stockage dispose de l’accès approprié au compte de stockage concerné. En cas d’échec de la vérification de l’autorisation, la création du déclencheur échoue également.

### <a name="storage-event-trigger-pipeline-run"></a>Exécution de pipeline du déclencheur d’événements de stockage

Ces flux de travail de haut niveau décrivent l’exécution de pipeline des déclencheurs d’événements de stockage via Event Grid. Pour Azure Synapse, le flux de données est le même : les pipelines Synapse prennent le rôle de Data Factory dans le diagramme ci-dessous.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Flux de travail des exécutions de pipeline déclenchant des événements de stockage.":::

Le flux de travail inclut trois appels perceptibles liés aux pipelines de déclenchement d’événements dans le service :

* Event Grid utilise un modèle de transmission de type push qui relaie le message le plus rapidement possible lorsque le stockage dépose le message dans le système. Cela diffère du système de messagerie, comme Kafka, qui utilise un système d’extraction.
* Le déclencheur d’événements sert d’écouteur actif du message entrant et déclenche correctement le pipeline associé.
* Le déclencheur d’événements de stockage lui-même ne crée pas de contact direct avec le compte de stockage

  * Cela dit, si vous avez une copie ou une autre activité dans le pipeline pour traiter les données dans le compte de stockage, le service effectue un contact direct avec le stockage, à l’aide des informations d’identification stockées dans le service lié. Vérifier que le service lié est correctement configuré
  * Toutefois, si vous n’effectuez aucune référence au compte de stockage dans le pipeline, vous n’avez pas besoin d’accorder l’autorisation au service d’accéder au compte de stockage

## <a name="next-steps"></a>Étapes suivantes

* Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#trigger-execution).
* Découvrez comment référencer les métadonnées d’un déclencheur dans le pipeline avec la section [Référencer les métadonnées de déclencheur dnas l’exécution de pipeline](how-to-use-trigger-parameterization.md)
