---
title: Utilisation de déclencheurs et d’actions dans les playbooks Microsoft Sentinel | Microsoft Docs
description: Découvrez plus en détail comment permettre à vos playbooks d’accéder aux informations de vos alertes et incidents Microsoft Sentinel et d’utiliser ces informations pour prendre des mesures correctives.
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
ms.openlocfilehash: 89b296d47f24391eb0c31b2292aa4add273d71f6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524395"
---
# <a name="use-triggers-and-actions-in-microsoft-sentinel-playbooks"></a>Utilisation de déclencheurs et d’actions dans les playbooks Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ce document explique les types de déclencheurs et d’actions du [connecteur Logic Apps Microsoft Sentinel](/connectors/azuresentinel/) que les playbooks peuvent utiliser pour interagir avec Microsoft Sentinel et les informations contenues dans les tables de votre espace de travail. Il vous montre également comment accéder à des types spécifiques d’informations Azure Sentinel dont vous aurez probablement besoin.

Ce document, de même que notre guide [Authentification des playbooks dans Microsoft Sentinel](authenticate-playbooks-to-sentinel.md), vient compléter notre autre documentation sur les playbooks, [Tutoriel : Utilisation des playbooks avec des règles d’automatisation dans Microsoft Sentinel](tutorial-respond-threats-playbook.md). Ces trois documents font référence les uns aux autres.

Pour obtenir une présentation des playbooks, consultez [Automatiser la réponse aux menaces à l’aide de playbooks dans Microsoft Sentinel](automate-responses-with-playbooks.md).

Pour connaître la spécification complète du connecteur Microsoft Sentinel, consultez la [documentation du connecteur Logic Apps](/connectors/azuresentinel/).

## <a name="permissions-required"></a>Autorisations requises

| Rôles/Composants du connecteur | Déclencheurs | Actions « Get » | Mettre à jour l’incident,<br>ajouter un commentaire |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Lecteur Microsoft Sentinel](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **[Répondeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)/[Contributeur](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor) Microsoft Sentinel** | &#10003; | &#10003; | &#10003; |
| 

[En savoir plus sur les autorisations dans Microsoft Sentinel.](./roles.md)

## <a name="microsoft-sentinel-triggers-summary"></a>Résumé déclencheurs Microsoft Sentinel

Bien que le connecteur Microsoft Sentinel soit utilisable de différentes manières, ses composants peuvent être divisés en deux flux, chacun déclenché par une occurrence Microsoft Sentinel distincte :

| Déclencheur | Nom complet du déclencheur dans<br>Concepteur Logic Apps | Quand utiliser cette fonctionnalité ? | Limitations connues 
| --------- | ------------ | -------------- | -------------- | 
| **Déclencheur d’incident** | « Quand la règle de création d’incidents Microsoft Sentinel a été déclenchée (préversion) » | Recommandé pour la plupart des scénarios d’automatisation des incidents.<br><br>Le playbook reçoit les objets Incident, notamment les entités et les alertes. Ce déclencheur permet d’associer le playbook à une **règle d’automatisation**. Il peut donc être mis en œuvre lorsqu’un incident est créé dans Microsoft Sentinel. Tous les [avantages des règles d’automatisation](./automate-incident-handling-with-automation-rules.md) s’appliquent alors à l’incident. | Les playbooks qui comportent ce déclencheur ne peuvent pas être exécutées manuellement dans Microsoft Sentinel.<br><br>Les playbooks qui comportent ce déclencheur ne prennent pas en charge le regroupement d’alertes. Ils ne reçoivent donc que la première alerte envoyée avec chaque incident.
| **Déclencheur d’alerte** | « Quand une réponse à une alerte Microsoft Sentinel est déclenchée » | Recommandé pour les playbooks qui doivent être exécutés manuellement sur les alertes à partir du portail Microsoft Sentinel et pour les règles d’analytique **planifiées** qui ne génèrent pas d’incidents pour leurs alertes. | Ce déclencheur ne peut pas être utilisé pour automatiser les réponses dans le cas des alertes générées par les règles d’analytique de **sécurité Microsoft**.<br><br>Les playbooks qui utilisent ce déclencheur ne peuvent pas être appelées par des **règles d’automatisation**. |
|

Les schémas utilisés par ces deux flux ne sont pas identiques. La pratique recommandée consiste à utiliser le flux **déclenchement d’incident Microsoft Sentinel**, qui s’applique à la plupart des scénarios.

### <a name="incident-dynamic-fields"></a>Champs dynamiques des incidents

L’objet **Incident** envoyé par **Quand la règle de création d’incidents Microsoft Sentinel a été déclenchée** comprend les champs dynamiques suivants :

- Propriétés de l’incident (indiquées sous la forme « Incident : nom du champ »)

- Alertes (tableau)

  - Propriétés de l’alerte (indiquées sous la forme « Alerte : nom du champ »)

    Lorsque vous sélectionnez une propriété d’alerte du type **Alerte : nom de la propriété**, une boucle *for each* est générée automatiquement, car un incident peut inclure plusieurs alertes.

- Entités (tableau de toutes les entités d’une alerte)

- Champs d’informations sur l’espace de travail (s’applique à l’espace de travail Sentinel dans lequel l’incident a été créé)
  - Identifiant d’abonnement
  - Nom de l’espace de travail
  - ID de l’espace de travail
  - Nom de groupe ressources

## <a name="microsoft-sentinel-actions-summary"></a>Résumé des actions Microsoft Sentinel

| Composant | Quand utiliser cette fonctionnalité ? |
| --------- | -------------- |
| **Alerte – Obtenir l’incident** | Dans les playbooks qui commencent par le déclencheur d’alerte. Utile pour obtenir les propriétés de l’incident ou pour récupérer **l’ID ARM de l’incident** à utiliser avec les actions **Mettre à jour l’incident** et **Ajouter un commentaire à l’incident**. |
| **Obtenir l'incident** | Pour déclencher un playbook à partir d’une source externe ou d’un déclencheur autre que Sentinel. Identifié par un **ID ARM de l’incident**. Récupère les propriétés et les commentaires de l’incident. |
| **Mettre à jour l'incident** | Pour modifier **l’État** d’un incident (par exemple lors de la fermeture de l’incident), attribuer un **Propriétaire**, ajouter ou supprimer une balise, ou modifier sa **Gravité**, son **Titre** ou sa **Description**.
| **Ajouter des commentaires à l’incident** | Pour enrichir l’incident avec les informations collectées à partir de sources externes, auditer les actions effectuées par le playbook sur les entités, fournir des informations supplémentaires utiles pour l’examen des incidents. |
| **Entités – Obtenir <type d’entité>** | Dans les playbooks qui fonctionnent sur un type d’entité spécifique (**Adresse IP**, **Compte**, **Hôte**, **URL** ou **Hachage de fichier**), connu au moment de la création du playbook. Vous devez être en mesure de l’analyser et de travailler sur ses champs uniques. |
|

## <a name="work-with-incidents---usage-examples"></a>Exemples d’utilisation des incidents

> [!TIP] 
> Les actions **Mettre à jour l’incident** et **Ajouter un commentaire à l’incident** exigent **l’ID ARM de l’incident**.
>
> Utilisez au préalable l’action **Alerte – Obtenir l’incident** pour obtenir **l’ID ARM de l’incident**.

### <a name="update-an-incident"></a>Mise à jour d’un incident
-  Le playbook est déclenché **lors de la création d’un incident**.

    ![Exemple de flux Mettre à jour simple pour un déclencheur d’incident](media/playbook-triggers-actions/incident-simple-flow.png)

-  Le playbook est déclenché **lors de la génération d’une alerte**.

    ![Exemple de flux Mettre à jour simple pour un déclencheur d’alerte](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>Utilisation des informations sur l’incident

Voici le playbook de base pour envoyer les détails de l’incident par e-mail :
-  Le playbook est déclenché **lors de la création d’un incident**.

    ![Exemple de flux Obtenir simple pour un déclencheur d’incident](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  Le playbook est déclenché **lors de la génération d’une alerte**.

    ![Exemple de flux Obtenir simple pour un déclencheur d’alerte](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>Ajout d’un commentaire à l’incident

-  Le playbook est déclenché **lors de la création d’un incident**.

    ![Exemple Ajouter un commentaire simple pour un déclencheur d’incident](media/playbook-triggers-actions/incident-comment.png)

-  Le playbook est déclenché **lors de la génération d’une alerte**.

    ![Exemple Ajouter un commentaire simple pour un déclencheur d’alerte](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>Utilisation de types d’entités spécifiques

Le champ dynamique **Entités** consiste en un tableau d’objets JSON, chacun représentant une entité. Chaque type d’entité possède son propre schéma, en fonction de ses propriétés uniques.

L’action **Entités – Obtenir \<entity name>** permet d’effectuer les actions suivantes :

- Filtrez le tableau d’entités selon le type demandé.
- Analysez les champs spécifiques de ce type pour qu’ils puissent être utilisés comme champs dynamiques dans d’autres actions.

L’entrée correspond au champ dynamique **Entités**.

La réponse est un tableau d’entités, dans lequel les propriétés spéciales sont analysées et peuvent être utilisées directement dans une boucle *For each*.

Les types d’entités pris en charge à l’heure actuelle sont les suivants :

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [Hôte](/connectors/azuresentinel/#entities---get-hosts)
- [Compte](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [FileHash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="Liste Actions des entités":::

Pour les autres types d’entités, des fonctionnalités similaires peuvent être obtenues avec les actions intégrées Logic Apps :

- Filtrez le tableau d’entités selon le type demandé avec [**Filtrer le tableau**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action).

- Analysez les champs spécifiques de ce type pour qu’ils puissent être utilisés comme champs dynamiques dans d’autres actions avec [**Analyser le code JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="work-with-custom-details"></a>Utilisation de détails personnalisés

Le champ dynamique **Détails personnalisés des alertes**, disponible dans le **déclencheur d’incident**, consiste en un tableau d’objets JSON, chacun représentant l’un des détails personnalisés d’une alerte. Pour rappel, les [Détails personnalisés](surface-custom-details-in-alerts.md) sont des paires clé-valeur qui permettent d’afficher des informations provenant d’événements dans l’alerte, pour qu’ils puissent être représentés, suivis et analysés dans le cadre de l’incident.

Étant donné que ce champ est personnalisable dans l’alerte, son schéma dépend du type d’événement mis en évidence. Vous devez fournir des données issues d’une instance de cet événement pour générer le schéma qui déterminera la façon dont le champ de détails personnalisés sera analysé.

Voir l’exemple suivant :

![Détails personnalisés définis dans la règle d’analytique.](./media/playbook-triggers-actions/custom-details-values.png)

Dans ces paires clé-valeur, la clé (colonne de gauche) représente les champs personnalisés que vous créez, et la valeur (colonne de droite) les champs des données d’événement qui alimentent les champs personnalisés.

Vous pouvez fournir le code JSON suivant pour générer le schéma. Ce code présente les noms de clés sous forme de tableaux, et les valeurs (qui apparaissent comme les valeurs réelles, et non la colonne les contenant) comme éléments des tableaux.

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. Ajoutez une nouvelle étape avec l’action intégrée **Analyser le code JSON**. Vous pouvez entrer « analyser JSON » dans le champ de recherche pour la trouver.

1. Recherchez et sélectionnez **Détails personnalisés des alertes** dans la liste **Contenu dynamique**, sous le déclencheur d’incident.

    ![Sélection de « Détails personnalisés des alertes » dans « Contenu dynamique ».](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    Une boucle **For each** est créée, car un incident contient un tableau d’alertes.

1. Cliquez sur le lien **Utiliser l’exemple de charge utile pour générer le schéma**.

    ![Sélection de « Utiliser l’exemple de charge utile pour générer le schéma ».](./media/playbook-triggers-actions/generate-schema-link.png)

1. Indiquez un exemple de charge utile. Pour trouver un exemple de charge utile, recherchez dans Log Analytics (le panneau **Journaux**) une autre instance de cette alerte et copiez l’objet de détails personnalisés (sous **Propriétés étendues**). Dans la capture d’écran ci-dessous, nous avons utilisé le code JSON indiqué plus haut.

    ![Saisie de l’exemple de charge utile JSON.](./media/playbook-triggers-actions/sample-payload.png)

1. Les champs personnalisés sont prêts à être utilisés comme champs dynamiques de type **Tableau**. Le tableau et ses éléments figurent à la fois dans le schéma et dans la liste qui apparaît sous **Contenu dynamique** (cf. ci-dessus).

    ![Champs du schéma prêts à être utilisés.](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser les déclencheurs et les actions des playbooks Microsoft Sentinel pour répondre aux menaces. 
- Découvrez comment [rechercher des menaces de façon proactive](hunting.md) à l’aide de Microsoft Sentinel.
