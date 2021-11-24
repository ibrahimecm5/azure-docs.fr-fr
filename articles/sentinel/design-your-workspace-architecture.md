---
title: Concevoir votre architecture d’espace de travail Microsoft Sentinel | Microsoft Docs
description: Utilisez un arbre de décision pour comprendre comment vous pouvez concevoir votre architecture d’espace de travail Microsoft Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0fccafee7a645be13ae17eca98d99305dd2b4a10
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521545"
---
# <a name="design-your-microsoft-sentinel-workspace-architecture"></a>Concevoir votre architecture d’espace de travail Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Cet article fournit un arbre de décision pour vous aider à prendre les décisions importantes concernant la conception de votre architecture d’espace de travail Microsoft Sentinel. Pour plus d’informations, consultez [Exemples de conception d’espaces de travail Microsoft Sentinel](sample-workspace-designs.md) et [Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel](best-practices-workspace-architecture.md).

## <a name="prerequisites"></a>Configuration requise

Avant d’utiliser l’arbre de décision, vérifiez que vous disposez des informations suivantes :

|Configuration requise  | Description |
|---------|---------|
|**Exigences réglementaires relatives à la résidence des données Azure**     |  Microsoft Sentinel peut s’exécuter sur les espaces de travail dans la plupart des régions [prises en charge en disponibilité générale pour Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), mais pas toutes. L’intégration du service Microsoft Sentinel aux nouvelles régions Log Analytics prises en charge peut prendre un certain temps. <br><br> Les données générées par Microsoft Sentinel, comme les incidents, les signets et les règles d’analyses, peuvent contenir des données client provenant des espaces de travail Log Analytics du client.<br><br> Pour plus d’informations, consultez [Disponibilité géographique et résidence des données](quickstart-onboard.md#geographical-availability-and-data-residency).|
|**Sources de données**     |   Découvrez les [sources de données](connect-data-sources.md) dont vous avez besoin pour vous connecter, y compris les connecteurs intégrés aux solutions Microsoft et non-Microsoft. Vous pouvez également utiliser le format CEF (Common Event Format), Syslog ou l’API REST pour connecter vos sources de données à Microsoft Sentinel. <br><br>Si vous avez des machines virtuelles Azure dans plusieurs emplacements Azure dont vous avez besoin pour collecter les journaux et que l’enregistrement sur le coût de sortie des données est important pour vous, vous devez calculer le coût de sortie des données à l’aide de la [calculatrice de prix de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/#overview) pour chaque emplacement Azure.      |
|**Rôles d’utilisateur et niveaux d’accès aux données/autorisations**     |    Microsoft Sentinel utilise le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) pour fournir des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être attribués à des utilisateurs, des groupes et des services dans Azure. <br><br>Tous les rôles intégrés Microsoft Sentinel accordent un accès en lecture aux données de votre espace de travail Microsoft Sentinel. Par conséquent, vous devez déterminer s’il est nécessaire de contrôler l’accès aux données par source de données ou au niveau de la ligne, car cela aura un impact sur la décision de conception de l’espace de travail. Pour plus d’informations, consultez [Rôles personnalisés dans le contrôle d’accès en fonction du rôle Azure](roles.md#custom-roles-and-advanced-azure-rbac).     |
|**Débit d’ingestion des données**     |  Le débit d’ingestion quotidien, généralement en Go/jour, est l’un des aspects clés de la gestion des coûts et des considérations relatives à la planification et à la conception d’espace de travail pour Microsoft Sentinel. <br><br>Dans la plupart des environnements cloud et hybrides, les périphériques réseau, tels que les pare-feu ou les proxys, ainsi que les serveurs Windows et Linux, produisent les données les plus ingérées. Pour obtenir les résultats les plus précis, Microsoft recommande un inventaire exhaustif des sources de données. <br><br>L’outil de [calcul de coûts](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1) Microsoft Sentinel peut également inclure des tables utiles pour l’estimation de l’empreinte des sources de données. <br><br>**Important** : Ces estimations constituent un point de départ et les paramètres de verbosité des journaux et la charge de travail produira des variances. Nous vous recommandons de surveiller régulièrement votre système pour effectuer le suivi des modifications. Une surveillance régulière est recommandée en fonction de votre scénario. <br><br>Pour plus d’informations, consultez [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](../azure-monitor/logs/manage-cost-storage.md).       |
|     |         |

## <a name="decision-tree"></a>Arbre de décision

L’illustration suivante montre un organigramme de l’arbre de décision complet pour vous aider à comprendre comment concevoir au mieux votre espace de travail.

[![Arbre de décision de conception d’espace de travail Microsoft Sentinel.](media/best-practices/workspace-decision-tree.png)](media/best-practices/workspace-decision-tree.png#lightbox)

Les sections suivantes fournissent une version en texte intégral de cet arbre de décision, y compris les notes suivantes référencées à partir de l’image :

[Note 1](#note1) | [Note 2](#note2)  | [Note 3](#note3)  | [Note 4](#note4)  | [Note 5](#note5)  | [Note 6](#note6)  | [Note 7](#note7)  | [Note 8](#note8)  | [Note 9](#note9) | [Note 10](#note10)

### <a name="step-1-new-or-existing-workspace"></a>Étape 1 : espace de travail nouveau ou existant ?

Disposez-vous d’un espace de travail existant que vous pouvez utiliser pour Microsoft Sentinel ?

- **Si cela n’est pas le cas et que vous allez créer un espace de travail** de toute façon, passez directement à l’[étape 2](#step-2-keeping-data-in-different-azure-geographies).

- **Si vous avez un espace de travail existant** que vous pouvez utiliser, réfléchissez à la quantité de données à ingérer.

  - **Si vous allez ingérer *plus* de 100 Go/jour**, nous vous recommandons d’utiliser un espace de travail distinct par souci de rentabilité.

  - **Si vous allez ingérer *moins* de 100 Go/jour**, passez à l’[étape 2](#step-2-keeping-data-in-different-azure-geographies) pour davantage d’évaluation. Prenez de nouveau cette question en considération lorsqu’elle se présentera à l’[étape 5](#step-5-collecting-any-non-soc-data).

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>Étape 2 : conservation des données dans différentes zones géographiques Azure ?

- **Si vous avez des exigences réglementaires vous obligeant à conserver les données dans différentes zones géographiques Azure**, utilisez un espace de travail Microsoft Sentinel distinct pour chaque région Azure présentant des exigences de conformité. Pour plus d’informations, consultez [Éléments à prendre en compte au sujet des régions](best-practices-workspace-architecture.md#region-considerations).

- **Si vous n’avez pas besoin de conserver les données dans diverses régions géographiques Azure**, passez à l’[étape 3](#step-3-do-you-have-multiple-azure-tenants).

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>Étape 3 : avez-vous plusieurs locataires Azure ?

- **Si vous n’avez qu’un seul locataire**, passez directement à l’[étape 4](#step-4-splitting-billing--charge-back).

- **Si vous avez plusieurs locataires Azure**, déterminez si vous collectez des journaux spécifiques à une limite d’un locataire, par exemple Office 365 ou Microsoft 365 Defender.

  - **Si vous n’avez aucun journal spécifique à un locataire**, passez directement à l’[étape 4](#step-4-splitting-billing--charge-back).

  - **Si vous *collectez* des journaux spécifiques aux locataires**, utilisez un espace de travail Microsoft Sentinel distinct pour chaque locataire Azure AD. Passez à l’[étape 4](#step-4-splitting-billing--charge-back) pour d’autres points à prendre en considération.

    <a name="note1"></a>[Remarque 1 concernant l’arbre de décision](#decision-tree) : les journaux spécifiques aux limites du locataire, provenant par exemple d’Office 365 et Microsoft Defender for Cloud, peuvent uniquement être stockés dans un espace de travail au sein du même locataire.

    Bien qu’il soit *possible* d’utiliser des collecteurs personnalisés pour collecter des journaux spécifiques du locataire à partir d’un espace de travail dans un autre locataire, nous ne recommandons pas ceci en raison des inconvénients suivants :

    - Les données collectées par les connecteurs personnalisés sont ingérées dans des tables personnalisées. Par conséquent, vous ne pourrez pas utiliser toutes les règles et tous les classeurs intégrés.
    - Les tables personnalisées ne sont pas prises en compte par certaines des fonctionnalités intégrées, telles que les UEBA et les règles de Machine Learning.
    - Coût et effort supplémentaires requis pour les connecteurs personnalisés, tels que l’utilisation d’Azure Functions et de Logic Apps.

    Si ces inconvénients ne sont pas un problème pour votre organisation, passez à l’[étape 4](#step-4-splitting-billing--charge-back) au lieu d’utiliser des espaces de travail Microsoft Sentinel distincts.

### <a name="step-4-splitting-billing--charge-back"></a>Étape 4 : fractionnement de la facturation/de la rétrofacturation ?

Si vous avez besoin de fractionner votre facturation ou rétrofacturation, déterminez si les rapports d’utilisation ou le tarif croisé manuel fonctionnent pour vous.

- **Si vous *n’avez pas* besoin de fractionner votre facturation ou rétrofacturation**, passez à l’[étape 5](#step-5-collecting-any-non-soc-data).

- **Si vous *avez* besoin de fractionner votre facturation ou rétrofacturation**, déterminez si les [rapports d’utilisation ou la facturation croisée manuelle](azure-sentinel-billing.md) fonctionnent pour vous.

  - **Si les rapports d’utilisation ou la facturation croisée manuelle fonctionne pour vous**, passez à l’[étape 5](#step-5-collecting-any-non-soc-data).

  - **Si *ni* les rapports d’utilisation ni la facturation croisée manuelle ne fonctionneront pour vous**, utilisez un espace de travail Microsoft Sentinel distinct pour chaque propriétaire de coût.

  <a name="note2"></a>[Remarque 2 concernant l’arbre de décision](#decision-tree) : pour plus d’informations, consultez [Coûts et facturation Microsoft Sentinel](azure-sentinel-billing.md).

### <a name="step-5-collecting-any-non-soc-data"></a>Étape 5 : collecte des données non-SOC ?

- **Si vous ne collectez aucune données non-SOC**, telles que des données opérationnelles, vous pouvez passer directement à l’[étape 6](#step-6-multiple-regions).

- **Si vous *collectez* des données non-SOC**, prenez en considération s’il y a des chevauchements ou non, où la même source de données est requises pour les données SOC et non-SOC.

  **Si vous *avez* des chevauchements entre les données SOC et non-SOC**, traitez les données qui se chevauchent comme des données SOC uniquement. Déterminez ensuite si l’ingestion *à la fois* des données SOC et non-SOC individuellement est inférieure à 100 Go/jour, mais plus de 100 Go/jour lorsqu’elles sont combinées :

  - **Oui** : passez à l’[étape 6](#step-6-multiple-regions) pour davantage d’évaluation.
  - **Non** : nous vous déconseillons d’utiliser le même espace de travail pour des raisons de rentabilité. Passez à l’[étape 6](#step-6-multiple-regions) pour davantage d’évaluation.

  Dans les deux cas, pour plus d'informations, consultez la [note 10](#note10).

  **Si vous n’avez *aucune* données se chevauchant**, déterminez si l’ingestion *à la fois* des données SOC et non-SOC individuellement est inférieure à 100 Go/jour, mais plus de 100 Go/jour lorsqu’elles sont combinées :

  - **Oui** : passez à l’[étape 6](#step-6-multiple-regions) pour davantage d’évaluation. Pour plus d'informations, consultez la [note 3](#combining-your-soc-and-non-soc-data).
  - **Non** : nous vous déconseillons d’utiliser le même espace de travail pour des raisons de rentabilité. Passez à l’[étape 6](#step-6-multiple-regions) pour davantage d’évaluation.

#### <a name="combining-your-soc-and-non-soc-data"></a>Combinaison de vos données SOC et non-SOC

<a name="note3"></a>[Remarque 3 concernant l’arbre de décision](#decision-tree) : bien que nous recommandons généralement aux clients de conserver un espace de travail distinct pour leurs données non-SOC afin qu’elles ne soient pas soumises aux coûts Microsoft Sentinel, il peut y avoir des situations où la combinaison de données SOC et non-SOC est moins coûteuse que de les séparer.

Par exemple, imaginons une organisation dont les journaux de sécurité sont ingérés à 50 Go/jour, les journaux des opérations sont ingérés à 50 Go/jour et qui a un espace de travail dans la région USA Est.

Le tableau suivant compare les options de l’espace de travail avec et sans espaces de travail distincts.

> [!NOTE]
> Les coûts et les termes répertoriés dans le tableau ci-dessous sont fictifs et utilisés à des fins d’exemple uniquement. Pour obtenir des informations à jour sur les coûts, consultez la calculatrice de prix Microsoft Sentinel.
>

|Architecture de l’espace de travail  |Description |
|---------|---------|
|L’équipe SOC a son propre espace de travail, avec Microsoft Sentinel activé. <br><br>L’équipe Ops a son propre espace de travail, avec Microsoft Sentinel désactivé.     |  **Équipe SOC** : <br>Le coût de Microsoft Sentinel pour 50 Go/jour est de 6 500 USD par mois.<br>Les trois premiers mois de rétention sont gratuits. <br><br>**Équipe Ops** :<br>– Le coût de Log Analytics à 50 Go/jour est d’environ 3 500 USD par mois.<br>– Les 31 premiers jours de rétention sont gratuits.<br><br>Le coût total pour les deux est égal à 10 000 USD par mois.       |
|Les équipes SOC et Ops partagent le même espace de travail avec Microsoft Sentinel activé. |En combinant les deux journaux, l’ingestion sera de 100 Go/jour, éligibles pour le niveau d’engagement (50 % pour Sentinel et 15 % pour LA).       <br><br>Le coût de Microsoft Sentinel pour 100 Go/jour est égal à 9 000 USD par mois.      |
|     |         |

Dans cet exemple, vous obtiendrez des économies de 1 000 USD par mois en combinant les deux espaces de travail, et l’équipe Ops profitera également de 3 mois de rétention gratuits au lieu de 31 jours.

Cet exemple est pertinent uniquement si à la fois les données SOC et non-SOC ont une taille d’ingestion de >=50 Go/jour et <100 Go/jour.

<a name="note10"></a>[Remarque 10 concernant l’arbre de décision](#decision-tree) : nous vous recommandons d’utiliser un espace de travail distinct pour les données non-SOC afin qu’elles ne soient pas soumises aux coûts de Microsoft Sentinel.

Toutefois, cette suggestion pour les espaces de travail distincts pour les données non-SOC provient d’un point de vue purement basé sur les coûts, et il existe d’autres facteurs de conception clés à examiner pour déterminer s’il faut utiliser un ou plusieurs espaces de travail. Pour éviter des coûts d’ingestion double, envisagez de collecter des données avec chevauchement sur un seul espace de travail avec un contrôle d’accès en fonction du rôle Azure au niveau de la table.

### <a name="step-6-multiple-regions"></a>Étape 6 : plusieurs régions ?

- **Si vous collectez des journaux à partir de machines virtuelles Azure dans une *seule* région uniquement**, passez directement à l’[étape 7](#step-7-segregating-data-or-defining-boundaries-by-ownership).

- **Si vous collectez des journaux à partir de machines virtuelles Azure dans *plusieurs* régions**, dans quelle mesure êtes-vous concerné par le coût de sortie des données ?

  <a name="note4"></a>[Note 4 de l’arbre de décision](#decision-tree) : la sortie de données fait référence au [coût de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) pour déplacer les données en dehors des centres de données Azure. Pour plus d’informations, consultez [Éléments à prendre en compte au sujet des régions](best-practices-workspace-architecture.md#region-considerations).

  - Si la réduction de la quantité d’effort nécessaire pour gérer des espaces de travail distincts est une priorité, passez à l'[étape 7](#step-7-segregating-data-or-defining-boundaries-by-ownership).

  - Si le coût de sortie des données est suffisant pour préférer la gestion des espaces de travail distincts, utilisez un espace de travail Microsoft Sentinel distinct pour chaque région dans laquelle vous devez réduire le coût de sortie des données.

    <a name="note5"></a>[Note 5 de l’arbre de décision](#decision-tree) : nous vous recommandons d’avoir le moins d’espaces de travail possible. Utilisez la [calculatrice de prix Azure](azure-sentinel-billing.md#estimate-microsoft-sentinel-costs) pour estimer le coût et déterminer les régions dont vous avez réellement besoin, et combinez les espaces de travail pour les régions avec des coûts de sortie faibles. Les coûts de bande passante ne peuvent représenter qu’une petite partie de votre facture Azure par rapport aux coûts d’ingestion Log Analytics et Microsoft Sentinel distincts.

    Par exemple, votre coût peut être estimé comme suit :

    - 1 000 machines virtuelles, chacune générant 1 Go/jour ;
    - Envoi de données d’une région des États-Unis vers une région en Europe ;
    - Utilisation d’un taux de compression de 2:1 dans l’agent

    Le calcul de ce coût estimé est le suivant : `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`

    Cet exemple de coût serait beaucoup moins onéreux en comparaison avec les coûts mensuels d’un espace de travail Microsoft Sentinel et Log Analytics distinct.

    > [!NOTE]
    > Les coûts répertoriés sont fictifs et sont utilisés à des fins d’exemple uniquement. Pour obtenir des informations à jour sur les coûts, consultez la calculatrice de prix Microsoft Sentinel.
    >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>Étape 7 : séparation des données ou définition des limites en propriété ?

- **Si vous *n’avez pas* besoin de séparer les données ou de définir des limites de propriété**, passez directement à l’[étape 8](#step-8-controlling-data-access-by-data-source--table).

- **Si vous *n’avez pas* besoin de séparer les données ou de définir les limites en fonction de la propriété**, est-ce que chaque propriétaire de données a besoin d’utiliser le portail Microsoft Sentinel ?

  - Si chaque propriétaire de données doit avoir accès au portail Microsoft Sentinel, utilisez un espace de travail Microsoft Sentinel distinct pour chaque propriétaire.

    <a name="note6"></a>[Remarque 6 concernant l’arbre de décision](#decision-tree) : l’accès au portail Microsoft Sentinel requiert que chaque utilisateur ait au moins le rôle de [lecteur Microsoft Sentinel](../role-based-access-control/built-in-roles.md), et dispose des autorisations de **lecteur** pour toutes les tables dans l’espace de travail. Si un utilisateur n’a pas accès à toutes les tables de l’espace de travail, il doit utiliser Log Analytics pour accéder aux journaux dans les requêtes de recherche.

  - Si l’accès aux journaux via Log Analytics suffit pour les propriétaires qui n’ont pas accès au portail Microsoft Sentinel, passez à l’[étape 8](#step-8-controlling-data-access-by-data-source--table).

  Pour plus d’informations, consultez [Autorisations dans Microsoft Sentinel](roles.md).

### <a name="step-8-controlling-data-access-by-data-source--table"></a>Étape 8 : contrôle de l’accès aux données par la source de données/table ?

- **Si vous *n’avez pas* besoin de contrôler l’accès aux données par la source ou la table**, utilisez un seul espace de travail Microsoft Sentinel.

- **Si vous *avez* besoin de contrôler l’accès aux données par la source ou la table**, envisagez d’utiliser le [contrôle d’accès en fonction du rôle selon la ressource](resource-context-rbac.md) dans les situations suivantes :

  - Si vous avez besoin de contrôler l’accès au niveau de la ligne, par exemple en fournissant plusieurs propriétaires sur chaque source de données ou table

  - Si vous disposez de plusieurs sources de données/tables personnalisées, chacune d’elles nécessitant des autorisations distinctes

  Dans d’autres cas, lorsque vous n’avez *pas* besoin de contrôler l’accès au niveau de la ligne, fournissez plusieurs tables/sources de données personnalisées avec des autorisations distinctes, utilisez un espace de travail Microsoft Sentinel unique, avec un contrôle d’accès en fonction du rôle au niveau de la table pour contrôler l’accès aux données.

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>Éléments à prendre en considération pour le contexte de ressource ou le contrôle d’accès en fonction du rôle au niveau de la table

Lorsque vous envisagez d’utiliser le contexte de ressource ou le contrôle d’accès en fonction du rôle au niveau de la table, tenez compte des éléments suivants :

- <a name="note7"></a>[Remarque 7 concernant l’arbre de décision](#decision-tree) : pour configurer le contrôle d’accès en fonction du rôle de contexte de ressource pour les ressources n’appartenant pas à Azure, vous pouvez associer un ID de ressource aux données lors de l’envoi à Microsoft Sentinel, afin que l’étendue de l’autorisation puisse être définie à l’aide du contrôle d’accès en fonction du rôle de contexte de ressource. Pour plus d’informations, consultez [Configurer explicitement un RBAC dans le contexte de la ressource](resource-context-rbac.md#explicitly-configure-resource-context-rbac) et [Modes d’accès par déploiement](../azure-monitor/logs/design-logs-deployment.md).

- <a name="note8"></a>[Note 8 de l’arbre de décision](#decision-tree) : [Les autorisations de ressource](../azure-monitor/logs/manage-access.md) ou le [contexte de la ressource](../azure-monitor/logs/design-logs-deployment.md) permettent aux utilisateurs d’afficher les journaux uniquement pour les ressources auxquelles ils ont accès. Le mode d’accès à l’espace de travail doit être défini sur les **autorisations de ressources utilisateur ou d’espace de travail**. Seules les tables relatives aux ressources pour lesquelles l’utilisateur dispose d’autorisations sont incluses dans les résultats de la recherche à partir de la page **Journaux** dans Microsoft Sentinel.

- <a name="note9"></a>[Note 9 de l’arbre de décision](#decision-tree) : [Le contrôle d’accès en fonction du rôle au niveau de la table](../azure-monitor/logs/manage-access.md) vous permet de définir un contrôle plus précis sur les données dans un espace de travail Log Analytics en plus des autres autorisations. Avec ce contrôle, vous pouvez définir des types de données spécifiques qui sont accessibles uniquement à un ensemble spécifique d’utilisateurs. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle au niveau de la table dans Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des exemples pratiques de cet arbre de décision, consultez [Exemples de conceptions d’espaces de travail Microsoft Sentinel](sample-workspace-designs.md).

Pour plus d'informations, consultez les pages suivantes :

- [Meilleures pratiques pour l’architecture de l’espace de travail Microsoft Sentinel](best-practices-workspace-architecture.md)
- [Meilleures pratiques pour Microsoft Sentinel](best-practices.md)
