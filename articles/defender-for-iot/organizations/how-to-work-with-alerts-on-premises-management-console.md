---
title: Utiliser les alertes dans la console de gestion locale
description: Utilisez la console de gestion locale pour obtenir une vue au niveau de l’entreprise des menaces récentes sur votre réseau et mieux comprendre comment les utilisateurs de capteurs les gèrent.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 2f97f4109f34ef448ccf67c02758c8df2d556c6d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293304"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Utiliser les alertes dans la console de gestion locale 

Vous pouvez effectuer les opérations suivantes à partir de la fenêtre **Alertes** dans la console de gestion :

- Utiliser des filtres d’alerte

- Utiliser des compteurs d’alerte

- Afficher les informations sur l’alerte

- Gérer les événements d’alerte

- Créer des règles d’exclusion d’alerte

- Déclencher des règles d’exclusion d’alerte à partir de systèmes externes

- Accélérer le flux de travail des incidents avec les groupes d’alertes

## <a name="view-alerts-in-the-on-premises-management-console"></a>Afficher les alertes dans la console de gestion locale

La console de gestion locale agrège les alertes de tous les capteurs connectés. Cela fournit une vue au niveau de l’entreprise des menaces récentes sur votre réseau et vous aide à mieux comprendre comment les utilisateurs de capteurs les traitent.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Capture d’écran de la fenêtre Alertes.":::

### <a name="work-with-alert-filters"></a>Utiliser des filtres d’alerte

La fenêtre **Alertes** affiche les alertes générées par les capteurs connectés à votre console de gestion locale. Vous pouvez afficher toutes les alertes pour les capteurs connectés ou présenter les alertes envoyées à partir d’un capteur spécifique :

- Site

- Zone

- Appareil

- Capteur

Sélectionnez **Effacer les filtres** pour afficher toutes les alertes.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Désactivez vos filtres en sélectionnant le bouton Effacer les filtres.":::

### <a name="work-with-alert-counters"></a>Utiliser des compteurs d’alerte

Les compteurs d’alerte fournissent une répartition des alertes par gravité et état reconnu.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Le compteur d’alertes affiche le nombre d’alertes que vous avez.":::

Les niveaux de gravité suivants apparaissent dans le compteur d’alertes :

- **Critique** : Indique une attaque malveillante qui doit être gérée immédiatement.

- **Majeure** : Indique une menace de sécurité qu’il est important de traiter.

- **Mineure** : Indique un écart par rapport au comportement de ligne de base qui peut contenir une menace de sécurité.

- **Avertissement** : Indique un écart par rapport au comportement de ligne de base sans menace de sécurité.

Les niveaux de gravité sont prédéfinis.

Vous pouvez ajuster le compteur pour fournir des nombres en fonction des alertes avec et sans accusé de réception. Les alertes sans accusé de réception ont été déclenchées par Defender pour les capteurs IoT, mais elles n’ont pas encore été révisées par les opérateurs au niveau du capteur.

Lorsque l’option **Afficher les alertes acquittées** est sélectionnée, toutes les alertes reconnues s’affichent dans la fenêtre **Alertes**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Affichez vos alertes acquittées.":::

### <a name="view-alert-information"></a>Afficher les informations sur l’alerte

L’alerte présente les informations suivantes :

- Résumé de l’événement d’alerte.

- Gravité de l'alerte.

- Un lien vers l’alerte dans le capteur qui l’a détectée.

- Un UUID d’alerte. L’UUID est constitué de l’ID d’alerte qui est associé à l’événement d’alerte détecté sur le capteur, séparé par un trait d’union et suivi d’un numéro d’ID système unique.

**UUID d’alerte de la console de gestion locale**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Un appareil est connecté, mais n’est pas autorisé.":::

**ID d’alerte de capteur**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID d’alerte de capteur.":::

L’utilisation des UUID garantit que chaque alerte affichée dans la console de gestion locale peut faire l’objet d’une recherche et est identifiable par un numéro unique. Cela est nécessaire, car les alertes générées à partir de plusieurs capteurs peuvent produire le même ID d’alerte.

> [!NOTE]
> Par défaut, les UUID sont affichés dans les systèmes partenaires suivants lors de la définition des règles de transfert : ArcSight, serveurs syslog, QRadar, Sentinel et NetWitness. Aucune configuration n’est requise.

**Pour afficher les informations sur l’alerte** :

- Dans la liste d’alertes, sélectionnez une alerte.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Capture d’écran des informations d’alerte.":::

**Pour afficher l’alerte dans le capteur** :

- Sélectionnez **OUVRIR LE CAPTEUR** de capteur à partir de l’alerte.

**Pour afficher les appareils dans une carte de zone** :

- Pour afficher la carte de l’appareil avec une focalisation sur l’appareil en état d’alerte et tous les appareils qui y sont connectés, sélectionnez **AFFICHER LES APPAREILS**.

## <a name="manage-alert-events"></a>Gérer les événements d’alerte

Plusieurs options sont disponibles pour la gestion des événements d’alerte à partir de la console de gestion locale.

- Découvrir ou accuser réception des événements d’alerte. Sélectionnez **Découvrir et accuser réception** pour découvrir tous les événements d’alerte qui peuvent être autorisés et pour accuser réception de tous les événements d’alerte qui ne sont pas acquittés actuellement.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Sélectionnez En savoir Découvrir et accuser réception pour tout découvrir.":::

- Coupez ou réactivez les événements d’alerte.

Pour en savoir plus sur l’apprentissage, l’accusé de réception et la désactivation des événements d’alerte, consultez l’article [Gérer les événements d’alerte](how-to-manage-the-alert-event.md).

## <a name="export-alert-information"></a>Envoyer des informations d’alerte

Exportez les informations d’alerte dans un fichier .csv. Vous pouvez exporter les informations de toutes les alertes détectées ou exporter des informations en fonction de la vue filtrée. Les informations suivantes sont exportées :

- Adresse de la source
- Destination Address
- Titre de l’alerte
- Gravité de l’alerte
- Message d’alerte
- Informations supplémentaires
- État de l’accusé de réception
- Disponibilité de PCAP

**Pour exporter les alertes** :

1. Sélectionnez **Alertes** dans le menu latéral.

1. Sélectionnez **Exporter**.

1. Sélectionnez **Exporter les alertes étendues** pour exporter les informations d’alerte dans des lignes distinctes pour chaque alerte qui couvre plusieurs appareils. Lorsque l’option Exporter les alertes étendues est sélectionnée, le fichier .csv crée une ligne dupliquée de l’alerte avec les éléments uniques de chaque ligne. Grâce à cette option, il est plus facile d’examiner les événements d’alerte exportés.  

## <a name="create-forwarding-rules"></a>Créer des règles de transfert

**Pour créer une règle de transfert sur la console de gestion** :

1. Connectez-vous au capteur.

1. Sélectionnez **Transfert** dans le menu latéral.

1. Sélectionnez l’icône :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/plus-add-icon.png" border="false":::.

1. Dans la fenêtre Créer une règle de transfert, entrez un nom pour la règle.

    :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/management-console-create-forwarding-rule.png" alt-text="Entrez un nom explicite dans le champ de la fenêtre Créer une règle de transfert.":::

   Définissez les critères de déclenchement d’une règle de transfert. L’utilisation de critères de règle de transfert permet d’identifier et de gérer le volume d’informations envoyé du capteur aux systèmes externes.

1. Sélectionnez un niveau de gravité dans le menu déroulant.

    Il s’agit de l’incident minimal à transférer, en termes de niveau de gravité. Par exemple, si vous sélectionnez **Mineur**, les alertes mineures et toute alerte supérieure à ce niveau de gravité seront transférées. Les niveaux sont prédéfinis.

1. Sélectionnez les protocoles à appliquer.

    Déclenche uniquement la règle de transfert si le trafic détecté s’exécutait sur des protocoles spécifiques. Sélectionnez les protocoles requis dans la liste déroulante ou sélectionnez-les tous.

1. Sélectionnez les moteurs auxquels la règle doit s’appliquer.

    
   Sélectionnez les moteurs requis ou choisissez-les tous. Les alertes des moteurs sélectionnés seront envoyées. 

1. Si vous souhaitez que la règle de transfert signale les notifications système, cochez la case correspondante.
  
1. Si vous souhaitez que la règle de transfert signale les notifications d’alerte, cochez la case correspondante.

1. Sélectionnez **Ajouter** pour ajouter une action à appliquer. Renseignez les paramètres requis pour l’action sélectionnée.

    Les actions de règle de transfert demandent au capteur de transférer les informations d’alerte aux fournisseurs ou serveurs partenaires. Vous pouvez créer plusieurs actions pour chaque règle de transfert.

1. Ajoutez toute autre action souhaitée.

1. Sélectionnez **Enregistrer**.

Si vous le souhaitez, approfondissez avec [À propos des informations d’alerte transférées](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information). Vous pouvez également [Tester des règles de transfert](how-to-forward-alert-information-to-partners.md#test-forwarding-rules) ou [Modifier et supprimer des règles de transfert](how-to-forward-alert-information-to-partners.md#edit-and-delete-forwarding-rules). Vous pouvez aussi en savoir plus sur les [Règles de transfert et règles d’exclusion d’alerte](how-to-forward-alert-information-to-partners.md#forwarding-rules-and-alert-exclusion-rules).

## <a name="create-alert-exclusion-rules"></a>Créer des règles d’exclusion d’alerte

Demandez à Defender pour IoT d’ignorer les déclencheurs d’alerte en fonction des éléments suivants :

- Fuseaux horaires et périodes

- Adresse de l’appareil (IP, MAC, sous-réseau)

- Noms des alertes

- Un capteur spécifique

Créez des règles d’exclusion d’alerte lorsque vous souhaitez que Defender pour IoT ignore l’activité qui déclenchera une alerte.

Par exemple, si vous savez que tous les appareils OT surveillés par un capteur spécifique vont subir des procédures de maintenance pendant deux jours, vous pouvez définir une règle d’exclusion qui indique à Defender pour IoT de supprimer les alertes détectées par ce capteur au cours de la période prédéfinie.

### <a name="alert-exclusion-logic"></a>Logique d’exclusion d’alerte

La logique de la règle d’alerte est basée sur l’opérateur `AND`. Cela signifie qu’une alerte est déclenchée uniquement lorsque toutes les conditions de règle sont remplies.

Si une condition de règle n’est pas définie, la condition inclut toutes les options. Par exemple, si vous n’incluez pas le nom d’un capteur dans la règle, elle sera appliquée à tous les capteurs.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Capture d’écran de la vue Créer une règle d’exclusion.":::

Les résumés des règles s’affichent dans la fenêtre **Règle d’exclusion**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Capture d’écran de la vue Résumé de la règle d’exclusion.":::

Outre l’utilisation des règles d’exclusion, vous pouvez supprimer les alertes en les désactivant.

### <a name="create-exclusion-rules"></a>Créer des règles d’exclusion

**Pour créer des règles d’exclusion** :

1. Dans le volet gauche de la console de gestion locale, sélectionnez **Exclusion d’alerte**. Définissez une nouvelle règle d’exclusion en sélectionnant l’icône **Ajouter** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: dans le coin supérieur droit de la fenêtre qui s’ouvre. La boîte de dialogue **Créer une règle d’exclusion** s’ouvre.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Créez une exclusion d’alerte en renseignant les informations ici.":::

1. Entrez un nom de règle dans le champ **Nom**. Le nom ne peut pas contenir de guillemets (`"`).

1. Dans la section **Par fuseau horaire/période**, entrez une période dans un fuseau horaire spécifique. Utilisez cette fonctionnalité lorsqu’une règle d’exclusion est créée pour une période spécifique dans un fuseau horaire, mais qu’elle doit être implémentée en même temps dans d’autres fuseaux horaires. Par exemple, vous pouvez être amené à appliquer une règle d’exclusion comprise entre 8 h et 10 h dans trois fuseaux horaires différents. Dans ce cas, créez trois règles d’exclusion distinctes qui utilisent la même période et le fuseau horaire approprié.

1. Sélectionnez **AJOUTER**. Pendant la période d’exclusion, aucune alerte n’est créée sur les capteurs connectés.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Capture d’écran de la vue par période de temps.":::

1. Dans la section **Par adresse d’appareil**, définissez les éléments suivants :

  - Adresse IP, adresse MAC ou adresse de sous-réseau de l’appareil que vous souhaitez exclure.

  - Direction du trafic pour les appareils exclus, source et destination.

1. Sélectionnez **AJOUTER**.

1. Dans la section **Par titre d’alerte**, commencez à saisir le titre de l’alerte. Dans la liste déroulante, sélectionnez le titre ou les titres d’alerte à exclure.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Capture d’écran de l’affichage par titre d’alerte.":::

1. Sélectionnez **AJOUTER**.

1. Dans la section **Par nom de capteur**, commencez à saisir le nom du capteur. Dans la liste déroulante, sélectionnez le ou les capteurs que vous souhaitez exclure.

1. Sélectionnez **AJOUTER**.

1. Sélectionnez **SAVE** (Enregistrer). La nouvelle règle apparaît dans la liste des règles.

Pour supprimer des alertes, vous pouvez soit les désactiver, soit créer des règles d’exclusion d’alerte. Cette section décrit les cas d’utilisation potentiels pour ces deux fonctionnalités.

- **Règle d’exclusion**. Écrivez une règle d’exclusion dans les cas suivants :

  - Vous savez à l’avance que vous souhaitez exclure l’événement de la base de données. Par exemple, vous savez que le scénario détecté sur un certain capteur déclenchera des alertes non pertinentes. Par exemple, vous effectuerez des tâches de maintenance sur des PLC de l’organisation sur un site spécifique et souhaitez supprimer les alertes relatives aux PLC pour ce site.

  - Vous souhaitez que Defender pour IoT ignore les événements pour une plage de temps spécifique (pour les tâches de maintenance système).

  - Vous souhaitez ignorer les événements d’un sous-réseau spécifique.

  - Vous souhaitez contrôler les événements d’alerte générés à partir de plusieurs capteurs avec une seule règle.

  - Vous ne souhaitez pas suivre l’exclusion d’alerte en tant qu’événement dans le journal des événements.

- **Muet**. Désactivez une alerte lorsque :

  - Les éléments qui doivent être désactivés ne sont pas planifiés. Vous ne savez pas à l’avance quels événements ne seront pas pertinents.

  - Vous souhaitez supprimer l’alerte de la fenêtre **Alertes**, mais vous souhaitez tout de même la suivre dans le journal des événements.

  - Vous souhaitez ignorer les événements sur un canal spécifique.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Déclencher des règles d’exclusion d’alerte à partir de systèmes externes

Déclenchez des règles d’exclusion d’alerte à partir de systèmes externes. Par exemple, gérez des règles d’exclusion à partir de systèmes de gestion de tickets d’entreprise ou de systèmes qui gèrent les processus de maintenance réseau.

Définissez les capteurs, les moteurs, l’heure de début et l’heure de fin pour appliquer la règle. Pour plus d’informations, consultez [Capteur d’API Defender pour IoT et API de la console de gestion](references-work-with-defender-for-iot-apis.md).

Les règles que vous créez à l’aide de l’API s’affichent dans la fenêtre **Règle d’exclusion** sous la forme RO.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Capture d’écran de la vue Modifier la règle d’exclusion.":::

## <a name="next-steps"></a>Étapes suivantes

[Utilisez des alertes sur votre capteur](how-to-work-with-alerts-on-your-sensor.md).
Consultez les [alertes du moteur Defender pour IoT](alert-engine-messages.md).
