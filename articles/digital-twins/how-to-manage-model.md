---
title: Gérer les modèles DTDL
titleSuffix: Azure Digital Twins
description: Découvrez comment gérer des modèles DTDL dans Azure Digital Twins, y compris comment les créer, les modifier et les supprimer.
author: baanders
ms.author: baanders
ms.date: 10/20/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7ae5a3293b7b9ba4712c3762b18b6d9c66eab926
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507167"
---
# <a name="manage-azure-digital-twins-models"></a>Gérer les modèles Azure Digital Twins

Cet article explique comment gérer les [modèles](concepts-models.md) dans votre instance Azure Digital Twins. Les opérations de gestion incluent le chargement, la validation, la récupération et la suppression des modèles. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-developer-interfaces.md](../../includes/digital-twins-developer-interfaces.md)]

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

:::image type="content" source="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png" alt-text="Capture d’écran d’Azure Digital Twins Explorer montrant un exemple de graphe de modèle" lightbox="media/how-to-use-azure-digital-twins-explorer/model-graph-panel.png":::

## <a name="create-models"></a>Créer des modèles

Les modèles pour Azure Digital Twins sont écrits en DTDL et enregistrés sous forme de fichiers .JSON. Il existe également une [extension DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponible pour [Visual Studio Code](https://code.visualstudio.com/). Cette extension fournit une fonctionnalité de validation de la syntaxe et d’autres fonctionnalités facilitant l’écriture de documents DTDL.

Prenons l’exemple d’un hôpital souhaitant disposer d’une représentation numérique des pièces de son bâtiment. Chaque pièce contient un distributeur de savon intelligent permettant de contrôler le lavage des mains, et des capteurs pour suivre le trafic.

La première étape de la solution consiste à créer des modèles pour représenter chaque aspect de l’hôpital. Dans ce scénario, la chambre d’un patient peut être décrite comme suit :

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Il s’agit d’un exemple de corps pour un fichier. JSON au sein duquel un modèle est défini et enregistré, et doit être téléchargé dans le cadre d’un projet client. En revanche, l’appel d’API REST prend un tableau de définitions de modèle comme celle ci-dessus (qui est mappée à un `IEnumerable<string>` dans le kit de développement logiciel .NET). Par conséquent, pour utiliser ce modèle dans l’API REST directement, entourez-le avec des crochets.

Ce modèle définit un nom et un ID unique pour chaque chambre de patient, ainsi que des propriétés représentant le nombre de visiteurs et l’état de lavage des mains. Ces compteurs sont mis à jour d’après les capteurs de mouvement et les distributeurs de savon intelligents ; ils sont utilisés ensemble pour calculer le pourcentage de lavage des mains (propriété *handWashPercentage*). Le modèle définit également une relation *hasDevices*, qui sera utilisée pour connecter toute [représentation numérique](concepts-twins-graph.md) basée sur ce modèle Room aux périphériques réels.

En suivant cette méthode, vous pouvez définir des modèles pour l’hôpital entier, ou bien pour certaines zones.

### <a name="validate-syntax"></a>Valider la syntaxe

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Charger des modèles

Une fois les modèles créés, vous pouvez les charger vers l’instance Azure Digital Twins.

Lorsque vous êtes prêt à charger un modèle, vous pouvez utiliser l’extrait de code suivant pour le [kit de développement logiciel (SDK) .NET](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true): :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Vous pouvez également charger plusieurs modèles dans une seule transaction. 

Si vous utilisez le kit de développement logiciel (SDK), vous pouvez charger plusieurs fichiers de modèle avec la méthode `CreateModels` comme suit :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Si vous utilisez les [API REST](/rest/api/azure-digitaltwins/) ou l’interface [Azure CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true), vous pouvez également charger différents modèles en plaçant plusieurs définitions de modèle dans un seul fichier JSON afin de les charger ensemble. Dans ce cas, les modèles doivent être placés dans un tableau JSON dans le fichier, comme dans l’exemple suivant :

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Lors du chargement, les fichiers de modèle sont validés par le service.

## <a name="retrieve-models"></a>Récupérer des modèles

Vous pouvez répertorier et récupérer des modèles stockés sur votre instance Azure Digital Twins. 

Les options disponibles sont les suivantes :
* Récupérer un seul modèle
* Récupérer tous les modèles
* Récupérer les métadonnées et les dépendances pour les modèles

Voici quelques exemples d’appels :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Tous les appels de SDK pour récupérer les modèles retournent des objets `DigitalTwinsModelData`. `DigitalTwinsModelData` contient les métadonnées relatives au modèle stocké dans l’instance Azure Digital Twins, par exemple le nom, le DTMI et la date de création du modèle. L’objet `DigitalTwinsModelData` comprend également le modèle lui-même. Par conséquent, en fonction des paramètres, vous pouvez utiliser les appels de récupération pour récupérer uniquement les métadonnées (utile lorsque vous souhaitez afficher une liste d’interfaces utilisateur des outils disponibles, par exemple) ou le modèle entier.

L’appel `RetrieveModelWithDependencies` retourne non seulement le modèle demandé, mais également tous les modèles dont il dépend.

Les modèles ne sont pas nécessairement retournés exactement sous la même forme que dans le document où ils ont été chargés. Azure Digital Twins garantit uniquement que la forme de retour est sémantiquement équivalente. 

## <a name="update-models"></a>Mettre à jour les modèles

Cette section décrit les points à prendre en considération pour mettre à jour des modèles ainsi que les différentes stratégies de mise à jour.

### <a name="before-updating-think-in-the-context-of-your-entire-solution"></a>Avant la mise à jour : ayez une réflexion qui prend en compte le contexte de votre solution entière

Avant de procéder à des mises à jour de vos modèles, nous vous recommandons d’avoir une réflexion globale au sujet de votre solution et de l’impact des modifications que vous souhaitez apporter aux modèles. Dans une solution Azure Digital Twins, les modèles sont souvent interconnectés. Il est donc important de garder à l’esprit l’éventualité de modifications en cascade : quand vous mettez à jour un modèle, vous devrez peut-être également mettre à jour plusieurs autres modèles. Non seulement la mise à jour des modèles a un impact sur les jumeaux qui utilisent les modèles modifiés, mais elle peut aussi impacter le code d’entrée et de traitement, les applications clientes et les rapports automatisés.

Voici quelques recommandations utiles pour gérer vos transitions de modèles sans problème :
* Au lieu de considérer les modèles comme des entités distinctes, songez à développer votre ensemble de modèles quand cela est approprié pour que les modèles et leurs relations soient toujours à jour les uns par rapport aux autres.
* Traitez les modèles comme du code source et gérez-les dans le contrôle de code source. Travaillez sur vos modèles et leurs modifications avec la même rigueur et attention que pour tout autre code de votre solution.

Lorsque vous êtes prêt à poursuivre ce processus de mise à jour de vos modèles, référez-vous au reste de cette section, qui décrit les stratégies de mise à jour que vous pouvez implémenter.

### <a name="strategies-for-updating-models"></a>Stratégies de mise à jour des modèles

Une fois qu’un modèle est chargé sur votre instance Azure Digital Twins, l’interface du modèle est immuable, c’est-à-dire qu’elle ne peut pas être modifiée selon le processus de « modification » habituel des modèles. Azure Digital Twins n’autorise pas non plus le rechargement du même modèle strictement identique si un modèle correspondant existe déjà dans l’instance.

Pour apporter des modifications à un modèle (par exemple, mettre à jour `displayName` ou `description`, ou ajouter et supprimer des propriétés), vous ne pouvez le faire qu’en remplaçant le modèle d’origine.

Deux stratégies sont possibles pour remplacer un modèle :
* [Stratégie 1 : Charger une nouvelle version du modèle](#strategy-1-upload-new-model-version). Cette stratégie consiste à charger le modèle, en changeant le numéro de version, puis à mettre à jour les jumeaux pour qu’ils utilisent ce nouveau modèle. Les deux versions du modèle, l’ancienne et la nouvelle, coexistent dans votre instance jusqu’à ce que vous en supprimiez une.
    - **Utilisez cette stratégie quand** vous souhaitez uniquement mettre à jour certains des jumeaux qui utilisent le modèle, ou quand vous voulez vous assurer que les jumeaux restent toujours conformes à leurs modèles et accessibles en écriture durant la transition du modèle.
* [Stratégie 2 : Supprimer l’ancien modèle et recharger le nouveau](#strategy-2-delete-old-model-and-reupload). Cette stratégie consiste à supprimer le modèle d’origine et à charger à la place le nouveau modèle, en utilisant les mêmes nom et ID (valeur DTMI). L’ancien modèle est alors entièrement remplacé par le nouveau. 
    - **Utilisez cette stratégie quand** vous souhaitez mettre à jour simultanément tous les jumeaux qui utilisent ce modèle, en plus de tout le code réagissant aux modèles. Si la mise à jour d’un modèle comporte un changement cassant, les jumeaux ne seront pas conformes à leurs modèles pendant une brève période, le temps de la transition des jumeaux de l’ancien modèle vers le nouveau. La conséquence est que les jumeaux ne pourront pas être mis à jour tant que le nouveau modèle n’aura pas été chargé et que les jumeaux ne seront pas conformes à ce modèle.

>[!NOTE]
> Il est déconseillé de faire des changements cassants dans vos modèles en dehors de la phase de développement.

Lisez les sections suivantes pour découvrir chaque option de stratégie plus en détail.

### <a name="strategy-1-upload-new-model-version"></a>Stratégie 1 : Charger une nouvelle version du modèle

Cette option consiste à créer une autre version du modèle et à charger cette nouvelle version dans votre instance.

Cette opération ne remplace **pas** les versions antérieures du modèle. Ainsi, plusieurs versions du modèle coexistent dans votre instance jusqu’à ce que vous décidiez de [les supprimer](#remove-models). Étant donné que la nouvelle version et l’ancienne version du modèle coexistent, les jumeaux peuvent utiliser l’une ou l’autre de ces versions. Autrement dit, le chargement d’une nouvelle version d’un modèle n’impacte pas automatiquement les jumeaux existants. Les jumeaux existants sont conservés comme instances de l’ancienne version du modèle. Vous pouvez mettre à jour ces jumeaux vers la nouvelle version du modèle en leur appliquant un correctif.

Pour utiliser cette stratégie, effectuez les étapes expliquées ci-dessous.

#### <a name="1-create-and-upload-new-model-version"></a>1. Créer et charger une nouvelle version du modèle 

Pour créer une nouvelle version d’un modèle existant, commencez par le DTDL du modèle d’origine. Mettez à jour, ajoutez ou supprimez les champs que vous souhaitez modifier.

Ensuite, marquez ce modèle comme version plus récente en mettant à jour son champ `id`. La dernière section de l’ID de modèle, après le point-virgule (`;`), représente le numéro de modèle. Pour indiquer que ce modèle est une version plus récente, incrémentez le nombre à la fin de la valeur `id` en choisissant un nombre supérieur au numéro de la version actuelle.

Par exemple, si votre ID de modèle précédent ressemble à ceci :

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

La version 2 de ce modèle peut se présenter comme suit :

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Ensuite, [chargez](#upload-models) la nouvelle version du modèle dans votre instance. 

Cette version du modèle devient alors disponible dans votre instance pour les jumeaux numériques. Elle ne remplace **pas** les versions antérieures du modèle. Ainsi, plusieurs versions du modèle coexistent maintenant dans votre instance.

#### <a name="2-update-graph-elements-as-needed"></a>2. Mettre à jour les éléments graphiques en fonction des besoins

Ensuite, mettez à jour les **jumeaux et leurs relations** dans votre instance afin qu’ils utilisent la nouvelle version du modèle à la place de l’ancienne.

Aidez-vous des instructions ci-après pour [mettre à jour les jumeaux](how-to-manage-twin.md#update-a-digital-twins-model) et [mettre à jour les relations](how-to-manage-graph.md#update-relationships). L’opération de correction pour mettre à jour le modèle d’un jumeau ressemble à ceci :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

>[!IMPORTANT]
>Quand vous mettez à jour des jumeaux, utilisez le **même correctif** pour mettre à jour à la fois l’ID du modèle (avec la nouvelle version du modèle) et chaque champ devant être modifié sur les jumeaux pour les rendre conformes au nouveau modèle.

Vous pouvez aussi avoir besoin de mettre à jour les **relations** et d’autres **modèles** dans votre instance qui référencent ce modèle, afin qu’ils référencent bien la nouvelle version du modèle. Pour cela, vous devez effectuer une autre opération de mise à jour du modèle. Revenez au début de cette section et répétez le processus pour chacun des autres modèles nécessitant une mise à jour.

#### <a name="3-optional-decommission-or-delete-old-model-version"></a>3. (Facultatif) Désaffecter ou supprimer l’ancienne version du modèle

Si vous n’avez plus besoin de l’ancienne version du modèle, vous pouvez la [désaffecter](#decommissioning). Avec cette action, le modèle est conservé dans l’instance, mais il ne peut pas être utilisé pour créer d’autres jumeaux numériques.

Vous pouvez aussi [supprimer](#deletion) complètement l’ancien modèle si vous ne souhaitez pas le conserver dans l’instance.

Les deux liens ci-dessus renvoient vers les sections contenant un exemple de code et des informations relatives à la désaffectation et à la suppression de modèles.

### <a name="strategy-2-delete-old-model-and-reupload"></a>Stratégie 2 : Supprimer l’ancien modèle et recharger le nouveau

Au lieu d’incrémenter la version d’un modèle, vous pouvez supprimer complètement un modèle et recharger un modèle modifié dans l’instance.

Comme Azure Digital Twins ne se souvient pas que l’ancien modèle a déjà été chargé, cette action revient à charger un modèle entièrement nouveau. Les jumeaux dans le graphe qui utilisent le modèle basculent automatiquement vers la nouvelle définition disponible. En fonction des différences qu’il y a entre la nouvelle définition et l’ancienne, ces jumeaux peuvent avoir des propriétés et des relations qui font référence à la définition supprimée et qui ne sont pas valides avec la nouvelle définition. Dans ce cas, vous devez les corriger afin qu’ils restent valides.

Pour utiliser cette stratégie, effectuez les étapes expliquées ci-dessous.

### <a name="1-delete-old-model"></a>1. Supprimer l’ancien modèle

Étant donné qu’Azure Digital Twins n’autorise pas l’existence de modèles ayant le même ID, commencez par supprimer le modèle d’origine de votre instance. 

>[!NOTE]
> Si vous avez d’autres modèles qui dépendent de ce modèle (par héritage ou certains composants), vous devez supprimer ces références avant de supprimer le modèle. Vous pouvez mettre à jour ces modèles dépendants pour supprimer temporairement les références dans un premier temps, ou supprimer les modèles dépendants et les recharger lors d’une étape ultérieure.

Aidez-vous des instructions suivantes pour [supprimer votre modèle d’origine](#deletion). Cette action laisse les jumeaux qui utilisaient ce modèle temporairement « orphelins », car ils utilisent désormais un modèle qui n’existe plus. Cet état sera corrigé à l’étape suivante lors du rechargement du modèle mis à jour.

### <a name="2-create-and-upload-new-model"></a>2. Créer et charger le nouveau modèle

Commencez par le DTDL du modèle d’origine. Mettez à jour, ajoutez ou supprimez les champs que vous souhaitez modifier.

Ensuite, [chargez le modèle](#upload-models) dans l’instance, comme s’il s’agissait d’un tout nouveau modèle chargé pour la première fois.

### <a name="3-update-graph-elements-as-needed"></a>3. Mettre à jour les éléments du graphe si nécessaire

Maintenant que votre nouveau modèle a été chargé à la place de l’ancien, les jumeaux dans votre graphe commenceront automatiquement à utiliser la nouvelle définition de modèle après l’expiration et la réinitialisation de la mise en cache dans l’instance. **Ce processus peut prendre de dix à quinze minutes ou plus** en fonction de la taille de votre graphe. Après cela, les propriétés nouvelles et modifiées de votre modèle seront accessibles, au contraire des propriétés supprimées.

>[!NOTE]
> Si vous avez supprimé d’autres modèles dépendants précédemment afin de supprimer le modèle d’origine, rechargez-les maintenant après la réinitialisation du cache. Si vous avez mis à jour les modèles dépendants pour supprimer temporairement les références au modèle d’origine, vous pouvez les mettre de nouveau à jour avec la référence.

Ensuite, mettez à jour **les jumeaux et les relations** dans votre instance afin que leurs propriétés correspondent aux propriétés définies par le nouveau modèle. À ce stade, les jumeaux qui ne correspondent pas à leur modèle peuvent toujours être lus, mais ils ne sont pas accessibles en écriture. Pour plus d’informations sur l’état des jumeaux sans modèle valide, consultez [Jumeaux sans modèles](#after-deletion-twins-without-models).

Il existe deux façons de mettre à jour les jumeaux et les relations du nouveau modèle afin de les rendre de nouveau accessibles en écriture :
* Corrigez les jumeaux et les relations de manière à ce qu’ils soient conformes au nouveau modèle. Aidez-vous des instructions ci-après pour [mettre à jour les jumeaux](how-to-manage-twin.md#update-a-digital-twin) et [mettre à jour les relations](how-to-manage-graph.md#update-relationships).
    - **Si vous avez ajouté des propriétés**, il n’est pas nécessaire de mettre à jour les jumeaux et les relations avec les nouvelles valeurs, car les jumeaux qui n’ont pas les nouvelles valeurs restent valides. Vous pouvez toutefois les corriger si vous souhaitez ajouter des valeurs pour les nouvelles propriétés.
    - **Si vous avez supprimé des propriétés**, il est nécessaire de corriger les jumeaux pour supprimer les propriétés qui ne sont maintenant plus valides avec le nouveau modèle.
    - **Si vous avez mis à jour des propriétés**, il est nécessaire de corriger les jumeaux pour mettre à jour les valeurs des propriétés modifiées afin qu’elles soient valides avec le nouveau modèle.
* Supprimez les jumeaux et les relations qui utilisent le modèle, puis recréez-les. Aidez-vous des instructions suivantes pour [supprimer les jumeaux](how-to-manage-twin.md#delete-a-digital-twin) et [recréer les jumeaux](how-to-manage-twin.md#create-a-digital-twin), et [supprimer les relations](how-to-manage-graph.md#delete-relationships) et [recréer les relations](how-to-manage-graph.md#create-relationships).
    - Vous souhaiterez peut-être effectuer cette opération si vous modifiez largement le modèle et qu’il sera difficile de mettre à jour les jumeaux existants de manière correcte. Toutefois, recréer entièrement les jumeaux et les relations peut s’avérer compliqué si vous avez beaucoup de jumeaux qui sont interconnectés par de nombreuses relations.

## <a name="remove-models"></a>Supprimer des modèles

Les modèles peuvent être supprimés du service de l’une des deux manières suivantes :
* **Désaffectation** : Une fois qu’un modèle est désactivé, vous ne pouvez plus l’utiliser pour créer de nouvelles représentations numériques. Les représentations numériques existantes utilisant déjà ce modèle ne sont pas affectées. vous pouvez donc toujours les mettre à jour avec des éléments tels que les modifications de propriétés et l’ajout ou la suppression de relations.
* **Suppression** : Cette opération supprime complètement le modèle de la solution. Les représentations qui utilisaient ce modèle ne sont plus associées à un modèle valide. Elles sont donc traitées comme si elles n’en avaient pas. Vous pouvez toujours lire ces jumeaux, mais pas les mettre à jour tant qu’ils ne sont pas réaffectés à un autre modèle.

Ces deux opérations sont des fonctionnalités distinctes qui n’ont pas d’impact l’une sur l’autre, mais elles peuvent être utilisées ensemble pour supprimer un modèle progressivement. 

### <a name="decommissioning"></a>Désaffectation

Pour désaffecter un modèle, vous pouvez utiliser la méthode [DecommissionModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.decommissionmodel?view=azure-dotnet&preserve-view=true) fournie dans le SDK :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

Vous pouvez également effectuer cette opération par un appel de l’API REST [DigitalTwinModels Update](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_update). La propriété `decommissioned` est la seule propriété qui peut être remplacée par cet appel d’API. Le document JSON Patch ressemble à ceci :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-decommission-model.json":::

L’état de désaffectation d’un modèle est inclus dans les enregistrements `ModelData` retournés par les API de récupération de modèle.

### <a name="deletion"></a>Suppression

Vous pouvez supprimer tous les modèles de votre instance d’une seule traite, ou vous pouvez le faire sur une base individuelle.

Pour obtenir un exemple de suppression de tous les modèles en même temps, consultez le dépôt [Exemples Azure Digital Twins de bout en bout](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/CommandLoop.cs) dans GitHub. Le fichier *CommandLoop.cs* contient une fonction `CommandDeleteAllModels` dont le code supprime la totalité des modèles dans l’instance.

Pour supprimer les modèles de façon individuelle, aidez-vous des instructions et des informations fournies dans la suite de cette section.

#### <a name="before-deletion-deletion-requirements"></a>Avant la suppression : Conditions requises pour la suppression

En règle générale, les modèles peuvent être supprimés à tout moment.

L’exception concerne les modèles dont dépendent d’autres modèles, qu’il s’agisse d’une relation `extends` ou en tant que composant. Par exemple, si un modèle ConferenceRoom étend un modèle Room, et qu’il possède un modèle ACUnit en tant que composant, vous ne pouvez pas supprimer Room ou ACUnit avant que ConferenceRoom ne supprime leurs références respectives. 

Pour ce faire, vous pouvez mettre à jour le modèle dépendant pour supprimer les dépendances, ou supprimer complètement le modèle dépendant.

#### <a name="during-deletion-deletion-process"></a>Durant la suppression : Processus de suppression

Même si un modèle répond aux exigences pour une suppression immédiate, vous voudrez peut-être suivre quelques étapes préliminaires pour éviter des conséquences inattendues pour la représentation numérique. Voici quelques étapes qui peuvent vous aider à gérer le processus :
1. Tout d’abord, désaffectez le modèle
2. Attendez quelques minutes, pour vous assurer que le service a bien traité toutes les demandes de création de représentations de dernière minute envoyées avant la désaffectation
3. Interrogez les représentations par modèle pour voir toutes les représentations utilisant le modèle désormais désactivé
4. Supprimez les représentations si vous n’en avez plus besoin ou bien liez-les à un nouveau modèle si nécessaire. Vous pouvez également choisir de conserver les jumeaux seuls, auquel cas ils deviennent des jumeaux sans modèle une fois le modèle supprimé. Consultez la section suivante pour connaître les implications de cet état.
5. Patientez quelques minutes pour vous assurer que les modifications ont été répercutées
6. Supprimer le modèle 

Pour supprimer un modèle, vous pouvez utiliser l’appel de SDK [DeleteModel](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.deletemodel?view=azure-dotnet&preserve-view=true) :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

Vous pouvez aussi supprimer un modèle à l’aide de l’appel d'API REST [DigitalTwinModels Delete](/rest/api/digital-twins/dataplane/models/digitaltwinmodels_delete).

#### <a name="after-deletion-twins-without-models"></a>Après la suppression : Représentations sans modèle

Une fois qu’un modèle est supprimé, les représentations numériques qui l’utilisaient sont désormais considérées comme sans modèle. Il n’existe aucune requête vous permettant de lister tous les jumeaux dans cet état, mais vous *pouvez* toujours interroger les jumeaux par le modèle supprimé pour connaître les jumeaux concernés.

Voici une vue d’ensemble de ce que vous pouvez et ne pouvez pas faire avec des jumeaux sans modèle.

Choses que vous **pouvez** faire :
* Interroger la représentation
* Lire les propriétés
* Lire les relations sortantes
* Ajouter et supprimer des relations entrantes (d’autres représentations peuvent toujours former des relations *vers* cette représentation)
  - La `target` dans la définition de la relation peut toujours refléter le DTMI du modèle supprimé. Une relation sans cible définie peut également fonctionner ici.      
* Supprimer des relations
* Supprimer la représentation

Choses que vous **ne pouvez pas** faire :
* Modifier les relations sortantes (les relations *de* cette représentation vers d’autres représentations)
* Modifier les propriétés

#### <a name="after-deletion-reuploading-a-model"></a>Après la suppression : recharger un modèle

Une fois qu’un modèle a été supprimé, vous pouvez décider de charger un nouveau modèle ayant un ID identique à celui que vous avez supprimé. Voici ce qui se passe dans ce cas.
* Du point de vue du magasin de solutions, cette opération revient à charger un modèle entièrement nouveau. Le service ne se rappelle pas du chargement de l’ancien modèle.   
* S’il reste des jumeaux dans le graphe qui référencent le modèle supprimé, ces jumeaux ne sont plus orphelins, car l’ID de ce modèle est de nouveau valide avec la nouvelle définition. Cependant, si la nouvelle définition du modèle est différente de la définition du modèle supprimée, il est possible que ces jumeaux possèdent des propriétés et des relations qui correspondent à la définition supprimée et qui ne sont pas valides avec la nouvelle définition.

Azure Digital Twins n’empêche pas cet état ; aussi, veillez à corriger les jumeaux de sorte qu’ils restent valides après le changement de la définition du modèle.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer et gérer des représentations numériques basées sur vos modèles :
* [Gérer des jumeaux numériques](how-to-manage-twin.md)