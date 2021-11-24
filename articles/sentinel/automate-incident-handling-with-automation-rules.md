---
title: Automatiser la gestion des incidents dans Microsoft Sentinel | Microsoft Docs
description: Cet article explique comment utiliser des règles d’automatisation pour automatiser la gestion des incidents, afin d’optimiser l’efficacité et l’efficience de votre centre des opérations de sécurité (SOC) en réponse à des menaces de sécurité.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 52f716bf8a5a4457f1f61545e079c17f16c708c3
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522153"
---
# <a name="automate-incident-handling-in-microsoft-sentinel-with-automation-rules"></a>Automatisation de la gestion des incidents dans Microsoft Sentinel avec des règles d’automatisation

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article explique les règles d’automatisation de Microsoft Sentinel et la manière de les utiliser pour implémenter vos opérations d’orchestration, d’automatisation et de réponse en matière de sécurité (Security Orchestration, Automation and Response, SOAR), en renforçant l’efficacité de votre SOC et en vous permettant d’économiser du temps et des ressources.

> [!IMPORTANT]
>
> - La fonctionnalité de **règles d’automatisation** est actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="what-are-automation-rules"></a>Que sont les règles d’automatisation ?

Les règles d’automatisation sont un concept nouveau dans Microsoft Sentinel. Cette fonctionnalité permet aux utilisateurs de gérer de manière centralisée l’automatisation de la gestion des incidents. En plus de vous permettre d’attribuer des playbooks aux incidents (pas seulement aux alertes comme auparavant), les règles d’automatisation vous permettent d’automatiser les réponses pour plusieurs règles d’analytique à la fois, d’étiqueter, d’attribuer ou de fermer automatiquement des incidents sans avoir besoin de playbooks, ainsi que de contrôler l’ordre des actions exécutées. Les règles d’automatisation simplifient l’utilisation de l’automatisation dans Microsoft Sentinel, et vous permettent de simplifier des flux de travail complexes pour vos processus d’orchestration d’incident.

## <a name="components"></a>Composants

Les règles d’automatisation sont constituées de plusieurs composants :

### <a name="trigger"></a>Déclencheur

Les règles d’automatisation sont déclenchées par la création d’un incident. 

Pour révision : les incidents sont créés à partir d’alertes déclenchées par des règles d’analytique. Il existe plusieurs types de règles, comme l’explique le tutoriel [Détection des menaces avec des règles d’analytique intégrées dans Microsoft Sentinel](detect-threats-built-in.md).

### <a name="conditions"></a>Conditions

Vous pouvez définir des ensembles complexes de conditions pour régir le moment où des actions (voir ci-dessous) doivent s’exécuter. Ces conditions sont généralement basées sur les états ou les valeurs d’attributs d’incidents et leurs entités, et peuvent inclure des opérateurs `AND`/`OR`/`NOT`/`CONTAINS`.

### <a name="actions"></a>Actions

Vous pouvez définir des actions peuvent à exécuter quand les conditions (voir ci-dessus) sont remplies. Vous pouvez définir de nombreuses actions dans une règle et choisir l’ordre dans lequel elles s’exécuteront (voir ci-dessous). Vous pouvez définir les actions ci-après à l’aide de règles d’automatisation, sans devoir faire appel à la [fonctionnalité avancée d’un playbook](automate-responses-with-playbooks.md) :

- Modification de l’état d’un incident, maintien à jour de votre flux de travail.

  - Lors du passage à « fermé », spécification de la [raison de la fermeture](investigate-cases.md#closing-an-incident) et ajout d’un commentaire. Cela vous permet d’effectuer le suivi de vos performances et de votre efficacité, ainsi que de régler précisément pour réduire les [faux positifs](false-positives.md).

- Modification de la gravité d’un incident : vous pouvez réévaluer et ré-hiérarchiser en fonction de la présence, de l’absence, des valeurs ou des attributs des entités impliquées dans l’incident.

- Attribution d’un incident à un propriétaire : cela vous aide à diriger les différents types d’incidents vers le personnel le plus compétent pour les gérer, ou le personnel le plus disponible.

- Ajout d’une étiquette à un incident : cela est utile pour classer les incidents par objet, par attaquant ou par tout autre dénominateur commun.

Vous pouvez également définir une action [**exécuter un playbook**](tutorial-respond-threats-playbook.md) pour prendre des mesures de réponse plus complexes, notamment impliquant des systèmes externes. **Seuls** les playbooks activés par le [**déclencheur d’incident**](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) sont utilisables dans des règles d’automatisation. Vous pouvez définir une action pour inclure plusieurs playbooks ou des combinaisons de playbooks et d’autres actions, ainsi que l’ordre dans lequel ils s’exécuteront.

### <a name="expiration-date"></a>Date d'expiration

Vous pouvez définir une date d’expiration sur une règle d’automatisation. La règle sera désactivée après cette date. Cela est utile pour gérer (c’est-à-dire fermer) des incidents de « bruit » occasionnés par des activités planifiées limitées dans le temps, telles qu’un test d’intrusion.

### <a name="order"></a>Commande

Vous pouvez définir l’ordre dans lequel les règles d’automatisation s’exécuteront. Les règles d’automatisation ultérieures évalueront les conditions de l’incident en fonction de son état après traitement par des règles d’automatisation précédentes.

Par exemple, si une « première règle d’automatisation » a modifié le niveau de gravité d’un incident de Moyenne à Faible, et si une « deuxième règle d’automatisation » est définie pour s’exécuter uniquement sur les incidents de gravité Moyenne ou supérieure, elle ne s’exécute pas sur cet incident.

## <a name="common-use-cases-and-scenarios"></a>Cas d’utilisation et scénarios courants

### <a name="incident-triggered-automation"></a>Automatisation déclenchée par un incident

Jusqu’à présent, seules des alertes pouvaient déclencher une réponse automatisée via l’utilisation de playbooks. Avec des règles d’automatisation, des incidents peuvent désormais déclencher des chaînes de réponses automatisées qui peuvent inclure de nouveaux playbooks déclenchés par incident ([des autorisations spéciales sont requises](#permissions-for-automation-rules-to-run-playbooks)), lors de la création d’un incident. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Playbooks de déclencheur pour fournisseurs Microsoft

Les règles d’automatisation offrent un moyen d’automatiser la gestion des alertes de sécurité Microsoft en appliquant ces règles aux incidents créés à partir des alertes. Les règles d’automatisation peuvent appeler des playbooks ([des autorisations spéciales sont requises](#permissions-for-automation-rules-to-run-playbooks)) et leur transmettre les incidents avec tous leurs détails, alertes et entités comprises. En général, les meilleures pratiques de Microsoft Sentinel dictent d’utiliser la file d’attente des incidents comme point focal des opérations de sécurité.

Les alertes de sécurité Microsoft sont les suivantes :

- Microsoft Defender for Cloud Apps
- Azure AD Identity Protection
- Microsoft Defender pour le cloud
- Defender pour IoT (anciennement Azure Security Center pour IoT)
- Microsoft Defender pour Office 365 (anciennement Office 365 ATP)
- Microsoft Defender for Endpoint (anciennement MDATP)
- Microsoft Defender for Identity (anciennement Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Plusieurs playbooks/actions séquencés dans une même règle

Vous pouvez maintenant exercer un contrôle presque complet sur l’ordre d’exécution des actions et des playbooks dans une même règle d’automatisation. Vous pouvez également contrôler l’ordre d’exécution des règles d’automatisation elles-mêmes. Cela vous permet de simplifier considérablement vos playbooks, en les réduisant à une seule tâche ou à une petite séquence de tâches simple, et de combiner ces petits playbooks dans différentes combinaisons dans différentes règles d’automatisation.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Attribuer un seul playbook à plusieurs règles d’analytique à la fois

Si vous avez une tâche que vous souhaitez automatiser sur toutes vos règles d’analytique (par exemple, la création d’un ticket de support dans un système de ticket externe), vous pouvez appliquer un seul playbook à la totalité ou à une partie de vos règles d’analytique, y compris des règles futures, en une seule fois. Cela rend les tâches de maintenance et de nettoyage simples mais répétitives beaucoup moins fastidieuses.

### <a name="automatic-assignment-of-incidents"></a>Attribution automatique d’incidents

Vous pouvez attribuer automatiquement des incidents au propriétaire approprié. Si votre SOC dispose d’un analyste spécialisé dans une plateforme particulière, tous les incidents liés à cette plateforme peuvent être attribués automatiquement à cet analyste.

### <a name="incident-suppression"></a>Suppression d’incident

Vous pouvez utiliser des règles pour résoudre automatiquement des incidents connus pour être des positifs faux/bénins sans utiliser de playbooks. Par exemple, quand vous effectuez des tests de pénétration, des tâches de maintenance planifiée ou des mises à niveau, ou quand vous testez des procédures d’automatisation, cela peut générer de nombreux incidents faux positifs que le SOC souhaite ignorer. Une règle d’automatisation limitée dans le temps peut automatiquement fermer ces incidents au fur et à mesure de leur création, tout en les marquant avec un descripteur de la cause de leur génération.

### <a name="time-limited-automation"></a>Automatisation limitée dans le temps

Vous pouvez ajouter des dates d’expiration pour vos règles d’automatisation. Il peut y avoir des cas autres que la suppression d’incident qui garantissent une automatisation limitée dans le temps. Vous pouvez attribuer un type particulier d’incident à un utilisateur particulier (par exemple, un interne ou un consultant) pour un délai d’exécution spécifique. Si le délai d’exécution est connu à l’avance, vous pouvez très bien faire en sorte que la règle soit désactivée à la fin de sa pertinence, sans que vous ayez à vous souvenir de le faire.

### <a name="automatically-tag-incidents"></a>Étiqueter automatiquement des incidents

Vous pouvez ajouter automatiquement des étiquettes en texte libre à des incidents pour les regrouper ou les classer en fonction de critères de votre choix.

## <a name="automation-rules-execution"></a>Exécution de règles d’automatisation

Les règles d’automatisation sont exécutées de façon séquentielle, en fonction de l’ordre que vous déterminez. Chaque règle d’automatisation est exécutée une fois l’exécution de la règle précédente terminée. Dans une règle d’automatisation, toutes les actions sont exécutées de façon séquentielle dans l’ordre dans lequel elles sont définies.

Les actions du playbook au sein d’une règle d’automatisation peuvent être traitées différemment dans certaines circonstances, en fonction des critères suivants :

| Temps d’exécution du playbook | La règle d’automatisation passe à l’action suivante... |
| ----------------- | --------------------------------------------------- |
| Inférieur à une seconde | Immédiatement après l’exécution du playbook |
| Moins de deux minutes | Jusqu’à deux minutes après le début de l’exécution du playbook,<br>mais pas plus de 10 secondes après la fin du playbook |
| Moins de deux minutes | Deux minutes après le début de l’exécution du playbook,<br>qu’il soit ou non terminé |
|

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Autorisations de règles d’automatisation pour l’exécution de playbooks

Quand une règle d’automatisation de Microsoft Sentinel exécute un playbook, elle utilise un compte de service Microsoft Sentinel spécial spécifiquement autorisé pour cette action. L’utilisation de ce compte (par opposition à votre compte d’utilisateur) augmente le niveau de sécurité du service.

Pour qu’une règle d’automatisation exécute un playbook, ce compte doit disposer d’autorisations explicites sur le groupe de ressources dans lequel se trouve le playbook. À ce stade, toute règle d’automatisation sera en mesure d’exécuter n’importe quel playbook dans ce groupe de ressources.

Quand vous configurez une règle d’automatisation et ajoutez l’action **Exécuter le playbook**, une liste déroulante de playbooks s’affiche. Les playbooks sur lesquels Microsoft Sentinel n’a pas d’autorisation apparaissent indisponibles (« grisés »). Vous pouvez accorder une autorisation Microsoft Sentinel aux groupes de ressources du playbook sur place en sélectionnant le lien **Gérer les autorisations des playbooks**.

#### <a name="permissions-in-a-multi-tenant-architecture"></a>Autorisations dans une architecture mutualisée

Les règles d’automatisation prennent entièrement en charge les déploiements [multi-locataires](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse) sur plusieurs espaces de travail (avec [Azure Lighthouse](../lighthouse/index.yml) dans le cas d’un déploiement multi-locataire).

Par conséquent, si votre déploiement de Microsoft Sentinel utilise une architecture multi-locataire, vous pouvez faire en sorte qu’une règle d’automatisation située dans un locataire exécute un playbook qui se trouve dans un autre locataire. Cependant, les autorisations permettant à Sentinel d’exécuter les playbooks doivent être établies dans le locataire où résident les playbooks, et non dans celui où les règles d’automatisation sont définies.

Dans le cas précis d’un fournisseur de services de sécurité gérée (où le locataire d’un fournisseur de services gère un espace de travail Microsoft Sentinel dans le locataire d’un client), deux scénarios particuliers doivent être pris en compte :

- **Une règle d’automatisation créée dans le locataire du client est configurée pour exécuter un playbook situé dans le locataire du fournisseur de services.** 

    Cette approche est normalement utilisée pour protéger la propriété intellectuelle dans le playbook. Rien de spécial n’est nécessaire pour que ce scénario fonctionne. Lorsque vous définissez une action de playbook dans votre règle d’automatisation et que vous arrivez à la phase où vous accordez des autorisations Microsoft Sentinel sur le groupe de ressources dans lequel se trouve le playbook (à l’aide du panneau **Gérer les autorisations du playbook**), les groupes de ressources appartenant au locataire du fournisseur de services figurent parmi les choix possibles. [Consultez l’ensemble du processus décrit ici](tutorial-respond-threats-playbook.md#respond-to-incidents).

- **Une règle d’automatisation créée dans l’espace de travail du client (avec connexion au locataire du fournisseur de services) est configurée pour exécuter un playbook situé dans le locataire du client.**

    Cette configuration est utilisée lorsqu’il n’est pas nécessaire de protéger la propriété intellectuelle. Pour que ce scénario fonctionne, les autorisations d’exécution du playbook doivent être accordées à Microsoft Sentinel dans ***les deux locataires** _. Dans le locataire du client, vous pouvez les octroyer dans le panneau _ *Gérer les autorisations du playbook** (cf. scénario ci-dessus). Pour accorder les autorisations appropriées dans le locataire du fournisseur de services, vous devez ajouter une délégation Azure Lighthouse supplémentaire qui accorde des droits d’accès à l’application **Azure Security Insights** avec le rôle **Contributeur Automatisation Microsoft Sentinel** sur le groupe de ressources où se trouve le playbook.

    Le scénario ressemble à ceci :

    :::image type="content" source="./media/automate-incident-handling-with-automation-rules/automation-rule-multi-tenant.png" alt-text="Architecture des règles d’automatisation multi-locataires":::

    Pour mettre en place cette configuration, consultez [nos instructions](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks).

## <a name="creating-and-managing-automation-rules"></a>Création et gestion de règles d’automatisation

Vous pouvez créer et gérer des règles d’automatisation à partir de différents points dans l’expérience Microsoft Sentinel, en fonction de votre besoin particulier et de votre cas d’utilisation.

- **Panneau Automatisation**

    Vous pouvez gérer les règles d’automatisation de manière centralisée dans le nouveau panneau **Automatisation** (qui remplace le panneau **Playbooks**) sous l’onglet **Règles d’automatisation** (vous pouvez désormais aussi gérer des playbooks dans ce panneau sous l’onglet **Playbooks**). À partir de là, vous pouvez créer des règles d’automatisation et modifier des règles existantes. Vous pouvez également faire glisser des règles d’automatisation pour modifier leur ordre d’exécution, ainsi que les activer ou les désactiver.

    Le panneau **Automatisation** affiche toutes les règles définies dans l’espace de travail, ainsi que leur état (activé/désactivé) et les règles d’analytique auxquelles elles sont appliquées.

    Si vous avez besoin d’une règle d’automatisation s’appliquant à de nombreuses règles d’analytique, créez-la directement dans le panneau **Automatisation**. Dans le menu supérieur, cliquez sur **Créer** et **Ajouter une nouvelle règle** pour ouvrir le panneau **Créer une nouvelle règle d’automatisation**. À partir de là, vous disposez d’une flexibilité totale pour la configuration de la règle : vous pouvez l’appliquer à toute règle d’analytique (y compris future) et définir la plage la plus large de conditions et d’actions.

- **Assistant Règle d’analytique**

    Sous l’onglet **Réponse automatisée** de l’Assistant Tègle d’analytique, vous pouvez voir, gérer et créer des règles d’automatisation qui s’appliquent à la règle d’analytique particulière créée ou modifiée dans l’Assistant.

    Lorsque vous cliquez sur **Créer** et l’un des types de règles (**Règle de requête planifiée** ou **Règle de création d’incident Microsoft**) dans le menu supérieur du panneau **Analytique**, ou si vous sélectionnez une règle d’analytique existante, puis cliquez sur **Modifier**, vous ouvrez l’Assistant Règle. Lorsque vous sélectionnez l’onglet **Réponse automatisée**, vous voyez une section appelée **Automatisation des incidents** affichant les règles d’automatisation qui s’appliquent actuellement à cette règle. Vous pouvez sélectionner une règle d’automatisation existante à modifier, ou cliquer sur **Ajouter** pour en créer une nouvelle.

    Vous remarquerez que, lorsque vous créez la règle d’automatisation à partir d’ici, le panneau **Créer une nouvelle règle d’automatisation** affiche la condition de **Règle d’analytique** comme indisponible, car cette règle est déjà définie pour s’appliquer uniquement à la règle d’analytique que vous modifiez dans l’Assistant. Toutes les autres options de configuration sont toujours disponibles.

- **Panneau Incidents**

    Vous pouvez également créer une règle d’automatisation à partir du panneau **Incidents** en réponse à un incident unique et périodique. Cela est utile lors de la création d’une [règle de suppression](#incident-suppression) pour la fermeture automatique d’incidents « bruyants ». Sélectionnez un incident dans la file d’attente, puis, dans le menu supérieur, cliquez sur **Créer une règle d’automatisation**.

    Vous remarquerez que, dans le volet **créer une nouvelle règle d’automatisation** tous les champs sont renseignés avec des valeurs de l’incident. Il attribue à la règle le nom de l’incident, l’applique à la règle d’analytique qui a généré l’incident, et utilise toutes les entités disponibles dans l’incident en tant que conditions de la règle. Il suggère également une action de suppression (fermeture) par défaut, ainsi qu’une date d’expiration pour la règle. Vous pouvez ajouter ou supprimer des conditions et actions, ainsi que modifier la date d’expiration à votre guise.

## <a name="auditing-automation-rule-activity"></a>Audit de l’activité de règle d’automatisation

Il peut être intéressant de savoir ce qui est arrivé à un incident donné et l’incidence qu’une règle d’automatisation donnée peut avoir eu ou non à cet égard. Vous disposez d’un enregistrement complet des chroniques d’incidents à votre disposition dans le tableau *SecurityIncident* du panneau **Journaux**. Utilisez la requête suivante pour afficher l’activité de votre règle d’automatisation :

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment utiliser des règles d’automatisation pour gérer votre file d’attente d’incidents Microsoft Sentinel, et implémenter une automatisation de base de la gestion des incidents.

- Pour en savoir plus sur les options d’automatisation avancées, consultez [Automatiser la réponse aux menaces à l’aide de playbooks dans Microsoft Sentinel](automate-responses-with-playbooks.md).
- Pour obtenir de l’aide sur l’implémentation des playbooks et des règles d’automatisation, consultez [Didacticiel : utiliser des playbooks pour automatiser les réponses aux menaces dans Microsoft Sentinel](tutorial-respond-threats-playbook.md).
