---
title: Utiliser des playbooks avec des règles d’automatisation dans Microsoft Sentinel
description: Utilisez ce tutoriel pour mieux appréhender l’utilisation de playbooks avec des règles d’automatisation dans Microsoft Sentinel afin d’automatiser votre réponse aux incidents et de corriger les menaces de sécurité.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8a1c290cebe566ec7d3a812de9746df8e0297120
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517745"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-microsoft-sentinel"></a>Tutoriel : Utiliser des playbooks avec des règles d’automatisation dans Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce tutoriel vous explique comment utiliser des playbooks avec des règles d’automatisation pour automatiser votre réponse aux incidents et corriger les menaces de sécurité détectées par Microsoft Sentinel. Grâce à ce didacticiel, vous pourrez :

> [!div class="checklist"]
>
> * Créer une tâche d'automatisation
> * Créer un playbook
> * Ajouter des actions à un playbook
> * Joindre un playbook à une règle d’automatisation ou une règle d’analyse pour automatiser la réponse aux menaces

> [!NOTE]
> Ce tutoriel fournit des conseils de base pour une principale tâche client : la création d’une automatisation pour le triage des incidents. Pour plus d’informations, consultez la section **Guide pratique**, comme [Automatiser la réponse aux menaces à l’aide de playbooks dans Microsoft Sentinel](automate-responses-with-playbooks.md) et [Utilisation de déclencheurs et d’actions dans les playbooks Microsoft Sentinel](playbook-triggers-actions.md).
>

## <a name="what-are-automation-rules-and-playbooks"></a>Que sont les règles d’automatisation et les playbooks ?

Les règles d’automatisation vous aident à trier les incidents dans Microsoft Sentinel. Vous pouvez les utiliser pour attribuer automatiquement des incidents aux personnes appropriées, fermer des incidents indésirables ou des [faux positifs](false-positives.md) connus, modifier leur gravité et ajouter des balises. Ils sont également le mécanisme par lequel vous pouvez exécuter des règles en réponse aux incidents.

Un playbook est une collection de procédures qui peut être exécutée à partir de Microsoft Sentinel en réponse à une alerte ou à un incident. Un playbook peut vous aider à automatiser et à orchestrer votre réponse, et peut être configuré pour s’exécuter automatiquement lorsque des alertes ou des incidents spécifiques sont générés, en les joignant à une règle d’analyse ou à une règle d’automatisation. Il peut également être exécuté manuellement à la demande.

Les playbooks dans Microsoft Sentinel reposent sur des workflows conçus dans [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Cela signifie que vous bénéficiez de la puissance, des possibilités de personnalisation et des modèles intégrés de Logic Apps. Chaque playbook est créé pour l’abonnement spécifique auquel il appartient, mais l’affichage des **règles** reprend toutes les règles disponibles dans les abonnements sélectionnés.

> [!NOTE]
> Étant donné que les règles utilisent des Azure Logic Apps, des frais supplémentaires peuvent s’appliquer. Consultez la page sur la tarification d’[Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) pour en savoir plus.

Par exemple, si vous souhaitez empêcher les utilisateurs potentiellement compromis de se déplacer dans votre réseau et de voler des informations, vous pouvez créer une réponse automatisée avec de nombreuses facettes pour les incidents générés par les règles détectant les utilisateurs compromis. Commencez par créer un playbook qui effectue les actions suivantes :

1. Lorsque le playbook est appelé par une règle d’automatisation lui transmettant un incident, le playbook ouvre un ticket dans [ServiceNow](/connectors/service-now/) ou tout autre système de création de tickets IT.

1. Il envoie un message à votre canal d’opérations de sécurité dans [Microsoft Teams](/connectors/teams/) ou [Slack](/connectors/slack/) pour vous assurer que vos analystes de sécurité sont conscients de l’incident.

1. Il envoie également toutes les informations de l’incident par e-mail à vos administrateur réseau et administrateur de sécurité supérieurs. L’e-mail comprendra les boutons d’option utilisateur **Bloquer** et **Ignorer**.

1. Le playbook attend jusqu’à ce qu’une réponse soit reçue de la part des administrateurs, puis continue avec les étapes suivantes.

1. Si les administrateurs choisissent **Bloc**, le playbook envoie une commande à Azure AD pour désactiver l’utilisateur, et une autre au pare-feu pour bloquer l’adresse IP.

1. Si les administrateurs choisissent **Ignorer**, le playbook ferme l’incident dans Microsoft Sentinel et le ticket dans ServiceNow.

Pour déclencher le playbook, vous allez créer une règle d’automatisation qui s’exécute lors de la génération de ces incidents. Cette règle effectue les étapes suivantes :

1. La règle modifie l’état de l’incident en **Actif**.

1. Il attribue l’incident à l’analyste chargé de gérer ce type d’incident.

1. Il ajoute la balise « utilisateur compromis ».

1. Enfin, il appelle le playbook que vous venez de créer. ([Des autorisations spéciales sont requises pour cette étape](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Les règles peuvent être exécutées automatiquement en réponse aux incidents, en créant des règles d’automatisation qui appellent les règles en tant qu’actions, comme dans l’exemple ci-dessus. Elles peuvent également être exécutées automatiquement en réponse aux alertes, en demandant à la règle d’analyse d’exécuter automatiquement un ou plusieurs playbooks lorsque l’alerte est générée. 

Vous pouvez également choisir d’exécuter manuellement un playbook à la demande, en réponse à une alerte sélectionnée.

Profitez d’une présentation plus complète et détaillée de l’automatisation de la réponse aux menaces à l’aide de [règles d’automatisation](automate-incident-handling-with-automation-rules.md) et de [playbooks](automate-responses-with-playbooks.md) dans Microsoft Sentinel.

> [!IMPORTANT]
>
> - Les **règles d’automatisation** et l’utilisation du **déclencheur d’incident** pour les règles sont actuellement en **version préliminaire**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="create-a-playbook"></a>Créer un playbook

Pour créer un playbook dans Microsoft Sentinel, effectuez les étapes suivantes :

### <a name="prepare-the-playbook-and-logic-app"></a>Préparer le playbook et l’application logique

1. Dans le menu de navigation de **Microsoft Sentinel**, sélectionnez **Automatisation**.

1. Dans le menu supérieur, sélectionnez **Créer** et **Ajouter un nouveau playbook**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Ajouter un nouveau playbook":::

    Un nouvel onglet de navigateur s’ouvre et vous permet d’accéder à l’Assistant **Créer une application logique**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Créer une application logique":::

1. Entrez votre **abonnement** et votre **groupe de ressources**, et donnez un nom à votre playbook sous **Nom de l’application logique**.

1. Pour **Région**, sélectionnez la région Azure dans laquelle les informations de votre application logique doivent être stockées.

1. Si vous souhaitez surveiller l’activité de ce playbook à des fins de diagnostic, activez la case à cocher **Activer Log Analytics**, puis entrez le nom de votre **espace de travail Log Analytics**.

1. Si vous souhaitez appliquer des balises à votre playbook, cliquez sur **Suivant : Balises >** (non connecté aux balises appliquées par les règles d’automatisation. [En savoir plus sur les balises](../azure-resource-manager/management/tag-resources.md)). Sinon, cliquez sur **Vérifier + créer**. Vérifiez les détails que vous avez fournis, puis cliquez sur **Créer**.

1. Pendant la création et le déploiement de votre playbook (cette opération prend quelques minutes), vous êtes dirigé vers un écran appelé **Microsoft. EmptyWorkflow**. Lorsque le message « votre déploiement est terminé » s’affiche, cliquez sur **Accéder à la ressource.**

1. Vous serez redirigé vers le [Concepteur de Logic Apps](../logic-apps/logic-apps-overview.md)du nouveau playbook, où vous pourrez commencer à concevoir le flux de travail. Vous verrez un écran avec une brève vidéo d’introduction, ainsi que des modèles et des déclencheurs d’applications logiques couramment utilisés. [Découvrez-en plus](../logic-apps/logic-apps-create-logic-apps-from-templates.md) sur la création d’un playbook avec Logic Apps.

1. Sélectionnez le modèle **Application logique vide**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galerie de modèles pour concepteur Logic Apps":::

### <a name="choose-the-trigger"></a>Choisir le déclencheur

Chaque playbook doit démarrer avec un déclencheur. Le déclencheur définit l’action qui va démarrer le playbook et le schéma que le playbook s’attend à recevoir.

1. Dans la barre de recherche, recherchez Microsoft Sentinel. Sélectionnez **Microsoft Sentinel** dans les résultats.

1. Sous l’onglet **Déclencheurs** obtenu, vous verrez les deux déclencheurs proposés par Microsoft Sentinel :
    - Quand une réponse à une alerte Microsoft Sentinel est déclenchée
    - Quand la règle de création d’incident Microsoft Sentinel a été déclenchée

   Choisissez le déclencheur qui correspond au type de playbook que vous êtes en train de créer.

    > [!NOTE]
    > N’oubliez pas que seuls les playbooks basés sur le **déclencheur d’incident** peuvent être appelés par des règles d’automatisation. Les playbooks basés sur le **déclencheur d’alerte** doivent être définis pour s’exécuter directement dans les [règles analytiques](detect-threats-custom.md#set-automated-responses-and-create-the-rule). Ils peuvent également être exécutés manuellement.
    > 
    > Pour plus d’informations sur le déclencheur à utiliser, consultez [**Utiliser des déclencheurs et actions dans les playbooks Microsoft Sentinel**](playbook-triggers-actions.md)

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Choisir un déclencheur pour votre playbook":::

> [!NOTE]
> Quand vous choisissez un déclencheur ou une action ultérieure, vous êtes invité à vous authentifier auprès du fournisseur de ressources avec lequel vous interagissez. Dans ce cas, le fournisseur est Microsoft Sentinel. Il existe plusieurs approches pour l’authentification. Pour obtenir des détails et des instructions, consultez [**Authentifier les playbooks auprès de Microsoft Sentinel**](authenticate-playbooks-to-sentinel.md).

### <a name="add-actions"></a>Ajouter des actions

Maintenant, vous pouvez définir l’action effectuée lorsque vous appelez le playbook. Vous pouvez ajouter des actions, des conditions logiques, des boucles ou des conditions de cas de basculement en sélectionnant **Nouvelle étape**. Cette sélection ouvre un nouveau cadre dans le concepteur, dans lequel vous pouvez choisir un système ou une application avec lequel interagir ou une condition à définir. Entrez le nom du système ou de l’application dans la barre de recherche en haut du cadre, puis choisissez parmi les résultats disponibles.

Dans chacune de ces étapes, un clic sur un champ permet d’afficher un panneau avec deux menus : le **contenu dynamique** et l'**expression**. Dans le menu **Contenu dynamique**, vous pouvez ajouter des références aux attributs de l’alerte ou de l’incident qui a été transmis au playbook, y compris les valeurs et attributs de toutes les entités impliquées. Dans le menu **Expression**, vous pouvez choisir parmi une grande bibliothèque de fonctions pour ajouter une logique supplémentaire à vos étapes.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Concepteur d’application logique":::

Cette capture d’écran montre les actions et les conditions que vous ajouteriez pour créer le playbook décrit dans l’exemple au début de ce document. La seule différence est que dans le playbook présenté ici, vous utilisez le **déclencheur d’alerte** au lieu du **déclencheur d’incident**. Cela signifie que vous appellerez ce playbook directement à partir d’une règle d’analyse, et non à partir d’une règle d’automatisation. Les deux méthodes d’appel d’un playbook sont décrites ci-dessous.

## <a name="automate-threat-responses"></a>Automatiser les réponses aux menaces

Vous avez créé votre playbook et défini le déclencheur, défini les conditions et imposé les actions qu’il prendra et les sorties qu’il produira. À présent, vous devez déterminer les critères selon lesquels il s’exécutera et configurer le mécanisme d’automatisation qui l’exécutera lorsque ces critères seront satisfaits.

### <a name="respond-to-incidents"></a>Répondre aux incidents

Vous pouvez utiliser un playbook pour répondre à un **incident** en créant une [règle d’automatisation](automate-incident-handling-with-automation-rules.md) qui s’exécutera lorsque l’incident sera généré, et à son tour, il appellera le playbook.

Pour créer une tâche d'automatisation :

1. À partir du panneau **Automatisation** dans le menu de navigation Microsoft Sentinel, sélectionnez **Créer** dans le menu supérieur, puis **Ajouter une nouvelle règle**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Ajouter une nouvelle règle":::

1. Le panneau **Créer une règle d’automatisation** s’ouvre. Entrez un nom pour votre règle.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Créer une tâche d'automatisation":::

1. Si vous souhaitez que la règle d’automatisation prenne effet uniquement sur certaines règles d’analyse, spécifiez celles-ci en modifiant la condition de **Si nom de règle d’analyse**.

1. Ajoutez toutes les autres conditions dont l’activation de cette règle d’automatisation dépend. Cliquez sur **Ajouter une condition** et choisissez les conditions dans la liste déroulante. La liste des conditions est remplie par les champs des détails d’alerte et de l’identificateur d’entité.

1. Choisissez les actions que vous souhaitez que cette règle d’automatisation prenne. Les actions disponibles incluent **Attribuer un propriétaire**, **Modifier le statut**, **Modifier la sévérité**, **Ajouter des balises** et **Exécuter le playbook**. Vous pouvez ajouter autant d’actions que vous le souhaitez.

1. Si vous ajoutez une action **Exécuter le playbook**, vous serez invité à choisir dans la liste déroulante des règles disponibles. Seuls les playbooks qui commencent par le **déclencheur d’incident** peuvent être exécutés à partir de règles d’automatisation, de sorte qu’ils s’affichent uniquement dans la liste.<a name="permissions-to-run-playbooks"></a>

    > [!IMPORTANT]
    > Microsoft Sentinel doit recevoir des autorisations explicites pour pouvoir exécuter des playbooks à partir de règles d’automatisation. Si un playbook apparaît « grisé » dans la liste déroulante, cela signifie que Sentinel n’a pas d’autorisation sur le groupe de ressources de ce playbook. Cliquez sur le lien **Gérer les autorisations du playbook** pour affecter des autorisations.
    > Dans le volet **Gérer les autorisations** qui s’ouvre, activez les cases à cocher des groupes de ressources contenant les règles que vous souhaitez exécuter, puis cliquez sur **Appliquer**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Gérer les autorisations":::
    > - Vous devez disposer des autorisations de **propriétaire** sur n’importe quel groupe de ressources auquel vous souhaitez accorder des autorisations Microsoft Sentinel, et vous devez disposer du rôle **Contributeur Logic App** sur tout groupe de ressources contenant les règles que vous souhaitez exécuter.
    > - Dans un déploiement à plusieurs locataires, si le playbook que vous souhaitez exécuter se trouve dans un autre locataire, vous devez accorder à Microsoft Sentinel l’autorisation d’exécuter le playbook dans le client du playbook.
    >    1. Dans le menu de navigation Microsoft Sentinel du locataire des playbooks, sélectionnez **Paramètres**.
    >    1. Dans le panneau **Paramètres**, sélectionnez l’onglet **Paramètres**, puis l’expander **Autorisations du Playbook**.
    >    1. Cliquez sur le bouton **Configurer les autorisations** pour ouvrir le panneau **Gérer les autorisations** repris ci-dessus, puis continuez comme décrit ici.
    > - Si, dans un scénario **MSSP**, vous souhaitez [exécuter le playbook d’un locataire client](automate-incident-handling-with-automation-rules.md#permissions-in-a-multi-tenant-architecture) à partir d’une règle d’automatisation créée lors de la connexion au locataire du fournisseur de services, vous devez accorder à Microsoft Sentinel l’autorisation d’exécuter le playbook dans les **_deux clients_ *_. Pour le client _* customer**, suivez les instructions de déploiement de plusieurs locataires, énoncées dans le point précédent. Dans le locataire du **fournisseur de services** , vous devez ajouter l’application Azure Security Insights à votre modèle d’intégration Azure Lighthouse :
    >    1. Dans le portail Azure, accédez à **Azure Active Directory**.
    >    1. Cliquez sur **Applications d’entreprise**.
    >    1. Sélectionnez le **type d’application** et filtrez par **Applications Microsoft**.
    >    1. Dans la zone de recherche, tapez **Azure Security Insights**.
    >    1. Copiez le champ **ID de l’objet**. Vous devrez ajouter cette autorisation supplémentaire à votre délégation Azure Lighthouse existante.
    >
    >    Le rôle **Contributeur Automatisation Microsoft Sentinel** possède le GUID fixe `f4c81013-99ee-4d62-a7ee-b3f1f648599a`. Un exemple d’autorisation Azure Lighthouse devrait ressembler à ceci dans votre modèle de paramètres :
    >    
    >    ```json
    >    {
    >        "principalId": "<Enter the Azure Security Insights app Object ID>", 
    >        "roleDefinitionId": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
    >        "principalIdDisplayName": "Microsoft Sentinel Automation Contributors" 
    >    }
    >    ```

1. Définissez une date d’expiration pour votre règle d’automatisation si vous souhaitez qu’elle en ait une.

1. Entrez un nombre sous **Ordre** pour déterminer à quel endroit de la séquence de règles d’automatisation cette règle s’exécutera.

1. Cliquez sur **Appliquer**. Vous avez terminé !

[Découvrez d’autres façons](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) de créer des règles d’automatisation.

### <a name="respond-to-alerts"></a>Répondre aux alertes

Vous utilisez un playbook pour répondre à une **alerte** en créant une **règle d’analyse** ou en modifiant une règle existante, qui s’exécute lors de la génération de l’alerte et en sélectionnant votre playbook en tant que réponse automatisée dans l' [Assistant règle d’analyse](detect-threats-custom.md).

1. À partir du panneau **Analytics** dans le menu de navigation Microsoft Sentinel, sélectionnez la règle d’analyse pour laquelle vous souhaitez automatiser la réponse, puis cliquez sur **Modifier** dans le volet d’informations.

1. Sur la page **Assistant de règle automatique - Modifier la règle existante**, sélectionnez l’onglet **Réponse automatisée**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Onglet Réponse automatisée":::

1. Choisissez votre playbook dans la liste déroulante. Vous pouvez choisir plusieurs playbooks, mais seuls les playbooks utilisant le **déclencheur d’alerte** seront disponibles.

1. Dans l’onglet **Vérifier et créer**, sélectionnez **Enregistrer**.

## <a name="run-a-playbook-on-demand"></a>Exécuter un playbook à la demande

Vous pouvez aussi exécuter un playbook à la demande.

 > [!NOTE]
 > Seuls les playbooks utilisant le **déclencheur d’alerte** peuvent être exécutés à la demande.

Pour exécuter un playbook à la demande :

1. Dans la page **Incidents**, sélectionnez un incident, puis cliquez sur **Afficher les informations complètes**.

2. Sous l’onglet **Alertes**, cliquez sur l’alerte qui doit déclencher l’exécution du playbook, faites défiler vers la droite, cliquez sur **Afficher les playbooks**, puis sélectionnez un playbook à **exécuter** dans la liste des playbooks disponibles pour l’abonnement.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à utiliser des playbooks et des règles d’automatisation dans Microsoft Sentinel pour répondre aux menaces. 
- Découvrez comment [rechercher des menaces de façon proactive](hunting.md) à l’aide de Microsoft Sentinel.
