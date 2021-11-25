---
title: Connecter Microsoft Sentinel à des services Azure, Windows et Microsoft
description: Découvrez comment connecter Microsoft Sentinel aux services cloud Azure et Microsoft 365, ainsi qu’aux journaux des événements Windows Server.
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79007f7716a8a24350bbfa6083b9891c425c120e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523027"
---
# <a name="connect-microsoft-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Connecter Microsoft Sentinel à des services Azure, Windows, Microsoft et Amazon

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel se base sur Azure afin de fournir une prise en charge service à service intégrée de l’ingestion de données à partir de nombreux services Azure et Microsoft 365, d’Amazon Web Services et de divers services Windows Server. Plusieurs méthodes permettent d’établir ces connexions et cet article explique comment procéder.

Cet article décrit les types de connecteurs suivants :

- Connexions **basées sur une API**
- Connexions de **paramètres de diagnostic**, dont certaines sont gérées par Azure Policy
- Connexions basées sur un **agent Log Analytics**

Cet article présente des informations communes aux groupes de connecteurs. Pour obtenir des informations propres à chaque connecteur, telles que les conditions préalables pour les licences et les tables Log Analytics pour le stockage des données, consultez la page de [référence du connecteur de données](data-connectors-reference.md) associée.

Les intégrations suivantes sont à la fois plus uniques et plus populaires, et sont traitées individuellement avec leurs propres articles :

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Microsoft Defender pour le cloud](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Événements de sécurité Windows](connect-windows-security-events.md)
- [Amazon Web Services (AWS) CloudTrail](connect-aws.md)

## <a name="api-based-connections"></a>Connexions basées sur une API

### <a name="prerequisites"></a>Configuration requise

- Vous devez disposer d’autorisations en lecture et en écriture dans l’espace de travail Log Analytics.
- Vous devez disposer du rôle Administrateur général ou Administrateur de la sécurité sur le locataire de votre espace de travail Microsoft Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre service dans la galerie de connecteurs de données, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Sélectionnez **Connexion** pour démarrer la diffusion en continu d’événements ou d’alertes à partir de votre service dans Microsoft Sentinel.

1. Si la page du connecteur contient une section intitulée **Créer des incidents (recommandé)** , sélectionnez **Activer** si vous souhaitez créer automatiquement des incidents à partir d’alertes.

Vous pouvez rechercher et interroger les données pour chaque service à l’aide des noms de table qui s’affichent dans la section du connecteur du service dans la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="diagnostic-settings-based-connections"></a>Connexions basées sur des paramètres de diagnostic

La configuration de certains connecteurs de ce type est gérée par Azure Policy. Sélectionnez l’onglet **Azure Policy** ci-dessous pour obtenir des instructions. Pour les autres connecteurs de ce type, sélectionnez l’onglet **Autonome**.

# <a name="standalone"></a>[Autonome](#tab/SA)

### <a name="prerequisites"></a>Prérequis

Pour ingérer des données dans Microsoft Sentinel :

- Vous devez disposer d’autorisations d’accès en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre type de ressource dans la galerie de connecteurs de données, puis choisissez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, sélectionnez le lien pour ouvrir la page de configuration des ressources.

    Si une liste de ressources du type souhaité s’affiche, sélectionnez le lien d’une ressource dont vous souhaitez ingérer les journaux.

1. Dans le menu de navigation de la ressource, sélectionnez **Paramètres de diagnostic**.

1. Sélectionnez **+ Ajouter un paramètre de diagnostic** en bas de la liste.

1. Dans l’écran **Paramètres de diagnostic**, entrez un nom dans le champ **Nom des paramètres de diagnostic**.

    Activez la case à cocher **Envoyer à Log Analytics**. Deux nouveaux champs s’affichent sous celle-ci. Choisissez les valeurs **Abonnement** et **Espace de travail Log Analytics** appropriées (où se trouve Microsoft Sentinel).

1. Activez les cases à cocher des types de journaux et de métriques que vous souhaitez collecter. Consultez nos choix recommandés pour chaque type de ressource dans la section pour le connecteur de la ressource dans la page [Référence des connecteurs de données](data-connectors-reference.md).

1. En haut de l’écran, sélectionnez **Enregistrer**.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>Prérequis

Pour ingérer des données dans Microsoft Sentinel :

- Vous devez disposer d’autorisations d’accès en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

- Pour utiliser Azure Policy afin d’appliquer une stratégie de streaming de journaux à vos ressources, vous devez avoir le rôle Propriétaire pour l’étendue d’attribution de stratégie.

### <a name="instructions"></a>Instructions

Les connecteurs de ce type utilisent Azure Policy pour appliquer une configuration de paramètres de diagnostic unique à une collection de ressources d’un type unique, définie en tant qu’étendue. Vous pouvez voir les types de journaux ingérés à partir d’un type de ressource donné sur le côté gauche de la page du connecteur pour cette ressource, sous **Types de données**.

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre type de ressource dans la galerie de connecteurs de données, puis choisissez **Ouvrir la page du connecteur** dans le volet de visualisation.

1. Dans la section **Configuration** de la page du connecteur, développez toutes les sections que vous voyez, puis sélectionnez le bouton **Lancer l’Assistant Attribution Azure Policy**.

    L’Assistant Attribution de stratégie s’ouvre, prêt à créer une stratégie, avec un nom de stratégie prérempli.

    1. Sous l’onglet **Informations de base**, sous **Étendue**, cliquez sur le bouton avec les points de suspension pour sélectionner votre abonnement (et éventuellement un groupe de ressources). Vous pouvez également ajouter une description.

    1. Dans l’onglet **Paramètres** :
       - Désactivez la case à cocher **Afficher uniquement les paramètres nécessitant une entrée**.
       - Si **Effet** figure dans des champs **Nom du paramètre**, laissez ceux-ci tels quels.
       - Choisissez votre espace de travail Microsoft Sentinel dans la liste déroulante **Espace de travail Log Analytics**.
       - Les champs de liste déroulante restants représentent les types de journaux de diagnostic disponibles. Conservez la valeur « True » pour tous les types de journaux que vous souhaitez ingérer.

    1. La stratégie sera appliquée aux ressources ajoutées à l’avenir. Pour appliquer la stratégie également à vos ressources existantes, sélectionnez l’onglet **Correction** et cochez la case **Créer une tâche de correction**.

    1. Sous l’onglet **Vérifier + créer**, cliquez sur **Créer**. Votre stratégie est maintenant affectée à l’étendue que vous avez choisie.

---

> [!NOTE]
>
> Avec ce type de connecteur de données, les indicateurs d’état de connectivité (une bande de couleur dans la galerie des connecteurs de données et des icônes de connexion à côté des noms de types de données) s’affichent comme *connectés* (en vert) uniquement si les données ont été ingérées au cours des 14 derniers jours. Après 14 jours passés sans ingestion de données, le connecteur sera déconnecté. Quand plus de données arrivent, l’état *connecté* est renvoyé.

Vous pouvez rechercher et interroger les données pour chaque type de ressource à l’aide des noms de table qui s’affichent dans la section du connecteur de la ressource dans la page [Référence des connecteurs de données](data-connectors-reference.md).

## <a name="windows-agent-based-connections"></a>Connexions basées sur l’agent Windows

# <a name="azure-monitor-agent"></a>[Agent Azure Monitor](#tab/AMA)

> [!IMPORTANT]
>
> - Certains connecteurs basés sur l’agent Azure Monitor (AMA) sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

L’[agent Azure Monitor](../azure-monitor/agents/azure-monitor-agent-overview.md) utilise des **règles de collecte de données (DCR)** pour définir les données à collecter à partir de chaque agent. Les règles de collecte de données offrent deux avantages distincts :

- **Gérer les paramètres de collecte à grande échelle**, tout en continuant à autoriser des configurations uniques, étendues pour les sous-ensembles de machines. Elles ne dépendent pas de l’espace de travail ni de la machine virtuelle, ce qui signifie qu’elles peuvent être définies une seule fois et être réutilisées sur d’autres machines et environnements. Consultez [Configurer la collecte de données pour l’agent Azure Monitor](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

- **Créer des filtres personnalisés** pour choisir les événements exacts que vous souhaitez ingérer. L’agent Azure Monitor utilise ces règles pour filtrer les données *à la source*, et ingérer uniquement les événements qui vous intéressent tout en laissant les autres données de côté. Vous pouvez réaliser des économies substantielles en matière d’ingestion de données avec cette solution !

Voir ci-dessous comment créer des règles de collecte de données.

### <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations d’accès en lecture et en écriture dans l’espace de travail Microsoft Sentinel.

- Pour collecter des événements à partir d’un système qui n’est pas une machine virtuelle Azure, il faut qu’[**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) soit installé et activé sur le système *avant* d’activer le connecteur basé sur l’agent Azure Monitor.

  notamment :

  - Les serveurs Windows installés sur des machines physiques
  - Les serveurs Windows installés sur des machines virtuelles locales
  - Les serveurs Windows installés sur des machines virtuelles dans des clouds non-Azure

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**. Sélectionnez votre connecteur dans la liste, puis sélectionnez **Ouvrir la page du connecteur** dans le volet de détails. Suivez ensuite les instructions à l’écran sous l’onglet **Instructions**, comme décrit dans le reste de cette section.

1. Vérifiez que vous disposez des autorisations appropriées, comme décrit dans la section **Prérequis** sur la page du connecteur.

1. Sous **Configuration**, sélectionnez **+Ajouter une règle de collecte de données**. L’Assistant **Création d’une règle de collecte de données** s’ouvre à droite.

1. Sous **De base**, entrez un **Nom de règle** et spécifiez un **Abonnement** et un **Groupe de ressources** dans lesquels la règle de collecte de données sera créée. Il *n’est pas* nécessaire que le groupe de ressources ou que l’abonnement soient les mêmes que ceux dans lesquels se trouvent les machines supervisées et leurs associations, tant qu’ils se trouvent dans le même locataire.

1. Sous l’onglet **Ressources**, sélectionnez **+Ajouter une ou plusieurs ressources** pour ajouter des machines auxquelles va s’appliquer la règle de collecte de données. La boîte de dialogue **Sélectionner une étendue** s’ouvre et la liste des abonnements disponibles s’affiche. Développez un abonnement pour voir ses groupes de ressources, puis développez un groupe de ressources pour voir les ordinateurs disponibles. Vous verrez des machines virtuelles Azure et des serveurs Azure Arc dans la liste. Vous pouvez activer les cases à cocher des abonnements ou des groupes de ressources pour sélectionner toutes les machines qu’ils contiennent, ou sélectionner des machines individuelles. Sélectionnez **Appliquer** lorsque vous avez choisi toutes vos machines. À la fin de ce processus, l’agent Azure Monitor sera installé sur les ordinateurs sélectionnés qui en étaient dépourvus.

1. Sous l’onglet **Collecter**, choisissez les événements à collecter : sélectionnez **Tous les événements** ou **Personnaliser** pour indiquer d’autres journaux ou pour filtrer des événements à l’aide de [requêtes XPath](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) (voir la remarque ci-dessous). Dans la zone, entrez des expressions qui correspondent à des critères XML spécifiques pour les événements à collecter, puis sélectionnez **Ajouter**. Vous pouvez entrer jusqu’à 20 expressions dans une seule zone, et jusqu’à 100 zones dans une règle.

    Apprenez-en davantage sur les [Règles de collecte de données](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr), dans la documentation Azure Monitor.

    > [!NOTE]
    >
    > - Le connecteur Événements de sécurité Windows propose deux autres [**jeux d’événements prédéfinis**](windows-security-event-id-reference.md) que vous pouvez choisir de collecter : **Courant** et **Minimal**.
    >
    > - L’agent Azure Monitor prend en charge les requêtes XPath pour **[XPath version 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) uniquement**.

1. Une fois que vous avez ajouté toutes les expressions de filtre souhaitées, sélectionnez **Suivant : Vérifier + créer**.

1. Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**. 

Vous verrez toutes vos règles de collecte de données (y compris celles créées via l’API) sous **Configuration** dans la page du connecteur. À partir de là, vous pouvez modifier ou supprimer des règles existantes.

> [!TIP]
> Utilisez l’applet de commande PowerShell **Get-WinEvent** avec le paramètre *-FilterXPath* pour tester la validité d’une requête XPath. Le script suivant donne un exemple :
>
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Si des événements sont retournés, la requête est valide.
> - Si vous recevez le message « Aucun événement correspondant aux critères de sélection spécifiés n’a été trouvé », il se peut que la requête soit valide, mais qu’il n’existe aucun événement correspondant sur l’ordinateur local.
> - Si vous recevez le message « La requête spécifiée n’est pas valide », la syntaxe de la requête n’est pas valide.

### <a name="create-data-collection-rules-using-the-api"></a>Créer des règles de collecte de données à l’aide de l’API

Vous pouvez également créer des règles de collecte de données à l’aide de l’API ([voir le schéma](/rest/api/monitor/data-collection-rules)), ce qui peut simplifier votre travail si vous créez de nombreuses règles (si vous êtes un fournisseur MSSP, par exemple). Voici un exemple (pour le connecteur [Événements de sécurité Windows via AMA](data-connectors-reference.md#windows-security-events-via-ama)) que vous pouvez utiliser comme modèle pour créer une règle :

**URL et en-tête de requête**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```

**Corps de la demande**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```

Consultez cette [description complète des règles de collecte de données](../azure-monitor/agents/data-collection-rule-overview.md) dans la documentation Azure Monitor.

# <a name="log-analytics-agent-legacy"></a>[Agent Log Analytics (hérité)](#tab/LAA)

### <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’autorisations de lecture et d’écriture sur l’espace de travail Log Analytics, ainsi que sur tout espace de travail contenant des machines dont vous souhaitez collecter les journaux.
- Vous devez avoir le rôle **Contributeur Log Analytics** sur la solution SecurityInsights (Microsoft Sentinel) de ces espaces de travail, en plus des rôles Microsoft Sentinel.

### <a name="instructions"></a>Instructions

1. Dans le menu de navigation de Microsoft Sentinel, sélectionnez **Connecteurs de données**.

1. Sélectionnez votre service (**DNS** ou **Pare-feu Windows**), puis choisissez **Ouvrir la page du connecteur**.

1. Installez et intégrez l’agent sur l’appareil qui génère les journaux.

    | Type de machine  | Instructions  |
    | --------- | --------- |
    | **Pour une machine virtuelle Azure Windows** | 1. Sous **Choisissez l’emplacement d’installation de l’agent**, développez **Installer l’agent sur une machine virtuelle Azure Windows**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines virtuelles Windows Azure >** . <br><br>3. Dans le panneau **Machines virtuelles**, sélectionnez une machine virtuelle sur laquelle installer l’agent, puis choisissez **Connecter**. Répétez cette étape pour chaque machine virtuelle à laquelle vous souhaitez vous connecter. |
    | **Pour toute autre machine Windows** | 1. Sous **Choisissez l’emplacement d’installation de l’agent**, développez **Installer l’agent sur un ordinateur non Azure Windows**. <br><br>2. Sélectionnez le lien **Télécharger et installer l’agent pour les machines Windows non-Azure >** .  <br><br>3. Dans le panneau **Agents de gestion**, sous l’onglet **Serveurs Windows**, sélectionnez le lien **Télécharger l’agent Windows** pour les systèmes 32 bits ou 64 bits, selon le cas.  <br><br>4. À l’aide du fichier exécutable téléchargé, installez l’agent sur les systèmes Windows de votre choix et configurez-le à l’aide de l’**ID et des clés de l’espace de travail** qui s’affichent sous les liens de téléchargement mentionnés à l’étape précédente. |
    | | |

> [!NOTE]
>
> Pour permettre aux systèmes Windows qui n’ont pas la connexion Internet nécessaire de continuer à transmettre des événements à Microsoft Sentinel, téléchargez et installez la **passerelle Log Analytics** sur un ordinateur distinct en utilisant le lien **Télécharger la passerelle Log Analytics** sur la page **Gestion des agents** afin de l’utiliser en tant que proxy.  Vous devez toujours installer l’agent Log Analytics sur chaque système Windows dont vous souhaitez collecter les événements.
>
> Pour plus d’informations sur ce scénario, consultez la documentation relative à la [**passerelle Log Analytics**](../azure-monitor/agents/gateway.md).

Pour obtenir des options d’installation supplémentaires et d’autres informations, consultez la documentation sur l’[**agent Log Analytics**](../azure-monitor/agents/agent-windows.md).


#### <a name="determine-the-logs-to-send"></a>Déterminer les journaux à envoyer

Pour les connecteurs Serveur DNS Windows et Pare-feu Windows, cliquez sur le bouton **Installer la solution**. Pour le connecteur Événements de sécurité hérité, choisissez le [**jeu d’événements**](windows-security-event-id-reference.md) que vous souhaitez envoyer et sélectionnez **Mettre à jour**.

Vous pouvez rechercher et interroger les données de ces services à l’aide des noms de table dans leurs sections respectives, dans la page de [référence des connecteurs de données](data-connectors-reference.md).

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter des services Azure, Microsoft et Windows, ainsi qu’Amazon Web Services, à Microsoft Sentinel. 
- Découvrez les [connecteurs de données Microsoft Sentinel](connect-data-sources.md) en général.
- [Recherchez votre connecteur de données Microsoft Sentinel](data-connectors-reference.md).
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Prise en main de la [détection des menaces avec Microsoft Sentinel](detect-threats-built-in.md).
