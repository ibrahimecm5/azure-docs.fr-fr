---
title: Valider des données XML dans des flux de travail d’intégration d’entreprise
description: Validez des documents XML à l’aide de schémas dans des flux de travail utilisant Azure Logic Apps et Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 20edb5f3b94e9014ce08d012cb0033a3bfb28db5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457465"
---
# <a name="validate-xml-in-workflows-with-azure-logic-apps"></a>Valider des données XML dans des flux de travail à l’aide d’Azure Logic Apps

Dans des scénarios B2B d’intégration d’entreprise, les partenaires commerciaux parties à un contrat doivent souvent s’assurer que les messages qu’ils échangent sont valides avant de commencer tout traitement de données. Votre flux de travail d’application logique peut valider les messages et documents XML à l’aide de l’action **Validation XML** et d’un [schéma](logic-apps-enterprise-integration-schemas.md) prédéfini.

Si vous débutez avec les applications logiques, voir [Qu’est-ce qu’Azure Logic Apps](logic-apps-overview.md) ? Pour plus d’informations sur l’intégration d’entreprise B2B, consultez [Workflows d’intégration d’entreprise B2B avec Azure Logic Apps et Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un flux de travail d’application logique, vide ou existant, dans lequel vous souhaitez utiliser l’action **Validation XML**.

  Si vous avez un flux de travail vide, utilisez un déclencheur de votre choix. Cet exemple utilise le déclencheur de demande.

* Une [ressource de compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) dans laquelle vous définissez et stockez les artefacts, comme les parties, les contrats, les certificats, etc., à utiliser dans vos flux de travail d’intégration d’entreprise et B2B. Cette ressource doit remplir les conditions suivantes :

  * Associé au même abonnement Azure que votre ressource d’application logique.

  * Existe au même emplacement ou dans la même région Azure que la ressource d’application logique dans laquelle vous envisagez d’utiliser l’action de **validation XML***.

  * Si vous utilisez le type de ressource [Application logique (consommation) **, vous devez disposer d’un** compte d’intégration](logic-apps-overview.md#resource-type-and-host-environment-differences) qui répond aux exigences suivantes :

    * Le [schéma](logic-apps-enterprise-integration-schemas.md) à utiliser pour valider le contenu XML

    * Un [lien vers votre ressource d'application logique](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Si vous utilisez le [type de ressource **Application logique (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), vous ne stockez pas de schéma dans votre compte d’intégration. Au lieu de cela, vous pouvez [directement ajouter des schémas à votre ressource d’application logique](logic-apps-enterprise-integration-schemas.md) à l’aide du portail Azure ou de Visual Studio Code. Vous pouvez ensuite utiliser ces schémas sur plusieurs flux de travail au sein de la *même ressource d’application logique*.

    Toutefois, vous avez toujours besoin de ce compte pour stocker les artefacts, tels que les partenaires, les contrats et les certificats, en plus d’utiliser les opérations [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) ou [EDIFACT](logic-apps-enterprise-integration-edifact.md). Toutefois, vous n’avez pas besoin de lier votre ressource d’application logique à votre compte d’intégration, donc la fonctionnalité de liaison n’existe pas. Votre compte d’intégration doit encore répondre à d’autres exigences, telles que l’utilisation du même abonnement Azure et le même emplacement que votre ressource d’application logique.

    > [!NOTE]
    > Actuellement, seul le type de ressource **Application logique (Consommation)** prend en charge les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Le type de ressource **Application logique (Standard)** n’inclut pas les opérations [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

## <a name="add-xml-validation-action"></a>Ajouter l’action de validation XML

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique et votre flux de travail en mode concepteur.

1. Si vous avez une application logique vide qui n’a pas de déclencheur, ajoutez un déclencheur de votre choix. Cet exemple utilise le déclencheur de demande. Sinon, passez à l’étape suivante.

   Pour ajouter le déclencheur de demande, dans la zone de recherche du concepteur, entrez `HTTP request`, puis sélectionnez le déclencheur de demande nommé **When an HTTP request is received** (Lors de la réception d’une requête HTTP).

1. Dans la phase de votre flux de travail où vous souhaitez ajouter l’action **Validation XML**, choisissez l’une des étapes suivantes :

   Pour une application logique basée sur un plan de consommation ou ISE, choisissez une étape :

   * Pour ajouter l’action **Validation XML** à la fin de votre flux de travail, sélectionnez **Nouvelle étape**.

   * Pour ajouter l’action **Validation XML** entre des étapes existantes, positionnez votre pointeur sur la flèche qui connecte ces étapes de façon à afficher le signe plus ( **+** ). Sélectionnez ce signe plus, puis sélectionnez **Ajouter une action**.

   Pour une application logique basée sur un plan standard, choisissez une étape :

   * Pour ajouter l’action **Validation XML** à la fin de votre flux de travail, sélectionnez le signe moins ( **+** ), puis **Ajouter une action**.

   * Pour ajouter l’action **Validation XML** entre des étapes existantes, sélectionnez le signe plus ( **+** ) entre celles-ci, puis sélectionnez **Ajouter une action**.

1. Sous **Choisir une opération**, sélectionnez **Intégré**. Dans la zone de recherche, entrez `xml validation`. Dans la liste des actions, sélectionnez **Validation XML**.

1. Pour spécifier le contenu XML à valider, cliquez dans la zone **Contenu** afin d’afficher la liste de contenu dynamique.

   La liste de contenu dynamique affiche des jetons de propriété qui représentent les sorties des étapes précédentes du workflow. Si la liste n’affiche pas de propriété attendue, vérifiez le titre du déclencheur ou de l’action dans la liste, et si vous pouvez sélectionner **Afficher plus**.

   Pour une application logique basée sur un plan de consommation ou ISE, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à plusieurs locataires avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Pour une application logique basée sur un plan standard, le concepteur ressemble à l’exemple suivant :

   ![Capture d’écran montrant un concepteur à un seul locataire avec le curseur dans la zone « Contenu » et une liste de contenu dynamique ouverte.](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. Dans la liste de contenu dynamique, sélectionnez le jeton de propriété pour le contenu que vous souhaitez valider.

   Cet exemple sélectionne le jeton **Corps** du déclencheur.

1. Pour spécifier le schéma à utiliser pour la validation, ouvrez la liste **Nom du schéma**, puis sélectionnez le schéma que vous avez ajouté précédemment.

1. Quand vous avez terminé, veillez à enregistrer votre flux de travail d’application logique.

   Vous avez maintenant terminé la configuration de votre action **Validation XML**. Dans une application réelle, vous souhaiterez peut-être stocker les données validées dans une application métier telle que Salesforce. Pour envoyer la sortie validée à Salesforce, ajoutez une action Salesforce.

1. Pour tester votre action de validation, déclenchez et exécutez votre flux de travail. Par exemple, pour le déclencheur de demande, envoyez une demande à l’URL de point de terminaison du déclencheur.

   L’action **Validation XML** s’exécute après le déclenchement de votre flux de travail et lorsque le contenu XML est disponible pour la validation.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des schémas pour la validation de code XML dans Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [Transformer du code XML pour des flux de travail dans Azure Logic Apps](logic-apps-enterprise-integration-transform.md)
