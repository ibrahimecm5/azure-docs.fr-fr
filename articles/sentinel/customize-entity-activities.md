---
title: Personnaliser les activités sur les chronologies d’entité Microsoft Sentinel | Microsoft Docs
description: Ajoutez des activités personnalisées à celles que Microsoft Sentinel suit et affiche sur la chronologie des pages d’entité.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0483d839af7a8707aa3be632f9d168447488e768
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523616"
---
# <a name="customize-activities-on-entity-page-timelines"></a>Personnaliser les activités sur les chronologies de page d’entité

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - La personnalisation de l’activité est en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="introduction"></a>Introduction

En plus des activités suivies et présentées dans la chronologie par Microsoft Sentinel sans configuration supplémentaire, vous pouvez créer toutes autres activités dont vous souhaitez effectuer le suivi et les présenter également sur la chronologie. Vous pouvez créer des activités personnalisées basées sur des requêtes de données d’entité à partir de toutes les sources de données connectées. Les exemples suivants illustrent la façon dont vous pouvez utiliser cette fonctionnalité :

- Ajoutez de nouvelles activités à la chronologie de l’entité en modifiant les modèles d’activité prêts à l’emploi existants.

- Ajoutez de nouvelles activités à partir de journaux personnalisés : par exemple, à partir d’un journal de contrôle d’accès physique, vous pouvez ajouter les activités d’entrée et de sortie d’un utilisateur pour un bâtiment particulier à la chronologie de l’utilisateur.

## <a name="getting-started"></a>Bien démarrer

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Comportement des entités**.

1. Dans le panneau **Comportement des entités**, sélectionnez **Personnaliser la page d’entité** en haut de l’écran.

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="Page Comportement des entités":::

1. Vous verrez une page contenant une liste de toutes les activités que vous avez créées dans l’onglet **Mes activités**. Dans l’onglet **Modèles d’activité**, vous verrez la collection d’activités proposées prêtes à l’emploi par les chercheurs en sécurité de Microsoft. Ce sont les activités qui font déjà l’objet d’un suivi et qui sont affichées dans les chronologies de vos pages d’entité.

    > [!NOTE]
    > - Tant que vous n’avez créé aucune activité définie par l’utilisateur, vos pages d’entité affichent toutes les activités listées sous l’onglet **Modèles d’activité**.
    >
    > - Une fois que vous avez défini une seule activité personnalisée, vos pages d’entité affichent **uniquement** les activités qui s’affichent sous l’onglet **Mes activités**.
    >
    > - Si vous souhaitez continuer à voir les activités prêtes à l’emploi dans vos pages d’entité, vous devez créer une activité pour chaque modèle à suivre et afficher. Suivez les instructions de la section « Créer une activité à partir d’un modèle » ci-dessous.

## <a name="create-an-activity-from-a-template"></a>Créer une activité à partir d’un modèle

1. Cliquez sur l’onglet **Modèles d’activité** pour afficher les différentes activités disponibles par défaut. Vous pouvez filtrer la liste par type d’entité et par source de données. Si vous sélectionnez une activité dans la liste, les détails suivants s’affichent dans le volet de visualisation :

    -  Une description de l’activité

    - La source de données qui fournit les événements qui composent l’activité

    - Les identificateurs utilisés pour identifier l’entité dans les données brutes

    - La requête qui entraîne la détection de cette activité

1. Cliquez sur le bouton **Créer une activité** en bas du volet de visualisation pour démarrer l’Assistant Création d’activité.

    :::image type="content" source="./media/customize-entity-activities/activity-details.png" alt-text="Afficher les détails de l’activité":::

1. L’**Assistant Activité - Créer une activité à partir d’un modèle** s’ouvre, avec ses champs déjà remplis à partir du modèle. Vous pouvez apporter les modifications souhaitées dans les onglets **Général** et **Configuration de l’activité**, ou laisser toutes les valeurs par défaut pour continuer à afficher l’activité prête à l’emploi.

1. Lorsque vous êtes satisfait, sélectionnez l’onglet **Vérifier et créer**. Lorsque le message **Validation réussie** s’affiche, cliquez sur le bouton **Créer** en bas.

## <a name="create-an-activity-from-scratch"></a>Créer une activité à partir de zéro

Dans la partie supérieure de la page Activités, cliquez sur **Ajouter une activité** pour démarrer l’Assistant Création d’activité.

L’**Assistant Activité - Créer une activité** s’ouvre, avec les champs vides.

### <a name="general-tab"></a>Onglet Général
1. Entrez un nom pour votre activité (exemple : « Utilisateur ajouté au groupe »).

1. Entrez une description de l’activité (exemple : « Modification de l’appartenance au groupe d’utilisateurs basée sur l’ID d’événement Windows 4728 »).

1. Sélectionnez le type d’entité (utilisateur ou hôte) que cette requête doit suivre.

1. Vous pouvez filtrer par paramètres supplémentaires pour affiner la requête et optimiser ses performances. Par exemple, vous pouvez filtrer les utilisateurs Active Directory en choisissant le paramètre **IsDomainJoined** et en définissant la valeur sur **True**.

1. Vous pouvez définir l’état initial de l’activité sur **Activé** ou **Désactivé**.

1. Sélectionnez **Étape suivante : Configuration de l’activité** pour passer à l’onglet suivant.

    :::image type="content" source="./media/customize-entity-activities/create-new-activity.png" alt-text="Capture d’écran - Créer une activité":::

### <a name="activity-configuration-tab"></a>Onglet Configuration de l’activité

#### <a name="writing-the-activity-query"></a>Écriture de la requête d’activité

Ici, vous allez écrire ou coller la requête KQL qui sera utilisée pour détecter l’activité de l’entité choisie, et déterminer comment elle sera représentée dans la chronologie.

Pour mettre en corrélation les événements et détecter l’activité personnalisée, KQL requiert une entrée de plusieurs paramètres, selon le type d’entité. Les paramètres sont les différents identificateurs de l’entité en question.

Il est préférable de sélectionner un identificateur fort afin d’avoir un mappage un-à-un entre les résultats de la requête et l’entité. La sélection d’un identificateur faible peut générer des résultats inexacts. [En savoir plus sur les entités et les identificateurs forts et faibles](entities-in-azure-sentinel.md).

Le tableau suivant fournit des informations sur les identifiants des entités.

**Identificateurs forts pour les entités de comptes et hôtes**

Au moins un identifiant est requis dans une requête.

| Entité | Identificateur | Description |
| - | - | - |
| **Compte** | Account_Sid | SID local du compte dans Active Directory |
| | Account_AadUserId | L’ID de l’objet Azure AD de l’utilisateur dans Azure Active Directory |
| | Account_Name + Account_NTDomain | Semblable à SamAccountName (exemple : Contoso\Joe) |
| | Account_Name + Account_UPNSuffix | Semblable à UserPrincipalName (exemple : Joe@Contoso.com ) |
| **Hôte** | Host_HostName + Host_NTDomain | similaire au nom de domaine complet (FQDN) |
| | Host_HostName + Host_DnsDomain | similaire au nom de domaine complet (FQDN) |
| | Host_NetBiosName + Host_NTDomain | similaire au nom de domaine complet (FQDN) |
| | Host_NetBiosName + Host_DnsDomain | similaire au nom de domaine complet (FQDN) |
| | Host_AzureID | ID de l’objet Azure AD de l’hôte dans Azure Active Directory (si le domaine AAD est joint) |
| | Host_OMSAgentID | ID de l’agent OMS de l’agent installé sur un ordinateur hôte spécifique (unique par hôte) |
|

En fonction de l’entité sélectionnée, vous verrez les identificateurs disponibles. En cliquant sur les identificateurs appropriés, vous collez l’identificateur dans la requête, à l’emplacement du curseur.

> [!NOTE]
> - Comme la requête peut contenir **jusqu’à 10 champs**, vous devez projeter les champs de votre choix.
>
> - Les champs projetés doivent inclure le champ **TimeGenerated** afin de placer l’activité détectée dans la chronologie de l’entité.

```kusto
SecurityEvent
| where EventID == "4728"
| where (SubjectUserSid == '{{Account_Sid}}' ) or (SubjectUserName == '{{Account_Name}}' and SubjectDomainName == '{{Account_NTDomain}}' )
| project TimeGenerated, SubjectUserName, MemberName, MemberSid, GroupName=TargetUserName
```

:::image type="content" source="./media/customize-entity-activities/new-activity-query.png" alt-text="Capture d’écran - Entrer une requête pour détecter l’activité":::

#### <a name="presenting-the-activity-in-the-timeline"></a>Présentation de l’activité dans la chronologie

Par souci pratique, vous pouvez déterminer comment l’activité est présentée dans la chronologie en ajoutant des paramètres dynamiques à la sortie de l’activité.

Microsoft Sentinel fournit des paramètres intégrés que vous pouvez utiliser. Vous pouvez également en utiliser d’autres en fonction des champs que vous avez projetés dans la requête.

Utilisez le format suivant pour vos paramètres : `{{ParameterName}}`

Une fois que la requête d’activité a réussi la validation et affiche le lien **Afficher les résultats de la requête** sous la fenêtre de requête, vous pouvez développer la section **Valeurs disponibles** pour afficher les paramètres que vous pouvez utiliser lors de la création d’un titre d’activité dynamique.

Sélectionnez l’icône de **copie** en regard d’un paramètre spécifique pour copier ce paramètre dans le Presse-papiers afin de pouvoir le coller dans le champ du **titre d’activité** ci-dessus.

Ajoutez l’un des paramètres suivants à votre requête :

- Tout champ que vous projetez dans la requête.

- Identificateurs de toutes les entités mentionnées dans la requête.

- `StartTimeUTC`, pour ajouter l’heure de début de l’activité, en heure UTC.

- `EndTimeUTC`, pour ajouter l’heure de fin de l’activité, en heure UTC.

- `Count`, pour résumer plusieurs sorties de requête KQL en une seule sortie.

    Le paramètre `count` ajoute la commande suivante à votre requête en arrière-plan, même si elle ne s’affiche pas entièrement dans l’éditeur :

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>
    ```

    Ensuite, lorsque vous utilisez le filtre **Taille du compartiment** dans les pages d’entité, la commande suivante est également ajoutée à la requête qui est exécutée en arrière-plan :

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>, bin (TimeGenerated, Bucket in Hours)
    ```

Par exemple :

:::image type="content" source="./media/customize-entity-activities/new-activity-title.png" alt-text="Capture d’écran - Voir les valeurs disponibles pour le titre de votre activité":::

Lorsque vous êtes satisfait du titre de votre activité et de votre requête, sélectionnez **Étape suivante : Examiner**.

### <a name="review-and-create-tab"></a>Onglet Vérifier et créer

1. Vérifiez toutes les informations de configuration de votre activité personnalisée.

1. Lorsque le message **Validation réussie** s’affiche, cliquez sur **Créer** pour créer l’activité. Vous pouvez la modifier ultérieurement sous l’onglet **Mes activités**.

## <a name="manage-your-activities"></a>Gérer vos activités 

Gérez vos activités personnalisées à partir de l’onglet **Mes activités**. Cliquez sur les points de suspension (...) à la fin de la ligne d’une activité pour effectuer les opérations suivantes :

- Modifier l’activité.
- Dupliquer l’activité pour en créer une nouvelle, légèrement différente.
- Supprimer l’activité.
- Désactiver l’activité (sans la supprimer).

## <a name="view-activities-in-an-entity-page"></a>Afficher les activités dans une page d’entité

Chaque fois que vous entrez sur une page d’entité, toutes les requêtes d’activité activées pour cette entité s’exécutent, ce qui vous permet d’obtenir des informations à la minute près dans la chronologie de l’entité. Vous verrez les activités dans la chronologie, en même temps que les alertes et les signets. 

Vous pouvez utiliser le filtre **Contenu de la chronologie** pour présenter uniquement des activités (ou n’importe quelle combinaison d’activités, d’alertes et de signets).  

Vous pouvez également utiliser le filtre **Activités** pour présenter ou masquer des activités spécifiques. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à créer des activités personnalisées pour vos chronologies de page d’entité. Pour en savoir plus sur Microsoft Sentinel, consultez les articles suivants :
- Découvrez les [pages d’entité](identify-threats-with-entity-behavior-analytics.md).
- Consultez la liste complète des [entités et identificateurs](entities-reference.md).
