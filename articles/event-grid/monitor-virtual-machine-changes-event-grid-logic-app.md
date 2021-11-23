---
title: Superviser les modifications d’une machine virtuelle - Azure Event Grid et Azure Logic Apps
description: Rechercher les modifications dans des machines virtuelles à l’aide d’Azure Event Grid et d’Azure Logic Apps
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, azla
ms.topic: tutorial
ms.date: 07/01/2021
ms.openlocfilehash: b1ad7f8af9233aeb49d38d4af93f43dcd7b04566
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520234"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Tutoriel : Superviser les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps

Pour superviser des événements spécifiques qui se produisent dans des ressources Azure ou des ressources tierces et y répondre, vous pouvez créer un [workflow d’application logique](../logic-apps/logic-apps-overview.md) automatisé avec très peu de code en utilisant Azure Logic Apps. Vous pouvez faire en sorte que ces ressources publient des événements dans une [grille d’événements Azure](../event-grid/overview.md). À son tour, la grille d’événements envoie ces événements aux abonnés qui possèdent des files d’attente, webhooks ou [hubs d’événements](../event-hubs/event-hubs-about.md) comme points de terminaison. En tant qu’abonné, votre workflow attend que ces événements arrivent dans la grille d’événements avant d’exécuter les étapes de traitement des événements.

Par exemple, voici certains événements que les éditeurs peuvent envoyer aux abonnés via le service Azure Event Grid :

* Créer, lire, mettre à jour ou supprimer une ressource. Par exemple, vous pouvez surveiller les modifications susceptibles d’être facturées sur votre abonnement Azure.

* Ajouter ou retirer une personne d’un abonnement Azure.

* Votre application effectue une action particulière.

* Un nouveau message apparaît dans une file d’attente.

Ce tutoriel montre comment créer une ressource d’application logique qui s’exécute dans [Azure Logic Apps *multilocataire*](../logic-apps/logic-apps-overview.md) et qui est basée sur le [modèle tarifaire Consommation](../logic-apps/logic-apps-pricing.md#consumption-pricing). Avec cette ressource d’application logique, vous créez un workflow qui supervise les modifications apportées à une machine virtuelle et envoie des e-mails sur ces modifications. Si vous créez un workflow qui a un abonnement aux événements auprès d’une ressource Azure, ces événements sont transférés de cette ressource vers le workflow par le biais d’une grille d’événements. Pour plus d’informations sur Azure Logic Apps multilocataire par rapport à Azure Logic Apps monolocataire, passez en revue les [environnements monolocataires ou multilocataires et les services d’intégration](../logic-apps/single-tenant-overview-compare.md).

![Capture d’écran montrant le concepteur de workflow avec un workflow qui supervise une machine virtuelle en utilisant Azure Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une ressource d’application logique et un workflow qui supervise les événements d’une grille d’événements ;
> * ajouter une condition qui recherche spécifiquement les modifications apportées à la machine virtuelle ;
> * envoyer un e-mail en cas de modification de la machine virtuelle.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un compte e-mail d’un service de messagerie qui utilise Azure Logic Apps, par exemple Outlook Office 365, Outlook.com ou Gmail, pour envoyer les notifications. Pour les autres fournisseurs, [passez en revue la liste des connecteurs ici](/connectors/).

  Ce tutoriel utilise un compte Outlook Office 365. Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais l’affichage de l’interface utilisateur peut être légèrement différent.

  > [!IMPORTANT]
  > Si vous souhaitez utiliser le connecteur Gmail, seuls les comptes professionnels G-Suite peuvent utiliser ce connecteur sans restriction dans Logic Apps. Si vous disposez d’un compte de consommateur Gmail, vous pouvez utiliser ce connecteur uniquement avec certains services approuvés par Google, ou vous pouvez [créer une application cliente Google pour servir lors de l’authentification avec votre connecteur Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Pour plus d’informations, consultez [Stratégies de confidentialité et de sécurité des données pour les connecteurs Google dans Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Une [machine virtuelle](https://azure.microsoft.com/services/virtual-machines) qui est seule dans son propre groupe de ressources Azure. Si ce n’est pas déjà fait, créez une machine virtuelle en suivant le [tutoriel Créer une machine virtuelle](../virtual-machines/windows/quick-create-portal.md). Pour que la machine virtuelle publie des événements, [aucun autre élément n’est nécessaire](../event-grid/overview.md).

* Si vous avez un pare-feu qui limite le trafic vers des adresses IP spécifiques, configurez-le pour autoriser l’accès à la fois aux adresses IP [entrantes](../logic-apps/logic-apps-limits-and-config.md#inbound) et [sortantes](../logic-apps/logic-apps-limits-and-config.md#outbound) utilisées par Azure Logic Apps dans la région Azure où vous créez votre workflow d’application logique.

  Cet exemple utilise des connecteurs managés qui exigent que votre pare-feu autorise l’accès pour *toutes* les [adresses IP sortantes du connecteur managé](/connectors/common/outbound-ip-addresses) dans la région Azure de votre ressource d’application logique.

## <a name="create-logic-app-resource"></a>Créer la ressource d’application logique

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

   ![Capture d’écran du portail Azure, montrant le bouton permettant de créer une ressource d’application logique.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Sous **Application logique**, fournissez des informations sur votre ressource d’application logique. Sélectionnez **Créer** lorsque vous avez terminé.

   ![Capture d’écran du menu de création d’applications logiques, montrant des détails tels que le nom, l’abonnement, le groupe de ressources et l’emplacement.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriété | Obligatoire | Valeur | Description |
   |----------|----------|-------|-------------|
   | **Nom** | Oui | <*logic-app-name*> | Fournissez un nom unique pour votre application logique. |
   | **Abonnement** | Oui | <*Azure-subscription-name*> | Sélectionnez le même abonnement Azure pour tous les services de ce tutoriel. |
   | **Groupe de ressources** | Oui | <*Azure-resource-group*> | Nom du groupe de ressources Azure pour votre application logique, que vous pouvez sélectionner pour tous les services de ce tutoriel. |
   | **Lieu** | Oui | <*Azure-region*> | Sélectionnez la même région pour tous les services de ce didacticiel. |
   |||

1. Une fois qu’Azure a déployé votre application logique, le concepteur de workflow affiche une page contenant une vidéo de présentation et les déclencheurs couramment utilisés. Faites défiler la vidéo et les déclencheurs.

1. Sous **Modèles**, sélectionnez **Application logique vide**.

   ![Capture d’écran de modèles Logic Apps, montrant la sélection permettant de créer une application logique vide.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Le concepteur de workflow affiche ensuite les [*déclencheurs*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que vous pouvez utiliser pour démarrer votre application logique. Chaque application logique doit démarrer avec un déclencheur, qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur est activé, Azure Logic Apps crée une instance de workflow qui exécute votre application logique.

## <a name="add-an-event-grid-trigger"></a>Ajouter un déclencheur Event Grid

Maintenant, ajoutez le déclencheur Event Grid qui permet de superviser le groupe de ressources de votre machine virtuelle.

1. Dans la zone de recherche du concepteur, entrez `event grid`. Dans la liste des déclencheurs, sélectionnez le déclencheur **Quand un événement de ressource se produit**.

   ![Capture d’écran montrant le concepteur de workflow avec le déclencheur Event Grid sélectionné.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Lorsque vous y êtes invité, connectez-vous à Azure Event Grid avec les informations d’identification de votre compte Azure. Dans la liste **Locataire**, qui indique le locataire Azure Active Directory associé à votre abonnement Azure, vérifiez que le locataire approprié s’affiche, par exemple :

   ![Capture d’écran montrant le concepteur de workflow avec l’invite de connexion Azure pour se connecter à Event Grid.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Si vous êtes connecté avec un compte Microsoft personnel, par exemple @outlook.com ou @hotmail.com, le déclencheur Event Grid risque de ne pas apparaître correctement. Il existe une solution de contournement qui consiste à sélectionner [Se connecter avec le principal de service](../active-directory/develop/howto-create-service-principal-portal.md) ou à s’authentifier en tant que membre de l’instance Azure Active Directory associée à l’abonnement Azure, par exemple *nom-utilisateur*@emailoutlook.onmicrosoft.com.

1. Maintenant, abonnez votre application logique aux événements de l’éditeur. Renseignez les détails de votre abonnement aux événements, comme décrit dans le tableau suivant, par exemple :

   ![Capture d’écran montrant le concepteur de workflow avec l’éditeur des détails de déclencheur ouvert.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Propriété | Obligatoire | Valeur | Description |
   | -------- | -------- | ----- | ----------- |
   | **Abonnement** | Oui | <*event-publisher-Azure-subscription-name*> | Sélectionnez le nom de l’abonnement Azure associé à l’*éditeur d’événements*. Dans le cadre de ce tutoriel, sélectionnez le nom d’abonnement Azure de votre machine virtuelle. |
   | **Type de ressource** | Oui | <*event-publisher-Azure-resource-type*> | Sélectionnez le type de ressource Azure pour l’éditeur d’événements. Pour plus d’informations sur les types de ressources Azure, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md). Pour ce tutoriel, sélectionnez la valeur `Microsoft.Resources.ResourceGroups` pour superviser les groupes de ressources Azure. |
   | **Nom de la ressource** |  Oui | <*event-publisher-Azure-resource-name*> | Sélectionnez le nom de la ressource Azure pour l’éditeur d’événements. Cette liste varie en fonction du type de ressource que vous avez sélectionné. Pour ce tutoriel, sélectionnez le nom du groupe de ressources Azure qui contient votre machine virtuelle. |
   | **Élément de type d’événement** |  Non | <*event-types*> | Sélectionnez un ou plusieurs types d’événements spécifiques à filtrer et envoyer à votre Event Grid. Par exemple, vous pouvez ajouter ces types d’événements pour détecter le moment où des ressources sont modifiées ou supprimées : <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Pour plus d’informations, consultez les rubriques suivantes : <p><p>- [Schéma d’événements Azure Event Grid pour les groupes de ressources](../event-grid/event-schema-resource-groups.md) <br>- [Comprendre le filtrage des événements](../event-grid/event-filtering.md) <br>- [Filtrer des événements pour Event Grid](../event-grid/how-to-filter-events.md) |
   | Pour ajouter des propriétés facultatives, sélectionnez **Ajouter un nouveau paramètre**, puis les propriétés souhaitées. | Non | {voir les descriptions} | * **Filtre de préfixe** : Pour ce tutoriel, laissez cette propriété vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de préfixe en tant que filtre, par exemple, un chemin d’accès et un paramètre pour une ressource spécifique. <p>* **Filtre de suffixe** : Pour ce tutoriel, laissez cette propriété vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de suffixe en tant que filtre, par exemple, une extension de nom de fichier, si vous ne souhaitez utiliser que des types de fichiers spécifiques. <p>* **Nom d’abonnement** : Pour ce tutoriel, vous pouvez indiquer un nom unique pour votre abonnement aux événements. |
   |||

1. Enregistrez votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**. Pour réduire et masquer les détails d’une action dans votre application logique, sélectionnez la barre de titre de l’action.

   ![Capture d’écran montrant le concepteur de workflow et le bouton « Enregistrer » sélectionné.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Lorsque vous enregistrez votre application logique avec un déclencheur Event Grid, Azure crée automatiquement un abonnement aux événements de votre application logique pour la ressource que vous avez sélectionnée. Par conséquent, lorsque la ressource publie un événement dans la grille d’événements, cette grille d’événements transmet automatiquement l’événement à votre application logique. Cet événement déclenche votre application logique, puis crée et lance une instance du flux de travail que vous définissez dans les étapes suivantes.

Votre application logique est désormais en ligne ; elle écoute les événements de la grille d’événements, mais ne fait rien tant que vous n’avez pas ajouté d’actions au flux de travail.

## <a name="add-a-condition"></a>Ajouter une condition

Si vous souhaitez que votre application logique s’exécute uniquement quand un événement ou une opération spécifique se produit, ajoutez une condition qui recherche l’opération `Microsoft.Compute/virtualMachines/write`. Lorsque cette condition est true, votre application logique vous envoie par courrier électronique des informations sur la machine virtuelle mise à jour.

1. Dans le Concepteur Logic Apps, sous le déclencheur Event Grid, sélectionnez **Nouvelle étape**.

   ![Capture d’écran montrant le concepteur de workflow avec l’option « Nouvelle étape » sélectionnée.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Sous **Choisir une action**, dans la zone de recherche, entrez `condition` en guise de filtre. Dans la liste des actions, sélectionnez l’action **Condition**.

   ![Capture d’écran montrant le concepteur de workflow avec l’option « Condition » sélectionnée.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Le Concepteur d’application logique ajoute une condition vide à votre flux de travail, y compris les chemins d’accès des actions à suivre selon que la condition est true ou false.

   ![Capture d’écran montrant le concepteur de workflow avec une condition vide ajoutée au workflow.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Renommez le titre de la condition comme suit : `If a virtual machine in your resource group has changed`. Dans la barre de titre de la condition, sélectionnez le bouton représentant des points de suspension ( **...** ), puis **Renommer**.

   ![Capture d’écran montrant le concepteur de workflow avec le menu contextuel de l’éditeur de la condition et l’option « Renommer » sélectionnés.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Créez une condition qui vérifie dans l’événement `body` un objet `data` dont la propriété `operationName` correspond à l’opération `Microsoft.Compute/virtualMachines/write`. En savoir plus sur le [schéma d’un événement Event Grid](../event-grid/event-schema.md).

   1. Sur la première ligne, sous **et**, cliquez à l’intérieur de la zone de gauche. Dans la liste de contenu dynamique qui s’affiche, sélectionnez **Expression**.

      ![Capture d’écran du concepteur de workflow montrant l’action de la condition et dans laquelle la liste de contenu dynamique est ouverte avec l’option « Expression » sélectionnée.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. Dans l’éditeur d’expressions, entrez cette expression, qui retourne le nom de l’opération à partir du déclencheur, puis sélectionnez **OK** :

      `triggerBody()?['data']['operationName']`

      Par exemple :

      ![Capture d’écran du Concepteur Logic Apps, montrant l’éditeur de condition avec une expression pour extraire le nom de l’opération.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Dans la zone du milieu, conservez l’opérateur **est égal à**.

   1. Dans la zone de droite, entrez l’opération que vous souhaitez superviser, qui est la valeur suivante pour cet exemple :

      `Microsoft.Compute/virtualMachines/write`

   Votre condition terminée ressemble maintenant à l’exemple suivant :

   ![Capture d’écran montrant le concepteur de workflow avec une condition qui compare l’opération.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Si vous passez du mode Création au mode Code puis à nouveau au mode Création, l’expression que vous avez spécifiée dans la condition est résolue en jeton **data.operationName** :

   ![Capture d’écran montrant le concepteur de workflow avec une condition qui a résolu les jetons.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Enregistrez votre application logique.

## <a name="send-email-notifications"></a>Envoyer des notifications par e-mail

Ajoutez maintenant une [*action*](../logic-apps/logic-apps-overview.md#logic-app-concepts) pour pouvoir recevoir un e-mail quand la condition spécifiée a la valeur true.

1. Dans la zone **Si true** de la condition, sélectionnez **Ajouter une action**.

   ![Capture d’écran montrant le concepteur de workflow avec le volet « Si true » de la condition ouvert et l’option « Ajouter une action » sélectionnée.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Sous **Choisir une action**, dans la zone de recherche, entrez `send an email` en guise de filtre. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Puis sélectionnez l’action « Envoyer un courrier électronique » pour votre connecteur. Par exemple :

   * Pour un compte professionnel ou scolaire Azure, sélectionnez le connecteur Outlook Office 365.

   * Pour des comptes personnels Microsoft, sélectionnez le connecteur Outlook.com.

   * Pour des comptes Gmail, sélectionnez le connecteur Gmail.

   Ce tutoriel continue d’utiliser le connecteur Outlook Office 365. Si vous utilisez un autre fournisseur, les étapes restent les mêmes, mais votre interface utilisateur peut s’afficher un peu différemment.

   ![Capture d’écran montrant le concepteur de workflow avec la zone de recherche ouverte pour trouver l’action « Envoyer un e-mail ».](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Si vous ne disposez pas déjà d’une connexion pour votre fournisseur de messagerie, connectez-vous à votre compte de messagerie lorsque vous êtes invité vous identifier.

1. Renommez le titre de l’action Envoyer un e-mail comme suit : `Send email when virtual machine updated`

1. Fournissez des informations sur l’e-mail comme l’indique le tableau suivant :

   ![Capture d’écran montrant le concepteur de workflow avec un contenu dynamique ajouté à la ligne d’objet de l’e-mail pour une condition ayant la valeur true.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Pour sélectionner une sortie à partir des étapes précédentes de votre workflow, cliquez dans une zone d’édition afin d’ouvrir la liste de contenu dynamique ou sélectionnez **Ajouter du contenu dynamique**. Pour voir davantage de résultats, sélectionnez **Plus** pour chaque section de la liste. Pour fermer la liste de contenu dynamique, sélectionnez de nouveau **Ajouter du contenu dynamique**.

   | Propriété | Obligatoire | Valeur | Description |
   | -------- | -------- | ----- | ----------- |
   | **To** | Oui | <*destinataire\@domaine*> | Entrez l’adresse e-mail du destinataire. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. |
   | **Subject** | Oui | `Resource updated:` **Objet** | Entrez le contenu de l’objet de l’e-mail. Dans le cadre de ce tutoriel, entrez le texte spécifié et sélectionnez le champ **Subject** de l’événement. Ici, l’objet de votre e-mail comprend le nom de la ressource mise à jour (machine virtuelle). |
   | **Corps** | Oui | `Resource:` **Sujet** <p>`Event type:` **Type d’événement**<p>`Event ID:` **ID**<p>`Time:` **Heure de l’événement** | Entrez le contenu du corps de l’e-mail. Dans le cadre de ce tutoriel, entrez le texte spécifié et sélectionnez les champs **Topic**, **Event Type**, **ID** et **Event Time** de l’événement de façon à inclure dans votre e-mail la ressource qui a déclenché l’événement, le type d’événement, son horodatage et son ID pour la mise à jour. Pour ce tutoriel, la ressource est le groupe de ressources Azure sélectionné dans le déclencheur. <p>Pour ajouter des lignes vides à votre contenu, appuyez sur Maj + Entrée. |
   ||||

   > [!NOTE]
   > Si vous sélectionnez un champ qui représente un tableau, le concepteur ajoute automatiquement une boucle **For each** autour de l’action qui référence ce tableau. De cette façon, votre application logique effectue cette action sur chaque élément du tableau.

   Votre action d’e-mail peut maintenant se présenter ainsi :

   ![Capture d’écran montrant le concepteur de workflow avec des sorties sélectionnées à envoyer par e-mail quand la machine virtuelle est mise à jour.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Et votre application logique terminée peut se présenter ainsi :

   ![Capture d’écran montrant le concepteur de workflow avec l’application logique terminée et les détails du déclencheur et des actions.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Enregistrez votre application logique. Pour réduire et masquer les détails de chaque action dans votre application logique, sélectionnez la barre de titre de l’action.

   Votre application logique est désormais en ligne, mais elle attend que votre machine virtuelle soit modifiée avant de faire quoi que ce soit. Pour tester maintenant votre application logique, passez à la section suivante.

## <a name="test-your-logic-app-workflow"></a>Tester le flux de travail d’une application logique

1. Pour vérifier que votre application logique reçoit des événements spécifiés, mettez à jour votre machine virtuelle.

   Par exemple, vous pouvez [redimensionner votre machine virtuelle](../virtual-machines/resize-vm.md).

   Après quelques instants, vous devriez recevoir un courrier électronique. Par exemple :

   ![Capture d’écran d’un exemple d’e-mail Outlook, montrant des détails sur une mise à jour de machine virtuelle.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Pour consulter l’historique des exécutions et des déclenchements de votre application logique, sélectionnez **Vue d’ensemble** dans le menu de votre application logique. Pour afficher plus de détails sur une exécution, sélectionnez la ligne correspondante.

   ![Capture d’écran de la page de vue d’ensemble d’une application logique, montrant une exécution réussie sélectionnée.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Pour afficher les entrées et les sorties de chaque étape, développez celle que vous souhaitez vérifier. Ces informations peuvent vous aider à diagnostiquer et déboguer les problèmes de votre application logique.

   ![Capture d’écran de l’historique des exécutions d’une application logique, montrant les détails de chaque exécution.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Félicitations, vous avez créé et exécuté une application logique qui surveille les événements liés aux ressources grâce à une grille d’événements et vous envoie un e-mail lorsque ces événements se produisent. Vous avez également appris comment créer facilement des flux de travaux qui automatisent les processus et intégrer des systèmes et des services cloud.

Vous pouvez surveiller les autres modifications de configuration avec des grilles d’événements et des applications logiques. Par exemple :

* Une machine virtuelle se voit attribuer des droits de contrôle d’accès en fonction du rôle Azure (Azure RBAC).
* Des modifications sont apportées à un groupe de sécurité réseau (NSG) sur une carte d’interface réseau (NIC).
* Des disques d’une machine virtuelle ont été ajoutés ou supprimés.
* Une adresse IP publique est affectée à la carte d’interface réseau (NIC) d’une machine virtuelle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Ce didacticiel utilise des ressources et effectue des actions qui peuvent entraîner des frais sur votre abonnement Azure. Par conséquent, lorsque vous aurez terminé de suivre ce didacticiel et d’effectuer les tests, veillez à désactiver ou à supprimer les ressources pour lesquelles vous ne souhaitez pas être facturé.

* Pour arrêter l’exécution de votre application logique sans supprimer votre travail, désactivez votre application. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**. Dans la barre d’outils, sélectionnez **Désactiver**.

  ![Capture d’écran de la vue d’ensemble d’une application logique, montrant le bouton Désactiver sélectionné pour désactiver l’application logique.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

* Pour supprimer définitivement votre application logique, sélectionnez **Vue d’ensemble** dans le menu de l’application logique. Dans la barre d’outils, sélectionnez **Supprimer**. Confirmez que vous souhaitez supprimer votre application logique, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et acheminer des événements personnalisés avec Event Grid](../event-grid/custom-event-quickstart.md)

Consultez les exemples suivants pour en savoir plus sur la publication d’événements sur Event Grid et leur consommation avec différents langages de programmation. 

- [Exemples Azure Event Grid pour .NET](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Exemples Azure Event Grid pour Java](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Exemples Azure Event Grid pour Python](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [Exemples Azure Event Grid pour JavaScript](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [Exemples Azure Event Grid pour TypeScript](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
