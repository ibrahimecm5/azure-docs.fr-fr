---
title: Connecter les données des événements de sécurité Windows à Azure Sentinel (version à onglets) | Microsoft Docs
description: Découvrez comment utiliser le connecteur d’événements de sécurité Windows pour streamer tous les événements de sécurité, depuis vos systèmes Windows jusqu’à votre espace de travail Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a95327518729b854fde1ca6fa22f5b78e65554f0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023412"
---
# <a name="connect-to-windows-servers-to-collect-security-events"></a>Se connecter aux serveurs Windows pour collecter des événements de sécurité

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Le connecteur d’événements de sécurité Windows vous permet de streamer des événements de sécurité depuis n’importe quel serveur Windows (physique ou virtuel, local ou dans un cloud) connecté à votre espace de travail Azure Sentinel. Cela vous permet d’afficher les événements de sécurité Windows dans vos tableaux de bord, de les utiliser pour créer des alertes personnalisées et de les utiliser pour améliorer vos investigations, ce qui vous donne plus d’informations sur le réseau de votre organisation et développe vos capacités d’opérations de sécurité.

## <a name="connector-options"></a>Options du connecteur

Le connecteur d’événements de sécurité Windows prend en charge les versions suivantes :

|Version du connecteur  |Description  |
|---------|---------|
|**Événements de sécurité**     |Version héritée, basée sur l’agent Log Analytics et parfois appelée Microsoft Monitoring Agent (MMA) ou agent OMS. <br><br>Limité à 10 000 événements par seconde. Pour garantir des performances optimales, veillez à vous en tenir à 8 500 événements par seconde au maximum.       |
|**Événements de sécurité Windows**     |Version plus récente **(désormais en disponibilité générale)** basée sur l’agent Azure Monitor (AMA).   <br><br>Prend en charge des fonctionnalités supplémentaires, telles que le filtrage de journaux avant ingestion et les règles de collecte de données individuelles pour certains groupes d’ordinateurs.      |
|     |         |


> [!NOTE]
> MMA pour Linux ne prend pas en charge l’hébergement multiple, qui permet d’envoyer des journaux à plusieurs espaces de travail. Si vous avez besoin d’un hébergement multiple, nous vous recommandons d’utiliser le connecteur d’**événements de sécurité Windows**.

> [!TIP]
> Si vous avez besoin de plusieurs agents, vous souhaiterez peut-être utiliser un groupe identique de machines virtuelles défini afin d’exécuter plusieurs agents pour l’ingestion de journaux, ou utiliser plusieurs ordinateurs. Vous pouvez ensuite utiliser les versions de l’ancien agent et AMA des connecteurs d’événements de sécurité Windows avec un équilibreur de charge pour vous assurer que les ordinateurs ne sont pas surchargés et empêcher la duplication de données.
>

Cet article présente des informations sur les deux versions du connecteur. Choisissez parmi les onglets ci-dessous pour afficher les informations adaptées au connecteur sélectionné.

# <a name="security-events-via-legacy-agent"></a>[Événements de sécurité via l’ancien agent](#tab/LAA)

Vous pouvez sélectionner les événements à transmettre en continu à partir des ensembles suivants : <a name="event-sets"></a>

- **Tous les événements** : tous les événements AppLocker et de sécurité Windows.
- **Courant** : un ensemble d’événements à des fins d’audit. Une piste d’audit utilisateur complète est fournie dans cet ensemble. Par exemple, il contient les événements de connexion et de déconnexion de l’utilisateur (ID d’événement 4624, 4634). Il existe également des actions d’audit, telles que les modifications de groupe de sécurité, les opérations Kerberos du contrôleur de domaine clé et les autres événements alignés avec les meilleures pratiques acceptées.

    L’ensemble d’événements **Common** peut contenir certains types d’événements qui ne sont pas si courants.  Cela est dû au fait que le point principal de l’ensemble **Common** est de réduire le volume d’événements à un niveau plus gérable, tout en conservant la fonctionnalité de piste d’audit complète.

- **Minimal** : un petit ensemble d’événements qui peuvent indiquer des menaces potentielles. Cet ensemble ne contient pas de piste d’audit complète. Il couvre uniquement les événements qui peuvent indiquer une violation avérée et d’autres événements importants qui ont un taux d’occurrence très faible. Par exemple, il contient des connexions utilisateur ayant réussi et ayant échoué (ID d’événement 4624, 4625), mais il ne contient pas d’informations de déconnexion (4634), qui sont importantes pour l’audit, mais pas pour la détection de violation, et dont le volume est relativement élevé. Le plus gros du volume de données de cet ensemble est constitué d’événements de connexion et d’événements de création de processus (ID d’événement 4688).

- **Aucun** : aucun événement AppLocker ni de sécurité. (Ce paramètre est utilisé pour désactiver le connecteur.)

> [!NOTE]
> La collecte des événements de sécurité dans le contexte d’un seul espace de travail peut être configurée à partir d’Azure Security Center ou d’Azure Sentinel, mais pas les deux à la fois. Si vous intégrez Azure Sentinel dans un espace de travail qui reçoit déjà des alertes Azure Defender et qui est défini pour collecter des événements de sécurité, vous disposez de deux options :
> - Laissez la collecte d’événements de sécurité dans Azure Security Center telle quelle. Vous êtes alors en mesure d’interroger et d’analyser ces événements dans Azure Sentinel ainsi que dans Azure Defender. Toutefois, vous n’êtes pas en mesure de surveiller l’état de connectivité du connecteur ou de modifier sa configuration dans Azure Sentinel. Si cela est important pour vous, envisagez la deuxième option.
>
> - [Désactivez la collecte des événements de sécurité](../security-center/security-center-enable-data-collection.md) dans Azure Security Center, puis ajoutez le connecteur Événements de sécurité dans Azure Sentinel. Comme pour la première option, vous êtes en mesure d’interroger et d’analyser les événements à la fois dans Azure Sentinel et Azure Defender/ASC. Cependant, vous pouvez maintenant surveiller l’état de connectivité du connecteur ou modifier sa configuration dans, et uniquement dans, Azure Sentinel.

# <a name="windows-security-events-via-azure-monitor-agent-ama"></a>[Événements de sécurité Windows via l’agent Azure Monitor (AMA)](#tab/AMA)

> [!NOTE]
>
> - Le connecteur de données d’événements de sécurité Windows basé sur l’agent Azure Monitor (AMA) est désormais en disponibilité générale.

L’[agent Azure Monitor](../azure-monitor/agents/azure-monitor-agent-overview.md) utilise des **règles de collecte de données (DCR)** pour définir les données à collecter depuis chaque agent. Les règles de collecte de données vous permettent de gérer les paramètres de collecte à grande échelle, tout en continuant d’autoriser des configurations uniques, étendues pour les sous-ensembles de machines. Elles ne dépendent pas de l’espace de travail ni de la machine virtuelle, ce qui signifie qu’elles peuvent être définies une seule fois et être réutilisées sur d’autres machines et environnements. Consultez [Configurer la collecte de données pour l’agent Azure Monitor](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

En plus des jeux d’événements présélectionnés (**Tous les événements**, **Minimal** ou **Courant**) que vous pouvez choisir d’ingérer avec l’ancien connecteur, les **règles de collecte de données** vous donnent la possibilité de créer des filtres personnalisés pour choisir les événements exacts que vous souhaitez ingérer. L’agent Azure Monitor utilise ces règles pour filtrer les données *à la source*, et ingérer uniquement les événements qui vous intéressent tout en laissant les autres données de côté. Vous pouvez réaliser des économies substantielles en matière d’ingestion de données avec cette solution !

Ce document vous montre comment créer des règles de collecte de données.

> [!NOTE]
> - **Coexistence avec d’autres agents**
> 
>   L’agent Azure Monitor peut coexister avec les agents existants, et vous pouvez donc continuer à utiliser le connecteur hérité pendant l’évaluation ou la migration. Vous devez être prudent lors de la collecte de données en double, car cela peut fausser les résultats de la requête et entraîner des frais supplémentaires pour l’ingestion et la rétention de données.
> 
> - **Collecter des événements de sécurité à partir de machines non-Azure**
> 
>   Pour collecter des événements de sécurité à partir d’un système qui n’est pas une machine virtuelle Azure, [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) doit être installé et activé sur le système *avant* d’activer le connecteur basé sur l’agent Azure Monitor.
>   
>   notamment :
>   
>    - Les serveurs Windows installés sur des machines physiques
>    - Les serveurs Windows installés sur des machines virtuelles locales
>    - Les serveurs Windows installés sur des machines virtuelles dans des clouds non-Azure

---

## <a name="set-up-the-windows-security-events-connector"></a>Configurer le connecteur d’événements de sécurité Windows

Pour collecter vos événements de sécurité Windows dans Azure Sentinel, suivez les instructions figurant sous l’onglet approprié, en fonction de la version du connecteur que vous souhaitez déployer :

# <a name="security-events-via-legacy-agent"></a>[Événements de sécurité via l’ancien agent](#tab/LAA)

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**. Dans la liste des connecteurs, sélectionnez **Événements de sécurité via l’ancien agent**, puis **Ouvrir la page du connecteur** dans le volet Détails. Suivez ensuite les instructions à l’écran sous l’onglet **Instructions**, comme décrit dans le reste de cette section.

1. Vérifiez que vous disposez des autorisations appropriées, comme décrit dans la section **Prérequis** sur la page du connecteur.

1. Téléchargez et installez l’[agent Log Analytics](../azure-monitor/agents/log-analytics-agent.md) (également appelé Microsoft Monitoring Agent ou MMA) sur les machines pour lesquelles vous souhaitez diffuser des événements de sécurité dans Azure Sentinel.

    Pour les machines virtuelles Azure :
    
    1. Sélectionnez **Installer l’agent sur la machine virtuelle Windows Azure**, puis sur le lien qui apparaît dessous.
    1. Pour chaque machine virtuelle que vous souhaitez connecter, sélectionnez son nom dans la liste qui s’affiche à droite, puis sélectionnez **Connecter**.

    Pour les machines Windows non-Azure (physiques, virtuelles locales ou virtuelles dans un autre cloud) :

    1. Sélectionnez **Installer l’agent sur une machine virtuelle Windows non-Azure**, puis sur le lien qui apparaît dessous.
    1. Sélectionnez les liens de téléchargement appropriés qui s’affichent à droite, sous **Ordinateurs Windows**.
    1. À l’aide du fichier exécutable téléchargé, installez l’agent sur les systèmes Windows de votre choix et configurez-le à l’aide de l’**ID et des clés de l’espace de travail** qui s’affichent sous les liens de téléchargement mentionnés ci-dessus.

    > [!NOTE]
    >
    > Pour permettre aux systèmes Windows qui n’ont pas la connexion Internet nécessaire de diffuser en continu des événements vers Azure Sentinel, téléchargez et installez la passerelle **OMS** sur un ordinateur distinct, en utilisant le lien situé en bas à droite pour l’utiliser en tant que proxy.  Vous devrez toujours installer l’agent Log Analytics sur chaque système Windows dont vous souhaitez collecter les événements.
    >
    > Pour plus d’informations sur ce scénario, consultez la documentation relative à la [**passerelle Log Analytics**](../azure-monitor/agents/gateway.md).

    Pour obtenir des options d’installation supplémentaires et d’autres informations, consultez la documentation sur l’[**agent Log Analytics**](../azure-monitor/agents/agent-windows.md).

1. Sélectionnez l’ensemble d’événements (All, Common ou Minimal) que vous souhaitez diffuser. Consultez les [listes d’ID d’événement inclus](#event-id-reference) dans les jeux d’événements Minimal et Courant.

1. Sélectionnez **Mettre à jour**.

1. Pour utiliser le schéma approprié dans Log Analytics pour les événements de sécurité Windows, saisissez `SecurityEvent` dans la fenêtre de requête.

# <a name="windows-security-events-via-azure-monitor-agent-ama"></a>[Événements de sécurité Windows via l’agent Azure Monitor (AMA)](#tab/AMA)

1. Dans le menu de navigation d’Azure Sentinel, sélectionnez **Connecteurs de données**. Dans la liste des connecteurs, cliquez sur **Événements de sécurité via AMA**, puis sur le bouton **Ouvrir la page du connecteur** dans l’angle inférieur droit. Suivez ensuite les instructions à l’écran sous l’onglet **Instructions**, comme décrit dans le reste de cette section.

1. Vérifiez que vous disposez des autorisations appropriées, comme décrit dans la section **Prérequis** sur la page du connecteur.

    1. Vous devez disposer d’autorisations en lecture et en écriture sur votre espace de travail et sur toutes les sources de données à partir desquelles vous allez ingérer des événements de sécurité Windows.

    1. Pour collecter des événements à partir d’ordinateurs Windows qui ne sont pas des machines virtuelles Azure, Azure Arc doit être installé et activé sur ces ordinateurs (physiques ou virtuels). [Plus d’informations](../azure-monitor/agents/azure-monitor-agent-install.md)

1. Sous **Configuration**, sélectionnez **+Ajouter une règle de collecte de données**. L’Assistant **Création d’une règle de collecte de données** s’ouvre à droite.

1. Sous **De base**, entrez un **Nom de règle** et spécifiez un **Abonnement** et un **Groupe de ressources** dans lesquels la règle de collecte de données sera créée. Il *n’est pas* nécessaire que le groupe de ressources ou que l’abonnement soient les mêmes que ceux dans lesquels se trouvent les machines supervisées et leurs associations, tant qu’ils se trouvent dans le même locataire.

1. Sous l’onglet **Ressources**, sélectionnez **+Ajouter une ou plusieurs ressources** pour ajouter des machines auxquelles va s’appliquer la règle de collecte de données. La boîte de dialogue **Sélectionner une étendue** s’ouvre et la liste des abonnements disponibles s’affiche. Développez un abonnement pour voir ses groupes de ressources, puis développez un groupe de ressources pour voir les ordinateurs disponibles. Vous verrez des machines virtuelles Azure et des serveurs Azure Arc dans la liste. Vous pouvez activer les cases à cocher des abonnements ou des groupes de ressources pour sélectionner toutes les machines qu’ils contiennent, ou sélectionner des machines individuelles. Sélectionnez **Appliquer** lorsque vous avez choisi toutes vos machines. À la fin de ce processus, l’agent Azure Monitor sera installé sur les ordinateurs sélectionnés qui en étaient dépourvus.

1. Sous l’onglet **Collecter**, choisissez le [Jeu d’événements](#event-id-reference) à collecter, ou sélectionnez **Personnaliser** pour indiquer d’autres journaux ou filtrer des événements à l’aide des [Requêtes XPath](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries). Dans la zone, entrez des expressions qui correspondent à des critères XML spécifiques pour les événements à collecter, puis sélectionnez **Ajouter**. Vous pouvez entrer jusqu’à 20 expressions dans une seule zone, et jusqu’à 100 zones dans une règle.

    Apprenez-en davantage sur les [Règles de collecte de données](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr), dans la documentation Azure Monitor.

    > [!NOTE]
    > - Veillez à interroger uniquement les journaux de sécurité Windows et les journaux AppLocker. Les événements des autres journaux Windows, ou des journaux de sécurité provenant d’autres environnements, peuvent ne pas adhérer au schéma des événements de sécurité Windows, et donc ne pas être correctement analysés ; dans ce cas ils ne seront pas ingérés dans votre espace de travail.
    >
    > - L’agent Azure Monitor prend en charge les requêtes XPath pour **[XPath version 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) uniquement**.

1. Une fois que vous avez ajouté toutes les expressions de filtre souhaitées, sélectionnez **Suivant : Vérifier + créer**.

1. Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**. 

Vous verrez toutes vos règles de collecte de données (y compris celles créées via l’API) sous **Configuration** dans la page du connecteur. À partir de là, vous pouvez modifier ou supprimer des règles existantes.

> [!TIP]
> Utilisez l’applet de commande PowerShell **Get-WinEvent** avec le paramètre *-FilterXPath* pour tester la validité d’une requête XPath. Le script suivant donne un exemple :
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - Si des événements sont retournés, la requête est valide.
> - Si vous recevez le message *Aucun événement correspondant aux critères de sélection spécifiés n’a été trouvé*, il se peut que la requête soit valide, mais qu’il n’existe aucun événement correspondant sur l’ordinateur local.
> - Si vous recevez le message *La requête spécifiée n’est pas valide*, la syntaxe de la requête n’est pas valide.

### <a name="create-data-collection-rules-using-the-api"></a>Créer des règles de collecte de données à l’aide de l’API

Vous pouvez également créer des règles de collecte de données à l’aide de l’API ([voir le schéma](/rest/api/monitor/data-collection-rules)), ce qui peut simplifier votre travail si vous créez de nombreuses règles (si vous êtes un fournisseur MSSP, par exemple). Voici un exemple que vous pouvez utiliser comme modèle pour créer une règle :

**URL et en-tête de requête**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```
**URL et en-tête de requête**

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



---

## <a name="validate-connectivity"></a>Valider la connectivité

Environ 20 minutes peuvent être nécessaires avant que vos journaux commencent à apparaître dans Log Analytics. 

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>Configurer le connecteur d’événements de sécurité / d’événements de sécurité Windows pour la détection de connexion RDP anormale

> [!IMPORTANT]
> La détection de connexion Remote Desktop Protocol (RDP) anormale est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel peut appliquer du Machine Learning (ML) aux données Événements de sécurité pour identifier une activité de connexion RDP anormale. Il s’agit entre autres des scénarios suivants :

- **IP inhabituelle** : l’adresse IP a rarement ou n’a jamais été observée au cours des 30 derniers jours

- **Emplacement géographique inhabituel** : l’adresse IP, la ville, le pays et l’ASN ont rarement ou n’ont jamais été observés au cours des 30 derniers jours

- **Nouvel utilisateur** : un nouvel utilisateur se connecte à partir d’une adresse IP et d’un emplacement géographique, dont les deux ou l’un des deux n’étaient pas censés être vus sur la base des données des 30 jours précédents.

**Instructions de configuration**

1. Vous devez collecter les données de connexion RDP (ID d’événement 4624) par le biais des connecteurs de données **Événements de sécurité via l’ancien agent** ou **Événements de sécurité Windows via AMA**. Assurez-vous que vous avez sélectionné un [jeu d’événements](#event-id-reference) en plus de « Aucun », ou que vous avez créé une règle de collecte de données qui comprend cet ID d’événement, afin de transmettre en continu dans Azure Sentinel.

1. Dans le portail Azure Sentinel, cliquez sur **Analytics**, puis sur l’onglet **Modèles de règles**. Choisissez la règle **Détection de connexion RDP anormale (préversion)** et déplacez le curseur **État** sur **Activé**.

    > [!NOTE]
    > Comme l’algorithme de Machine Learning nécessite 30 jours de données pour établir un profil de base du comportement de l’utilisateur, vous devez autoriser la collecte de 30 jours de données d’événements de sécurité Windows avant que tout incident puisse être détecté.

## <a name="event-id-reference"></a>Référence de ID de l'événement

La liste suivante fournit le détail complet des ID d’événement App Locker et de sécurité pour chaque ensemble :

| Ensemble d’événements | ID d’événements collectés |
| --- | --- |
| **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **Commun** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les événements de sécurité Windows à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](get-visibility.md).
- Commencez à détecter les menaces avec Azure Sentinel à l’aide de règles [intégrées](detect-threats-built-in.md) ou [personnalisées](detect-threats-custom.md).
