---
title: Encoder ou décoder des fichiers plats
description: Encoder ou décoder des fichiers plats pour l’intégration d’entreprise dans Azure Logic Apps avec Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: b2dd3b61ae3b612879e92b4c5ef8feb79b71b6fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429911"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Encoder et décoder des fichiers plats dans Azure Logic Apps

Avant de pouvoir envoyer du contenu XML à un partenaire commercial dans un scénario B2B, vous souhaitez peut-être d’abord encoder ce contenu. En générant un workflow d’application logique, vous pouvez encoder et décoder des fichiers plats à l’aide des actions **Fichier plat** [intégrées](../connectors/built-in.md#integration-account-built-in-actions).

Même si aucun déclencheur de **fichier plat** n’est disponible, vous pouvez utiliser un déclencheur ou une action différents pour obtenir ou alimenter le contenu XML à partir de différentes sources dans votre workflow à des fins d’encodage ou de décodage. Par exemple, vous pouvez utiliser le déclencheur de requête, une autre application ou d’autres [connecteurs pris en charge par Azure Logic Apps](../connectors/apis-list.md). Vous pouvez utiliser des actions **Fichier plat** avec des workflows dans les [types de ressources **Application logique (consommation)** et **Application logique (standard)** ](single-tenant-overview-compare.md).

> [!NOTE]
> Pour la ressource **Application logique (standard)** , les actions **Fichier plat** sont actuellement en version préliminaire. 

Cet article explique comment ajouter des actions de codage et de décodage de Fichier plat à un workflow d’application logique existant. Si vous débutez avec les applications logiques, consultez la documentation suivante :

* [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md)
* [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe dans le même emplacement ou la même région Azure que votre ressource d’application logique.

  * Si vous utilisez le type de ressource [Application logique (consommation) **, vous devez disposer d’un** compte d’intégration](logic-apps-overview.md#resource-type-and-host-environment-differences) qui répond aux exigences suivantes :

    * [Schéma](logic-apps-enterprise-integration-schemas.md) de fichier plat à utiliser pour encoder ou décoder le contenu XML.

    * Un [lien vers votre ressource d'application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si vous utilisez le [type de ressource **Application logique (Standard)**](logic-apps-overview.md#resource-type-and-host-environment-differences), vous ne stockez pas de schémas dans votre compte d’intégration. Au lieu de cela, vous pouvez [directement ajouter des schémas à votre ressource d’application logique](logic-apps-enterprise-integration-schemas.md) à l’aide du portail Azure ou de Visual Studio Code. Vous pouvez ensuite utiliser ces schémas sur plusieurs flux de travail au sein de la *même ressource d’application logique*.

    Toutefois, vous avez toujours besoin de ce compte pour stocker les artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ou [EDIFACT](logic-apps-enterprise-integration-edifact.md). Toutefois, vous n’avez pas besoin de lier votre ressource d’application logique à votre compte d’intégration, donc la fonctionnalité de liaison n’existe pas. Votre compte d’intégration doit encore répondre à d’autres exigences, telles que l’utilisation du même abonnement Azure et le même emplacement que votre ressource d’application logique.

    > [!NOTE]
    > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* Un workflow d’application logique, vide ou existant, dans lequel vous souhaitez utiliser l’action **Fichier plat**.

  Si vous avez un workflow vide, utilisez un déclencheur pour démarrer le workflow. Cet exemple utilise le déclencheur de demande.

## <a name="limits"></a>limites

Assurez-vous que les groupes XML contenus dans le schéma de fichier plat que vous générez ne présentent pas de nombres excessifs pour la propriété `max count` définie sur une valeur *supérieure à 1*. Évitez d’imbriquer un groupe XML avec une valeur de propriété `max count` supérieure à 1 à l’intérieur d’un autre groupe XML dont la propriété `max count` est supérieure à 1.

Chaque fois que le schéma de fichier plat permet de choisir le fragment suivant, le moteur Azure Logic Apps qui analyse le schéma génère un *symbole* et une *prédiction* pour ce fragment. Si le schéma autorise un trop grand nombre de ces constructions (par exemple, plus de 100 000), l’expansion du schéma devient excessivement volumineuse, ce qui consomme trop de ressources et de temps.

## <a name="add-flat-file-encoding-action"></a>Ajouter l’action Encodage de fichier plat

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Si vous disposez d’un workflow vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Sinon, passez à l’étape suivante.

   Cet exemple utilise le déclencheur de requête, appelé **Réception d’une requête HTTP**, et gère les requêtes entrantes provenant de l’extérieur du workflow de l’application logique. Pour ajouter le déclencheur de requête, procédez comme suit :

   1. Dans la zone de recherche du concepteur, sélectionnez **Intégré**. Dans la zone de recherche du Concepteur, entrez `HTTP request`.

   1. Dans la liste des déclencheurs, sélectionnez le déclencheur de requête appelé **Réception d’une requête HTTP**.

   > [!TIP]
   > Le schéma JSON est facultatif. Si vous avez un exemple de charge utile de la requête entrante, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**, entrez l’exemple de charge utile, puis sélectionnez **Terminé**. Le schéma figure dans la zone **Schéma JSON du corps de la requête**.

1. Dans l’étape de votre workflow où vous souhaitez ajouter l’action **Encodage de fichier plat**, choisissez l’une des options suivantes :

   * Pour ajouter l’action **Encodage de fichier plat** à la fin de votre workflow, sélectionnez **Nouvelle étape**.

   * Pour ajouter l’action **Encodage de fichier plat** entre des étapes existantes, positionnez votre pointeur sur la flèche qui connecte ces étapes de sorte que le signe plus ( **+** ) apparaisse. Sélectionnez ce signe plus, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche **Choisir une opération**, entrez `flat file`. Dans la liste des actions, sélectionnez l’action appelée **Encodage de fichier plat**.

   ![Capture d’écran montrant le Portail Azure et le concepteur de consommation avec « fichier plat » dans la zone de recherche et l’action « Encodage de fichier plat » sélectionnée.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-consumption.png)

1. Cliquez dans la zone **Contenu** pour afficher la liste du contenu dynamique. Dans la liste, dans la section **Réception d’une requête HTTP**, sélectionnez la propriété **Corps**, qui contient la sortie du corps de la requête du déclencheur et le contenu à encoder.

   ![Capture d’écran montrant le concepteur de consommation et la propriété « Contenu » avec la liste de contenu dynamique et le contenu sélectionné pour l’encodage.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-consumption.png)

   > [!TIP]
   > Si la propriété **Corps** n’apparaît pas dans la liste de contenu dynamique, sélectionnez **Voir plus** en regard de l’étiquette de section **Réception d’une requête HTTP**.
   > Vous pouvez également entrer directement le contenu à décoder dans la zone **Contenu**.

1. Dans la liste **Nom du schéma**, sélectionnez le schéma qui se trouve dans votre compte d’intégration lié à utiliser pour l’encodage, par exemple :

   ![Capture d’écran montrant le concepteur de consommation et la liste « Nom de schéma » ouverte avec le schéma sélectionné à utiliser pour l’encodage.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-consumption.png)

   > [!NOTE]
   > Si aucun schéma n’apparaît dans la liste, votre compte d’intégration ne contient aucun fichier de schéma à utiliser pour l’encodage. Chargez le schéma que vous voulez utiliser dans votre compte d’intégration.

1. Enregistrez votre flux de travail. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Pour tester votre workflow, envoyez une requête au point de terminaison HTTPS, qui apparaît dans la propriété **HTTP POST URL** du déclencheur de requête et ajoutez le contenu XML que vous souhaitez encoder dans le corps de la requête.

Vous avez maintenant terminé la configuration de votre action d’encodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier telle que Salesforce. Vous pouvez également envoyer ces données encodées à une partie. Pour envoyer la sortie de l’action d’encodage à Salesforce ou à votre partie, utilisez les autres [connecteurs disponibles dans Azure Logic Apps](../connectors/apis-list.md).

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Si vous disposez d’un workflow vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Sinon, passez à l’étape suivante.

   Cet exemple utilise le déclencheur de requête, appelé **Réception d’une requête HTTP**, et gère les requêtes entrantes provenant de l’extérieur du workflow de l’application logique. Pour ajouter le déclencheur de requête, procédez comme suit :

   1. Dans le concepteur, sélectionnez **Choisir une opération**. Une fois le volet **Choisir une opération** ouvert, dans la zone de recherche, sélectionnez **Intégrée**.

   1. Dans la zone de recherche, entrez `HTTP request`. Dans la liste des déclencheurs, sélectionnez le déclencheur de requête appelé **Réception d’une requête HTTP**.

     > [!TIP]
     > Le schéma JSON est facultatif. Si vous avez un exemple de charge utile de la requête entrante, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**, entrez l’exemple de charge utile, puis sélectionnez **Terminé**. Le schéma figure dans la zone **Schéma JSON du corps de la requête**.

1. Dans l’étape de votre workflow où vous souhaitez ajouter l’action **Encodage de fichier plat**, choisissez l’une des options suivantes :

   * Pour ajouter l’action **Encodage de fichier plat** à la fin de votre workflow, sélectionnez le signe moins ( **+** ), puis **Ajouter une action**.

   * Pour ajouter l’action **Encodage de fichier plat** entre des étapes existantes, sélectionnez le signe plus ( **+** ) entre celles-ci, puis sélectionnez **Ajouter une nouvelle étape**.

1. Une fois le volet **Choisir une opération** ouvert, dans la zone de recherche, sélectionnez **Intégrée**.

1. Dans la zone de recherche, entrez `flat file`. Dans la liste des actions, sélectionnez l’action appelée **Encodage de fichier plat**.

   ![Capture d’écran montrant le Portail Azure et le concepteur de workflow standard avec « fichier plat » dans la zone de recherche et l’action « Encodage de fichier plat » sélectionnée.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-standard.png)

1. Cliquez dans la zone **Contenu** pour afficher la liste du contenu dynamique. Dans la liste, dans la section **Réception d’une requête HTTP**, sélectionnez la propriété **Corps**, qui contient la sortie du corps de la requête du déclencheur et le contenu à encoder.

   ![Capture d’écran montrant le concepteur de workflow standard et la propriété « Contenu » avec la liste de contenu dynamique et le contenu sélectionné pour l’encodage.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-standard.png)

   > [!TIP]
   > Si la propriété **Corps** n’apparaît pas dans la liste de contenu dynamique, sélectionnez **Voir plus** en regard de l’étiquette de section **Réception d’une requête HTTP**.
   > Vous pouvez également entrer directement le contenu à décoder dans la zone **Contenu**.

1. Dans la liste **Nom**, sélectionnez le schéma que vous avez précédemment téléchargé vers votre ressource d’application logique pour l’encodage, par exemple :

   ![Capture d’écran montrant le concepteur de workflow standard et la liste « Nom » ouverte avec le schéma sélectionné à utiliser pour l’encodage.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-standard.png)

   > [!NOTE]
   > Si aucun schéma n’apparaît dans la liste, votre ressource d’application logique standard ne contient aucun fichier de schéma à utiliser pour l’encodage. Découvrez comment [charger le schéma que vous souhaitez utiliser dans votre ressource d’application logique standard](logic-apps-enterprise-integration-schemas.md).

1. Enregistrez votre flux de travail. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Pour tester votre workflow, envoyez une requête au point de terminaison HTTPS, qui apparaît dans la propriété **HTTP POST URL** du déclencheur de requête et ajoutez le contenu XML que vous souhaitez encoder dans le corps de la requête.

Vous avez maintenant terminé la configuration de votre action d’encodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier telle que Salesforce. Vous pouvez également envoyer ces données encodées à une partie. Pour envoyer la sortie de l’action d’encodage à Salesforce ou à votre partie, utilisez les autres [connecteurs disponibles dans Azure Logic Apps](../connectors/apis-list.md).

---

## <a name="add-flat-file-decoding-action"></a>Ajouter l’action Décodage de fichier plat

### <a name="consumption"></a>[Consommation](#tab/consumption)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Si vous disposez d’un workflow vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Sinon, passez à l’étape suivante.

   Cet exemple utilise le déclencheur de requête, appelé **Réception d’une requête HTTP**, et gère les requêtes entrantes provenant de l’extérieur du workflow de l’application logique. Pour ajouter le déclencheur de requête, procédez comme suit :

   1. Dans la zone de recherche du concepteur, sélectionnez **Intégré**. Dans la zone de recherche du Concepteur, entrez `HTTP request`.

   1. Dans la liste des déclencheurs, sélectionnez le déclencheur de requête appelé **Réception d’une requête HTTP**.

   > [!TIP]
   > Le schéma JSON est facultatif. Si vous avez un exemple de charge utile de la requête entrante, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**, entrez l’exemple de charge utile, puis sélectionnez **Terminé**. Le schéma figure dans la zone **Schéma JSON du corps de la requête**.

1. Dans l’étape de votre workflow où vous souhaitez ajouter l’action **Décodage de fichier plat**, choisissez l’une des options suivantes :

   * Pour ajouter l’action **Décodage de fichier plat** à la fin de votre workflow, sélectionnez **Nouvelle étape**.

   * Pour ajouter l’action **Décodage de fichier plat** entre des étapes existantes, positionnez votre pointeur sur la flèche qui connecte ces étapes de sorte que le signe plus ( **+** ) apparaisse. Sélectionnez ce signe plus, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche **Choisir une opération**, entrez `flat file`. Dans la liste des actions, sélectionnez l’action appelée **Décodage de fichier plat**.

   ![Capture d’écran montrant le Portail Azure et le concepteur de consommation avec « fichier plat » dans la zone de recherche et l’action « Décodage de fichier plat » sélectionnée.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-consumption.png)

1. Cliquez dans la zone **Contenu** pour afficher la liste du contenu dynamique. Dans la liste, dans la section **Réception d’une requête HTTP**, sélectionnez la propriété **Corps**, qui contient la sortie du corps de la requête du déclencheur et le contenu à décoder.

   ![Capture d’écran montrant le concepteur de consommation et la propriété « Contenu » avec la liste de contenu dynamique et le contenu sélectionné pour le décodage.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-consumption.png)

   > [!TIP]
   > Si la propriété **Corps** n’apparaît pas dans la liste de contenu dynamique, sélectionnez **Voir plus** en regard de l’étiquette de section **Réception d’une requête HTTP**. Vous pouvez également entrer directement le contenu à décoder dans la zone **Contenu**.

1. Dans la liste **Nom du schéma**, sélectionnez le schéma qui se trouve dans votre compte d’intégration lié à utiliser pour le décodage, par exemple :

   ![Capture d’écran montrant le concepteur de consommation et la liste « Nom de schéma » ouverte avec le schéma sélectionné à utiliser pour le décodage.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-consumption.png)

   > [!NOTE]
   > Si aucun schéma n’apparaît dans la liste, votre compte d’intégration ne contient aucun fichier de schéma à utiliser pour le décodage. Chargez le schéma que vous voulez utiliser dans votre compte d’intégration.

1. Enregistrez votre flux de travail. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Pour tester votre workflow, envoyez une requête au point de terminaison HTTPS, qui apparaît dans la propriété **HTTP POST URL** du déclencheur de requête et ajoutez le contenu XML que vous souhaitez décoder dans le corps de la requête.

Vous avez maintenant terminé la configuration de votre action de décodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données décodées dans une application métier telle que Salesforce. Vous pouvez également envoyer les données décodées à une partie. Pour envoyer la sortie de l’action de décodage à Salesforce ou à votre partie, utilisez les autres [connecteurs disponibles dans Azure Logic Apps](../connectors/apis-list.md).

### <a name="standard"></a>[Standard](#tab/standard)

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le flux de travail de votre application logique dans le concepteur.

1. Si vous disposez d’un workflow vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Sinon, passez à l’étape suivante.

   Cet exemple utilise le déclencheur de requête, appelé **Réception d’une requête HTTP**, et gère les requêtes entrantes provenant de l’extérieur du workflow de l’application logique. Pour ajouter le déclencheur de requête, procédez comme suit :

   1. Dans le concepteur, sélectionnez **Choisir une opération**. Une fois le volet **Choisir une opération** ouvert, dans la zone de recherche, sélectionnez **Intégrée**.

   1. Dans la zone de recherche, entrez `HTTP request`. Dans la liste des déclencheurs, sélectionnez le déclencheur de requête appelé **Réception d’une requête HTTP**.

     > [!TIP]
     > Le schéma JSON est facultatif. Si vous avez un exemple de charge utile de la requête entrante, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**, entrez l’exemple de charge utile, puis sélectionnez **Terminé**. Le schéma figure dans la zone **Schéma JSON du corps de la requête**.

1. Dans l’étape de votre workflow où vous souhaitez ajouter l’action **Décodage de fichier plat**, choisissez l’une des options suivantes :

   * Pour ajouter l’action **Décodage de fichier plat** à la fin de votre workflow, sélectionnez le signe moins ( **+** ), puis **Ajouter une action**.

   * Pour ajouter l’action **Décodage de fichier plat** entre des étapes existantes, sélectionnez le signe plus ( **+** ) entre celles-ci, puis sélectionnez **Ajouter une nouvelle étape**.

1. Une fois le volet **Choisir une opération** ouvert, dans la zone de recherche, sélectionnez **Intégrée**.

1. Dans la zone de recherche, entrez `flat file`. Dans la liste des actions, sélectionnez l’action appelée **Décodage de fichier plat**.

   ![Capture d’écran montrant le Portail Azure et le concepteur de workflow standard avec « fichier plat » dans la zone de recherche et l’action « Décodage de fichier plat » sélectionnée.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-standard.png)

1. Cliquez dans la zone **Contenu** pour afficher la liste du contenu dynamique. Dans la liste, dans la section **Réception d’une requête HTTP**, sélectionnez la propriété **Corps**, qui contient la sortie du corps de la requête du déclencheur et le contenu à décoder.

   ![Capture d’écran montrant le concepteur de workflow standard et la propriété « Contenu » avec la liste de contenu dynamique et le contenu sélectionné pour le décodage.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-standard.png)

   > [!TIP]
   > Si la propriété **Corps** n’apparaît pas dans la liste de contenu dynamique, sélectionnez **Voir plus** en regard de l’étiquette de section **Réception d’une requête HTTP**.
   > Vous pouvez également entrer directement le contenu à décoder dans la zone **Contenu**.

1. Dans la liste **Nom**, sélectionnez le schéma que vous avez précédemment téléchargé vers votre ressource d’application logique pour le décodage, par exemple :

   ![Capture d’écran montrant le concepteur de workflow standard et la liste « Nom » ouverte avec le schéma sélectionné à utiliser pour le décodage.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-standard.png)

   > [!NOTE]
   > Si aucun schéma n’apparaît dans la liste, votre ressource d’application logique standard ne contient aucun fichier de schéma à utiliser pour le décodage. Découvrez comment [charger le schéma que vous souhaitez utiliser dans votre ressource d’application logique standard](logic-apps-enterprise-integration-schemas.md).

1. Enregistrez votre flux de travail. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

1. Pour tester votre workflow, envoyez une requête au point de terminaison HTTPS, qui apparaît dans la propriété **HTTP POST URL** du déclencheur de requête et ajoutez le contenu XML que vous souhaitez décoder dans le corps de la requête.

Vous avez maintenant terminé la configuration de votre action de décodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données décodées dans une application métier telle que Salesforce. Vous pouvez également envoyer les données décodées à une partie. Pour envoyer la sortie de l’action de décodage à Salesforce ou à votre partie, utilisez les autres [connecteurs disponibles dans Azure Logic Apps](../connectors/apis-list.md).

---

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
