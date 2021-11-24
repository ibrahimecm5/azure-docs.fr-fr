---
title: Extension Visual Studio Code pour Azure Video Analyzer
description: Cet article de référence explique comment utiliser les différentes fonctionnalités de l’extension Visual Studio Code pour Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0faa61b724420ad04cc489d51539c134569dd05
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287112"
---
# <a name="visual-studio-code-extension-for-azure-video-analyzer"></a>Extension Visual Studio Code pour Azure Video Analyzer

Azure Video Analyzer est une plateforme qui facilite la création de programmes d’analyse vidéo et l’extension Visual Studio Code associée est un outil qui facilite l’apprentissage de cette plateforme.  Cet article fait référence aux différentes fonctionnalités offertes par l’extension.  Il aborde les concepts de base suivants :

* Topologies de pipelines : création, modification, suppression, affichage du code JSON
* Pipelines en direct : création, activation, désactivation, suppression, affichage du code JSON
* Modification d’une topologie de pipeline : modules, paramètres, variables système, connexions, validation

Si vous n’avez pas configuré l’extension de façon à ce qu’elle se connecte à votre appareil de périphérie, suivez les étapes dans [Utiliser l’extension Visual Studio Code pour Azure Video Analyzer](./edge/use-visual-studio-code-extension.md).

## <a name="managing-pipelines-topology"></a>Gestion de la topologie des pipelines

Pour créer une topologie, sur le côté gauche de votre module, cliquez avec le bouton droit sur `Pipelines topologies`, puis sélectionnez `Create pipeline topology`.  Une nouvelle topologie vide s’ouvre.  Vous pouvez ensuite charger l’une des topologies prédéfinies en la sélectionnant dans la liste déroulante `Try sample topologies` située en haut ou vous pouvez créer une topologie vous-même.  

Une fois toutes les zones requises remplies, vous devez enregistrer la topologie avec `Save` en haut à droite.  Pour les exemples de topologies, les champs obligatoires doivent être préremplis.  La topologie est alors disponible pour la création de pipelines en direct.

Pour modifier une topologie existante, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le nom de la topologie, puis sélectionnez `Edit pipeline topology`.

Pour supprimer une topologie existante, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le nom de la topologie, puis sélectionnez `Delete pipeline topology`.  Les pipelines en direct devront d’abord être supprimés.

Si vous souhaitez afficher le code JSON sous-jacent derrière une topologie existante, à gauche sous les topologies du pipeline, cliquez avec le bouton droit sur le nom de la topologie, puis sélectionnez `Show pipeline topology JSON`.

## <a name="live-pipelines"></a>Pipelines en direct

Pour créer un pipeline en direct, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le nom de la topologie, puis sélectionnez `Create live pipeline`.  Vous devez ensuite renseigner un nom de pipeline en direct et tous les paramètres requis avant de continuer.  Dans le coin supérieur droit, vous pouvez cliquer sur `Save`, ce qui permet de l’enregistrer dans un état inactif, ou sur `Save and activate` pour démarrer immédiatement le pipeline en direct. 

Pour activer un pipeline en direct existant, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le nom du pipeline en direct, puis sélectionnez `Activate live pipeline`.

Pour désactiver une instance en cours d’exécution, dans la partie gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le pipeline en direct, puis sélectionnez `Deactivate live pipeline`.  Cela ne supprime pas le pipeline en direct.

Pour supprimer un pipeline en direct existant, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le pipeline en direct, puis sélectionnez `Delete live pipeline`.  Vous ne pouvez pas supprimer un pipeline en direct actif.

Si vous souhaitez afficher le code JSON sous-jacent derrière un pipeline en direct existant, à gauche sous les topologies de pipeline, cliquez avec le bouton droit sur le nom du pipeline en direct, puis sélectionnez `Show live pipeline JSON`.

## <a name="editing-a-topology"></a>Modification d’une topologie 

Les topologies de pipeline sont constituées d’un grand nombre d’éléments. Pour en savoir plus sur ces éléments, consultez la documentation de conception sur les [pipelines](pipeline.md). Cette section concerne les parties de l’interface qui vous aident à créer ou à modifier une topologie.

### <a name="modules"></a>Modules

Les modules sont disponibles sur la gauche et sont regroupés en sources, processeurs et récepteurs.  Pour en ajouter un à votre topologie, faites-le glisser dans la zone d’édition.

Si vous souhaitez qu’un autre module du même type possède les mêmes paramètres de départ, vous pouvez le dupliquer en cliquant dessus avec le bouton droit et en sélectionnant `Copy`.

Si vous souhaitez supprimer un module de la topologie, cliquez avec le bouton droit sur le module et sélectionnez `Delete`.

### <a name="parameters"></a>Paramètres

Vous pouvez modifier les attributs d’un module en cliquant dessus dans la zone d’édition.  Un volet s’affiche à droite avec la liste de tous les éléments.  Les champs requis sont indiqués par un astérisque rouge.  Le texte d’information contient une brève description à côté du nom de chaque élément.

Comme les topologies sont des modèles efficaces que vous pouvez utiliser pour créer plusieurs pipelines en direct, vous devez renseigner certains éléments au moment de la création.  Pour cela, utilisez des paramètres.  Pour activer ceci, cliquez avec le bouton droit sur les trois points situés à droite de l’attribut et sélectionnez `Parameterize`.  Une fenêtre apparaît.

Dans la fenêtre de paramétrage, vous pouvez créer un paramètre qui fonctionnera comme une valeur que vous remplirez lors de la création du pipeline en direct. Vous pouvez également sélectionner un paramètre qui existe déjà.  Pour en créer un, vous devez renseigner le nom et le type.  Vous pouvez éventuellement entrer une valeur par défaut si peu de modifications sont prévues.  Lorsqu’un pipeline en direct est créé, seuls les paramètres sans valeur par défaut sont requis.  Une fois ces opérations effectuées, cliquez sur `Set`.

Si vous souhaitez gérer vos paramètres existants, vous pouvez le faire avec l’option `Manage parameters` située en haut.  Le volet qui s’affiche vous permet d’ajouter de nouveaux paramètres et de modifier ou de supprimer des paramètres existants.

### <a name="system-variable"></a>Variable système

Lors de la création d’une série de pipelines en direct, vous voudrez probablement utiliser des variables pour aider à nommer des fichiers ou des sorties.  Par exemple, vous voudrez peut-être nommer un clip vidéo avec le nom et la date/heure du pipeline en direct. Cela vous permettra ainsi de savoir d’où il provenait et à quel moment.  Video Analyzer fournit trois variables système que vous pouvez utiliser dans vos modules pour vous aider.

| Variable système        | Description                                                  | Exemple              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | Date/heure UTC au format conforme de fichier ISO8601 (représentation de base AAAAMMJJThhmmss). | 20200222T173200Z     |
| System.TopologyName    | Nom fourni par l’utilisateur de la topologie de pipeline en cours d’exécution.          | IngestAndRecord      |
| System.PipelineName    | Nom fourni par l’utilisateur du pipeline en direct en cours d’exécution.          | camera001            |

### <a name="connections"></a>Connexions 

Lorsque vous créez une topologie, vous devez connecter les différents modules.  Cette opération s’effectue à l’aide de connexions.  À partir du cercle sur le bord d’un module, faites glisser vers le cercle du module suivant vers lequel vous souhaitez que les données soient acheminées.  Une connexion est alors générée.

Par défaut, les connexions envoient les données vidéo d’un module à un autre. Si vous souhaitez envoyer uniquement des données audio ou des données d’application, vous pouvez cliquer sur la connexion et modifier les types de sortie. Les types de données sélectionnables sont les suivants : vidéo, audio et application. La sélection de aucun des types de sortie est traitée comme l’envoi de toutes les données applicables à partir du nœud d’expéditeur.
