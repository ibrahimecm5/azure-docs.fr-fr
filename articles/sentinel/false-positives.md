---
title: Gérer les faux positifs dans Microsoft Azure Sentinel
description: Découvrez comment résoudre les faux positifs dans Microsoft Sentinel en créant des règles d’automatisation ou en modifiant des règles d’analyse pour spécifier des exceptions.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 009cb9d66f4294bc72bafdcf515a68131d9b7e38
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521156"
---
# <a name="handle-false-positives-in-microsoft-sentinel"></a>Gérer les faux positifs dans Microsoft Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Les [règles d’analyse de Microsoft Sentinel](detect-threats-built-in.md) vous avertissent lorsque quelque chose de suspect se produit sur votre réseau. Aucune règle analytique n’est parfaite et il est possible que vous receviez des faux positifs qu’il vous faudra gérer. Cet article explique comment gérer les faux positifs à l’aide de l’automatisation ou en modifiant les règles analytiques planifiées.

## <a name="false-positive-causes-and-prevention"></a>Causes et prévention des faux positifs

Même en présence d’une règle analytique correctement créée, les faux positifs proviennent souvent d’entités spécifiques telles que des utilisateurs ou adresses IP qui devraient en être exclus.

Scénarios courants :

- Les activités normales de certains utilisateurs, généralement les principaux de service, présentent un modèle qui semble suspect.
- L’activité d’analyse de sécurité intentionnelle provenant d’adresses IP connues est détectée comme malveillante.
- Une règle qui exclut les adresses IP privées doit également exclure certaines adresses IP internes non privées.

Cet article décrit deux méthodes permettant d’éviter les faux positifs :

- Les **règles d’automatisation** créent des exceptions sans modifier les règles analytiques.
- Les **modifications planifiées des règles analytiques** autorisent des exceptions plus détaillées et permanentes.

Le tableau suivant décrit les caractéristiques de chaque méthode.


|Méthode|Caractéristique|
|-|-|
|**Règles d’automatisation**|<ul><li>Peuvent s’appliquer à plusieurs règles analytiques.</li><li>Conservent une piste d’audit. Les exceptions empêchent la création d’incidents, mais les alertes sont toujours enregistrées à des fins d’audit.</li><li>Sont souvent générées par des analystes.</li><li>Autorisent l’application d’exceptions pendant une période limitée. Par exemple, le travail de maintenance peut déclencher des faux positifs qui, en dehors de la période de maintenance, constitueraient de véritables incidents.</li></ul>|
|**Modifications des règles analytiques**|<ul><li>Autorisent les expressions booléennes avancées et les exceptions basées sur le sous-réseau.</li><li>Vous permettent d’utiliser des watchlists pour centraliser la gestion des exceptions.</li><li>Nécessitent généralement une implémentation par des ingénieurs du centre d’opérations de sécurité (SOC).</li><li>Constituent la solution la plus souple et la plus complète en matière de faux positifs, mais se révèlent plus complexes.</li></ul>|

## <a name="add-exceptions-by-using-automation-rules"></a>Ajouter des exceptions à l’aide de règles d’automatisation

La façon la plus simple d’ajouter une exception consiste à [Ajouter une règle d’automatisation](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) lorsque vous constatez un incident de faux positif.

Pour ajouter une règle d’automatisation afin de gérer un faux positif :

1. Dans Microsoft Sentinel, sous **Incidents**, sélectionnez l’incident pour lequel vous souhaitez créer une exception.
1. Sélectionnez **Créer une règle d’automatisation**.
1. Dans la barre latérale **Créer une règle d’automatisation**, vous pouvez modifier le nom de la nouvelle règle plutôt que le nom de la règle d’alerte pour identifier l’exception.
1. Sous **Conditions**, vous pouvez ajouter plusieurs **noms de règles analytiques** auxquelles ajouter l’exception.
1. La barre latérale présente les entités spécifiques de l’incident susceptible d’avoir provoqué le faux positif. Conservez les suggestions automatiques ou modifiez-les pour affiner l’exception. Par exemple, vous pouvez modifier une condition sur une adresse IP à appliquer à un sous-réseau entier.

   :::image type="content" source="media/false-positives/create-rule.png" alt-text="Capture d’écran montrant comment créer une règle d’automatisation pour un incident dans Microsoft Sentinel.":::

1. Après avoir défini le déclencheur, vous pouvez continuer à définir l’objectif de la règle :

   :::image type="content" source="media/false-positives/apply-rule.png" alt-text="Capture d’écran montrant comment terminer la création et l’application d’une règle d’automatisation dans Microsoft Sentinel.":::

   - La règle est déjà configurée pour fermer un incident répondant aux critères de l’exception.
   - Vous pouvez ajouter un commentaire à l’incident automatiquement fermé expliquant l’exception. Par exemple, vous pouvez indiquer que l’incident est lié à une activité administrative connue.
   - Par défaut, la règle est configurée pour expirer automatiquement après 24 heures. Cet expiration correspond peut-être à vos attentes et réduit les risques d’erreurs de faux négatifs. Pour une exception plus longue, définissez **Expiration de la règle** sur une période ultérieure.

1. Sélectionnez **Appliquer** pour activer l’exception.

> [!TIP]
> Vous pouvez également créer une règle d’automatisation de zéro, sans utiliser d’incident. Sélectionnez **Automatisation** dans le menu de navigation gauche de Microsoft Sentinel, puis **Créer** > **Ajouter une nouvelle règle**.

## <a name="add-exceptions-by-modifying-analytics-rules"></a>Ajouter des exceptions en modifiant les règles analytiques

Pour implémenter des exceptions, une autre option consiste à modifier la requête de règle analytique. Vous pouvez inclure des exceptions directement dans la règle, ou de préférence, si possible, utiliser une référence à une [watchlist](watchlists.md#use-watchlists-in-analytics-rules). Vous pouvez ensuite gérer la liste d’exceptions dans la watchlist.

### <a name="modify-the-query"></a>Modifier la requête

Pour modifier des règles d’analyse existantes, sélectionnez **Automatisation** dans le menu de navigation gauche de Microsoft Sentinel. Sélectionnez la règle que vous souhaitez modifier, puis **Modifier** en bas à droite pour ouvrir l'**Assistant règles analytiques**.

Pour obtenir des instructions détaillées sur l’utilisation de l’**Assistant Règles analytiques** afin de créer et de modifier des règles analytiques, consultez [Créer des règles analytiques personnalisées pour détecter des menaces](detect-threats-custom.md).

Pour implémenter une exception dans un préambule de règle classique, vous pouvez ajouter une condition comme `where IPAddress !in ('<ip addresses>')` près du début de la requête de règle. Cette ligne exclut des adresses IP spécifiques de la règle.

```kusto
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in ('10.0.0.8', '192.168.12.1')
...
```

Ce type d’exception ne se limite pas aux adresses IP. Vous pouvez exclure des utilisateurs spécifiques à l’aide du champ `UserPrincipalName` ou des applications spécifiques à l’aide de `AppDisplayName`.

Vous pouvez également exclure plusieurs attributs. Par exemple, pour exclure les alertes de l’adresse IP `10.0.0.8` ou de l’utilisateur `user@microsoft.com`, utilisez :

```kusto
| where IPAddress !in ('10.0.0.8')
| where UserPrincipalName != 'user@microsoft.com'
```

Pour implémenter une exception affinée, le cas échéant, et réduire le risque de faux négatifs, vous pouvez combiner des attributs. L’exception suivante s’applique uniquement si les deux valeurs s’affichent dans la même alerte :

```kusto
| where IPAddress != '10.0.0.8' and UserPrincipalName != 'user@microsoft.com'
```

### <a name="exclude-subnets"></a>Exclure sous-réseaux

L’exclusion des plages d’adresses IP utilisées par une organisation implique l’exclusion du sous-réseau. L'exemple suivant montre comment exclure des sous-réseaux.

L'opérateur `ipv4_lookup` est un opérateur d’enrichissement, et non un opérateur de filtrage. La ligne `where isempty(network)` effectue le filtrage en inspectant les événements qui n’affichent pas de correspondance.

```kusto
let subnets = datatable(network:string) [ "111.68.128.0/17", "5.8.0.0/19", ...];
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| evaluate ipv4_lookup(subnets, IPAddress, network, return_unmatched = true)
| where isempty(network)
...
```

### <a name="use-watchlists-to-manage-exceptions"></a>Utiliser des watchlists pour gérer les exceptions

Vous pouvez utiliser une watchlist pour gérer la liste des exceptions en dehors de la règle elle-même. Le cas échéant, cette solution présente les avantages suivants :

- Un analyste peut ajouter des exceptions sans modifier la règle, ce qui est davantage conforme aux meilleures pratiques SOC.
- La même watchlist peut s’appliquer à plusieurs règles, activant ainsi la gestion centralisée des exceptions.

L’utilisation d’une watchlist s’apparente à l’utilisation d’une exception directe. Utilisez `_GetWatchlist('<watchlist name>')` pour appeler la watchlist :

```kusto
let timeFrame = 1d;
let logonDiff = 10m;
let allowlist = (_GetWatchlist('ipallowlist') | project IPAddress);
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in (allowlist)
...
```

Vous pouvez également effectuer un filtrage de sous-réseau à l’aide d’une watchlist. Par exemple, dans le code d’exclusion des sous-réseaux précédents, vous pouvez remplacer la définition des sous-réseaux `datatable` par une watchlist :

```kusto
let subnets = _GetWatchlist('subnetallowlist');
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :
- [Utiliser les données UEBA pour analyser les faux positifs](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [Automatiser la gestion des incidents dans Microsoft Sentinel avec des règles d’automatisation](automate-incident-handling-with-automation-rules.md)
- [Créer des règles d’analytique personnalisées pour détecter des menaces](detect-threats-custom.md)
- [Utiliser les listes de surveillance Microsoft Sentinel](watchlists.md)
