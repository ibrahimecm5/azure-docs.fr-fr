---
title: Automatisation des workflows dans Microsoft Defender pour le cloud | Microsoft Docs
description: Découvrez comment créer et automatiser des flux de travail dans Microsoft Defender pour le cloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 05/03/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4cf12721cc691f4719cc4442b4092b44f6d8e1b7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004565"
---
# <a name="automate-responses-to-microsoft-defender-for-cloud-triggers"></a>Automatiser les réponses aux déclencheurs Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Chaque programme de sécurité comprend plusieurs workflows pour la réponse aux incidents. Ces processus peuvent inclure l’envoi de notifications aux parties prenantes concernées, le lancement d’un processus de gestion des changements et l’application d’étapes de correction spécifiques. Les experts en sécurité vous conseillent d’automatiser le plus possible les étapes de ces processus. D’une part, l’automatisation réduit votre charge de travail. D’autre part, elle contribue à renforcer votre sécurité en garantissant que les étapes des processus sont effectuées rapidement, de manière cohérente et selon vos exigences prédéfinies.

Cet article décrit la fonctionnalité d’automatisation de workflow de Microsoft Defender pour le cloud. Cette fonctionnalité peut déclencher Logic Apps sur les alertes de sécurité, les recommandations et les modifications apportées à la conformité réglementaire. Par exemple, vous pouvez définir que Defender pour le cloud envoie un e-mail à un utilisateur donné quand une alerte se produit. Vous allez également apprendre à créer des applications logiques à l’aide du service [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Gratuit|
|Rôles et autorisations obligatoires :|**Rôle d’administrateur de sécurité** ou **Propriétaire** sur le groupe de ressources<br>Doit également disposer d’autorisations en écriture sur la ressource cible<br><br>Pour utiliser des workflows Azure Logic Apps, vous devez également disposer des autorisations/rôles Logic Apps suivants :<br> - Les autorisations [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) sont nécessaires pour lire/déclencher des applications logiques (ce rôle ne permet pas la création ni la modification d’applications logiques ; il sert uniquement à *exécuter* des applications existantes)<br> - Les autorisations [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) sont requises pour la création et la modification d’applications logiques<br>Si vous souhaitez utiliser des connecteurs d’applications logiques, vous pouvez avoir besoin d’informations d’identification supplémentaires pour vous connecter à leurs services respectifs (par exemple, vos instances Outlook/Teams/Slack).|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png":::National/Souverain (Azure Government, Azure China 21Vianet)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Créer une application logique et définir le moment de son exécution automatique 

1. Dans la barre latérale de Defender pour le cloud, sélectionnez **Automatisation du workflow**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Liste d’automatisations des workflows.":::

    À partir de cette page, vous pouvez créer des règles d’automatisation, et activer, désactiver ou supprimer des règles existantes.

1. Pour définir un nouveau workflow, cliquez sur **Add workflow automation** (Ajouter une automatisation de workflow). 

    Un volet s’affiche avec les options de votre nouvelle automatisation. Vous pouvez y entrer :
    1. Le nom et la description de l’automatisation.
    1. Les déclencheurs qui lanceront l’exécution de ce workflow automatique. Par exemple, vous pouvez définir que votre application logique s’exécute quand une alerte de sécurité contenant « SQL » est générée.

        > [!NOTE]
        > Si votre déclencheur est une recommandation qui contient des « sous-recommandations », par exemple **Les résultats de l’évaluation des vulnérabilités sur vos bases de données SQL doivent être corrigés**, l’application logique ne se déclenchera pas pour chaque nouvelle recherche de sécurité, mais uniquement lorsque l’état de la recommandation mère change.

    1. L’application logique à exécuter lorsque les conditions du déclencheur définies seront remplies. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Volet Ajouter des automatisations de workflow.":::

1. Dans la section Actions, sélectionnez **Accéder à la page Logic Apps** pour commencer le processus de création de l’application logique.

    Vous êtes redirigé vers le service Azure Logic Apps.

1. Sélectionnez **Ajouter**. 

    [![Création d’une nouvelle Application logique.](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Entrez un nom, un groupe de ressources et un emplacement, puis sélectionnez **Examiner et créer** > **Créer**.

    Le message **Le déploiement est en cours** s’affiche. Attendez que la notification de fin de déploiement s’affiche et sélectionnez **Accéder à la ressource** dans la notification.

1. Dans votre nouvelle application logique, vous pouvez choisir des modèles prédéfinis intégrés dans la catégorie Sécurité. Vous pouvez aussi définir un workflow personnalisé des événements attendus au déclenchement de ce processus.

    > [!TIP]
    > Parfois, dans une application logique, les paramètres sont inclus dans le connecteur dans le cadre d’une chaîne et non dans leur propre champ. Pour obtenir un exemple d’extraction de paramètres, consultez l’étape 14 de [Utilisation des paramètres de l’application logique lors de la génération d’automatisations des workflows Defender pour le cloud](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    Le concepteur d’applications logiques prend en charge les déclencheurs de protection pour le cloud :

    - **Quand une recommandation Microsoft Defender pour le cloud est créée ou déclenchée** : si votre application logique repose sur une recommandation qui est dépréciée ou remplacée, votre automatisation cesse de fonctionner et vous devez mettre à jour le déclencheur. Pour suivre les modifications apportées aux recommandations, utilisez les [notes de publication](release-notes.md).

    - **Quand une alerte Defender pour le cloud est créée ou déclenchée** : vous pouvez personnaliser le déclencheur pour qu’il ne concerne que les alertes dont les niveaux de gravité vous intéressent.
    
    - **Quand une évaluation Defender pour le cloud de la conformité réglementaire est créée ou déclenchée** : déclenchez des automatisations en fonction des mises à jour apportées aux évaluations de conformité réglementaire.

    > [!NOTE]
    > Si vous utilisez le déclencheur hérité « Quand une réponse à une alerte Azure Security Center est déclenchée », votre application logique n’est pas lancée par la fonctionnalité Automatisation des workflows. À la place, utilisez l’un des déclencheurs mentionnés ci-dessus. 

    [![Exemple d’application logique.](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Après avoir défini votre application logique, revenez au volet de définition de l’automatisation des workflows (« Ajouter une automatisation de workflow »). Cliquez sur **Actualiser** afin de rendre votre nouvelle application logique disponible pour la sélection.

    ![Actualiser :](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Sélectionnez votre application logique et enregistrez l’automatisation. Notez que la liste déroulante Application logique affiche uniquement les applications logiques disposant des connecteurs Defender pour le cloud pris en charge mentionnés ci-dessus.


## <a name="manually-trigger-a-logic-app"></a>Déclencher manuellement une application logique

Vous pouvez également exécuter des applications logiques manuellement quand une alerte ou recommandation de sécurité quelconques s’affichent.

Pour exécuter manuellement une application logique, ouvrez une alerte ou une recommandation, puis cliquez sur **Déclencher l’application logique** :

[![Déclencher manuellement une Application logique.](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Configurer l’automatisation des workflows à grande échelle à l’aide des stratégies fournies

L’automatisation des processus d’analyse et de réponse aux incidents de votre organisation peut réduire de manière significative le temps requis pour examiner et atténuer les incidents de sécurité.

Pour déployer vos configurations d’automatisation à l’échelle de votre organisation, utilisez les stratégies Azure Policy « DeployIfdNotExist » fournies qui sont décrites ci-dessous pour créer et configurer les procédures d’automatisation des workflows.

Prise en main des [modèles d’automatisation de flux de travail](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Pour implémenter ces stratégies :

1. Dans le tableau ci-dessous, sélectionnez la stratégie que vous souhaitez appliquer :

    |Objectif  |Policy  |ID de stratégie  |
    |---------|---------|---------|
    |Automatisation du flux de travail pour les alertes de sécurité              |[Déployer l’automatisation de workflow pour les alertes de Microsoft Defender pour le cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Automatisation du flux de travail pour les recommandations de sécurité     |[Déployer l’automatisation de workflow pour les recommandations de Microsoft Defender pour le cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Automatisation des workflows pour les modifications de conformité réglementaire|[Déployer l’automatisation de workflow pour la conformité réglementaire de Microsoft Defender pour le cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Vous pouvez également les trouver faisant une recherche dans Azure Policy :
    > 1. Ouvrez Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Accès à Azure Policy.":::
    > 2. Dans le menu Azure Policy, sélectionnez **Définitions** et recherchez-les par nom. 

1. Dans la page Azure Policy appropriée, sélectionnez **Attribuer**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Attribution d’Azure Policy.":::

1. Ouvrez chaque onglet et définissez les paramètres comme vous le souhaitez :
    1. Sous l’onglet **Général**, définissez l’étendue de la stratégie. Pour utiliser la gestion centralisée, attribuez la stratégie au groupe d’administration contenant les abonnements qui utiliseront la configuration de l’automatisation des workflows. 
    1. Dans l’onglet **Paramètres**, définissez les détails du groupe de ressources et du type de données. 
        > [!TIP]
        > Chaque paramètre est accompagné d’une info-bulle qui explique les options disponibles.
        >
        > L’onglet Paramètres d’Azure Policy (1) donne accès à des options de configuration similaires à celles de la page d’automatisation des workflows de Defender pour le cloud(2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Comparaison des paramètres de l’automatisation des workflows avec Azure Policy." lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Si vous le souhaitez, pour appliquer cette attribution à des abonnements existants, ouvrez l’onglet **Correction** et sélectionnez l’option permettant de créer une tâche de correction.

1. Consultez la page de résumé et sélectionnez **Créer**.


## <a name="data-types-schemas"></a>Schémas des types de données

Pour consulter les schémas d’événements bruts des alertes de sécurité ou les recommandations que les événements ont transmises à l’instance Logic App, consultez [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas). Cela peut être utile dans les cas où vous n’utilisez pas les connecteurs d’application logique intégrés à Defender pour le cloud mentionnés ci-dessus, mais plutôt le connecteur HTTP générique de l’application logique. Vous pouvez utiliser le schéma JSON d’événement pour l’analyser manuellement comme vous le souhaitez.


## <a name="faq---workflow-automation"></a>FAQ : Automatisation du workflow

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>L’automatisation des workflows prend-elle en charge les scénarios de continuité d'activité et reprise d'activité (BCDR) ?

Lors de la préparation de votre environnement pour les scénarios BCDR, où la ressource cible subit une panne ou un autre incident, il incombe à l’organisation d’éviter la perte de données en créant des sauvegardes conformes aux instructions fournies par Azure Event Hubs, l’espace de travail Log Analytics et l’application logique.

Pour chaque automatisation active, nous vous recommandons de créer une automation identique (désactivée) et de la stocker à un autre emplacement. En cas de panne, vous pouvez activer ces automations de sauvegarde et gérer les opérations normales.

En savoir plus sur la [Continuité d’activité et reprise d’activité pour Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des applications logiques, à automatiser leur exécution dans Defender pour le cloud et à les exécuter manuellement.

Consultez les documents connexes suivants : 

- [Module Microsoft Learn sur la façon d’utiliser l’automatisation des workflows pour automatiser une réponse de sécurité](/learn/modules/resolve-threats-with-azure-security-center/)
- [Recommandations de sécurité dans Microsoft Defender pour le cloud](review-security-recommendations.md)
- [Alertes de sécurité dans Microsoft Defender pour le cloud](alerts-overview.md)
- [À propos d’Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
- [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas)
