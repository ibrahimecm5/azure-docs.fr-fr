---
title: Analyseurs ASIM (Advanced SIEM Information Model) | Microsoft Docs
description: Cet article explique comment utiliser les fonctions KQL comme analyseurs au moment de la requête pour implémenter le modèle ASIM (Advanced SIEM Information Model)
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 43ec5162b0dc1bfd9def8afea6a36c7899483a55
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520291"
---
# <a name="advanced-siem-information-model-asim-parsers-public-preview"></a>Analyseurs du modèle ASIM (Azure SIEM Information Model) (préversion publique)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dans Microsoft Sentinel, l’analyse et la [normalisation](normalization.md) se produisent au moment de la requête. Les analyseurs sont créés en tant que [fonctions définies par l’utilisateur KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions) qui transforment les données des tables existantes, telles que **CommonSecurityLog**, les tables de journaux personnalisées ou Syslog, dans le schéma normalisé. Une fois l’analyseur enregistré en tant que fonction de l’espace de travail, il peut être utilisé comme n’importe quelle autre table Microsoft Sentinel.

> [!TIP]
> Regardez également le [webinaire de formation approfondie sur la normalisation des analyseurs et le contenu normalisé Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM). Pour plus d’informations, consultez [Étapes suivantes](#next-steps).
>

> [!IMPORTANT]
> ASIM n’est actuellement disponible qu’en PRÉVERSION. Les [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluent des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore disponibles dans la version en disponibilité générale.
>

## <a name="source-agnostic-and-source-specific-parsers"></a>Analyseurs agnostiques et spécifiques de la source

ASIM comprend deux niveaux d’analyseur : Analyseurs **indépendants de la source** et **spécifiques de la source** :

### <a name="source-agnostic-parsers"></a>Analyseurs indépendants de la source

Un **analyseur indépendant de la source** combine toutes les sources normalisées dans le même schéma et peut être utilisé pour interroger toutes les sources avec des champs normalisés. Le nom de l’analyseur indépendant de la source est `im<schema>`, où `<schema>` correspond au schéma spécifique qu’il sert.

Par exemple, la requête suivante utilise l’analyseur DNS indépendant de la source pour interroger les événements DNS en utilisant les champs normalisés `ResponseCodeName`, `SrcIpAddr` et `TimeGenerated` :

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

Un analyseur indépendant de la source peut combiner plusieurs analyseurs normalisés spécifiques de la source en utilisant l’opérateur KQL `union`. Le nom d’un analyseur normalisé spécifique de la source est `vim<schema><vendor><product>`. Ainsi, l’analyseur `imDns` se présente comme suit :

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> Lorsque vous utilisez les analyseurs agnostiques de la source ASIM, qui commencent par `im` sur la page **Journaux**, le sélecteur de plage horaire est défini sur `custom` . Vous pouvez toujours définir l’intervalle de temps vous-même. Vous pouvez également spécifier l’intervalle de temps à l’aide des paramètres de l’analyseur.
>
> Vous pouvez également utiliser un analyseur `ASim`, qui ne prend pas en charge les paramètres et ne fixe pas non plus la valeur par défaut du sélecteur de plage horaire sur `custom` .
>

### <a name="source-specific-parsers"></a>Analyseurs spécifiques de la source

L’ajout d’analyseurs normalisés **spécifiques de la source** à l’analyseur indépendant de la source vous permet d’inclure des sources personnalisées dans des requêtes intégrées qui utilisent les analyseurs indépendants de la source.

Les analyseurs spécifiques de la source vous permettent de tirer une valeur immédiate du contenu intégré, tel que l’analytique, les workbooks et les insights de vos données personnalisées.

Les analyseurs spécifiques de la source peuvent également être utilisés indépendamment. Par exemple, dans un workbook propre à Infoblox, utilisez l’analyseur `vimDnsInfobloxNIOS`.

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>Optimisation de l’analyse à l’aide de paramètres

L’utilisation d’analyseurs peut avoir un impact sur les performances de vos requêtes, principalement sur la nécessité de filtrer les résultats après l’analyse. Pour cette raison, de nombreux analyseurs ont des paramètres de filtrage facultatifs qui vous permettent de filtrer avant d’analyser et d’améliorer les performances des requêtes. Avec l’optimisation des requêtes et les efforts de pré-filtrage, les analyseurs ASIM offrent souvent de meilleures performances par rapport à la normalisation.

Utilisez des paramètres de filtrage en ajoutant un ou plusieurs paramètres nommés lors de l’appel de l’analyseur. Par exemple, le début de la requête suivante garantit que seules les requêtes DNS pour les domaines inexistants sont renvoyées :

```kusto
imDns(responsecodename='NXDOMAIN')
```

L’exemple précédent est semblable à la requête suivante, mais est bien plus efficace.

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

Chaque schéma a un ensemble standard de paramètres de filtrage qui sont documentés dans le document de schéma. Les paramètres de filtrage sont entièrement facultatifs et ne sont actuellement pris en charge que pour le schéma DNS. D’autres schémas prennent en charge les paramètres de filtrage standard sans optimisation préalable du filtrage.

## <a name="writing-source-specific-parsers"></a>Écriture d’analyseurs spécifiques de la source

Un analyseur est une requête KQL enregistrée en tant que fonction de l’espace de travail. Une fois enregistré, il peut être utilisé comme des tables intégrées. L’analyseur de requête comprend les parties suivantes :

**Filtrer** > **Analyser** > **Préparer les champs**

### <a name="filtering"></a>Filtrage

#### <a name="filtering-the-relevant-records"></a>Filtrage des enregistrements pertinents

Dans de nombreux cas, une table comprend plusieurs types d’événements. Par exemple :
* La table Syslog contient des données provenant de plusieurs sources.
* Les tables personnalisées peuvent inclure des informations provenant d’une source unique qui fournit plusieurs types d’événements et peut s’adapter à différents schémas.

Ainsi, un analyseur doit tout d’abord filtrer uniquement les enregistrements qui sont pertinents pour le schéma cible.

Le filtrage en KQL s’effectue à l’aide de l’opérateur `where`. Par exemple, **Sysmon event 1** signale la création de processus et doit être normalisé dans le schéma **ProcessEvent**. L’événement **Sysmon event 1** faisant partie de la table `Event`, le filtre suivant doit être utilisé :

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>Filtrage basé sur les paramètres de l’analyseur

Lorsque vous utilisez des [analyseurs paramétrés](#optimized-parsers), assurez-vous que votre analyseur accepte les paramètres de filtrage pour le schéma approprié, comme décrit dans l’article de référence pour ce schéma.

L’article de fonction est identique pour chaque schéma. Par exemple, pour la signature de l’analyseur paramétrable de la requête DNS :

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

Ajoutez vos filtres en fonction de vos valeurs de paramètres. Lors du filtrage, assurez-vous de :

- **Filtrez avant d’analyser à l’aide de champs physiques**. Si les résultats filtrés ne sont pas suffisamment précis, répétez le test après l’analyse pour affiner vos résultats. Pour plus d’informations, consultez  [« optimisation du filtrage »](#optimization).
 - **Ne filtrez pas si le paramètre n’est pas défini et possède toujours la valeur par défaut**. Les exemples suivants montrent comment implémenter le filtrage pour un paramètre de chaîne, où la valeur par défaut est généralement « \* » et, pour un paramètre de liste, où la valeur par défaut est généralement une liste vide.

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> Un analyseur existant du même type est un excellent début de l’implémentation du filtrage des paramètres.
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>Optimisation du filtrage


Pour garantir les performances de l’analyseur, notez les recommandations de filtrage suivantes :

-   Effectuez toujours le filtrage sur des champs intégrés plutôt que sur des champs analysés. Même s’il est parfois plus facile de filtrer avec des champs analysés, cela a un impact considérable sur les performances.
-   Utilisez des opérateurs qui fournissent des performances optimisées. En particulier, `==`, `has` et `startswith`. L’utilisation d’opérateurs tels que `contains` ou `matches regex` a également un impact considérable sur les performances.

Les recommandations de filtrage pour optimiser les performances peuvent ne pas toujours être faciles à suivre. Par exemple, l’utilisation de `has` est moins précise que `contains`. Dans d’autres cas, le fait de faire correspondre le champ intégré, tel `SyslogMessage`, est moins précis que la comparaison d’un champ extrait, tel que `DvcAction`. Dans ces cas, nous vous recommandons de toujours effectuer un préfiltrage avec un opérateur d’optimisation des performances sur un champ prédéfini et de répéter le filtre en utilisant des conditions plus précises après l’analyse.

Pour obtenir un exemple, consultez l’extrait de code d’analyseur [Infoblox DNS](https://aka.ms/AzSentinelInfobloxParser) suivant. L’analyseur vérifie d’abord que le champ SyslogMessage a (`has`) le mot `client`. Toutefois, le terme peut être utilisé ailleurs dans le message. Ainsi, après l’analyse du champ `Log_Type`, l’analyseur revérifie que le mot `client` était en effet la valeur du champ.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> Les analyseurs ne doivent pas filtrer en fonction de critères de temps, car la requête qui utilise l’analyseur le fait déjà.
>

### <a name="parsing"></a>Analyse

Une fois que la requête sélectionne les enregistrements appropriés, elle peut être amenée à les analyser. En règle générale, l’analyse est nécessaire si une grande partie des informations d’événement sont transmises dans un champ de texte unique.

Les opérateurs KQL qui effectuent l’analyse sont listés ci-dessous, classés par optimisation des performances. Le premier offre les performances les plus optimisées, tandis que le dernier offre les performances les moins optimisées.

|Opérateur  |Description  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    Analyser une chaîne de valeurs délimitées par un délimiteur     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     Analyser une chaîne de valeurs au format CSV (valeurs séparées par des virgules).    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    Analyser plusieurs valeurs à partir d’une chaîne arbitraire avec un modèle, qui peut être un modèle simplifié offrant de meilleures performances ou une expression régulière.     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | Analyser des valeurs uniques à partir d’une chaîne arbitraire avec une expression régulière. `extract_all` présente un niveau de performance similaire à `parse` si ce dernier utilise une expression régulière.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    Extraire une valeur unique à partir d’une chaîne arbitraire avec une expression régulière. <br><br>L’utilisation de `extract` offre de meilleures performances que `parse` ou `extract_all` si une seule valeur est nécessaire. Toutefois, l’utilisation de plusieurs activations de `extract` sur la même chaîne source est moins efficace qu’un seul `parse` ou `extract_all` et doit être évitée.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | Analyser les valeurs d’une chaîne au format JSON. Si seules quelques valeurs sont nécessaires à partir des données JSON, l’utilisation de `parse`, `extract` ou `extract_all` offre de meilleures performances.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    Analyser les valeurs d’une chaîne au format XML. Si seules quelques valeurs sont nécessaires à partir des données XML, l’utilisation de `parse`, `extract` ou `extract_all` offre de meilleures performances.     |
| | |

En plus d’analyser la chaîne, la phase d’analyse peut nécessiter plus de traitement des valeurs d’origine, notamment :

- **Mise en forme et conversion de type**. Une fois extrait, le champ source doit être mis en forme pour correspondre au champ de schéma cible. Par exemple, vous pouvez être amené à convertir une chaîne représentant la date et l’heure en un champ DateHeure.     Les fonctions telles que `todatetime` et `tohex` sont utiles dans ces cas.

- **Recherche de valeur**. La valeur du champ source, une fois extraite, peut devoir être mappée à l’ensemble de valeurs spécifié pour le champ de schéma cible. Par exemple, certaines sources signalent des codes de réponse DNS numériques, alors que le schéma impose les codes de réponse texte plus courants. Les fonctions `iff` et `case` peuvent être utiles pour mapper quelques valeurs.

    Par exemple, l’analyseur DNS Microsoft attribue le champ `EventResult` en fonction de l’ID d’événement et du code de réponse en utilisant une instruction `iff`, comme suit :

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    Pour plusieurs valeurs, utilisez `datatable` et `lookup`, comme illustré dans le même analyseur DNS :

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> La transformation n’autorise pas l’utilisation de `lookup` uniquement, car plusieurs valeurs sont mappées à `Reserved for Private Use` ou `Unassigned` ; ainsi, la requête utilise à la fois lookup et case.
> Même dans ce cas, la requête est encore plus efficace que l’utilisation de `case` pour toutes les valeurs.
>

### <a name="prepare-fields-in-the-result-set"></a>Préparer les champs du jeu de résultats

L’analyseur doit préparer les champs du jeu de résultats pour garantir que les champs normalisés sont utilisés. En règle générale, les champs d’origine qui ne sont pas normalisés ne doivent pas être supprimés du jeu de résultats à moins qu’il y ait une raison impérieuse de le faire, par exemple s’ils créent une confusion.

Les opérateurs KQL suivants sont utilisés pour préparer les champs :

|Opérateur  | Description  | Utilisation dans un analyseur  |
|---------|---------|---------|
|**extend**     | Crée des champs calculés et les ajoute à l’enregistrement        |  `Extend` est utilisé si les champs normalisés sont analysés ou transformés à partir des données d’origine. Pour plus d’informations, consultez la section [Analyse](#parsing) plus haut.     |
|**project-rename**     | Renomme les champs        |     Si un champ existe dans l’événement actuel et doit uniquement être renommé, utilisez `project-rename`. <br><br>Le champ renommé se comporte toujours comme un champ prédéfini, et les opérations sur le champ offrent de meilleures performances.   |
|**project-away**     |      Supprime des champs.   |Utilisez `project-away` pour des champs spécifiques que vous souhaitez supprimer du jeu de résultats.         |
|**project**     |  Sélectionne des champs qui étaient déjà existants ou qui ont été créés dans le cadre de l’instruction. Supprime tous les autres champs.       | Non recommandé pour une utilisation dans un analyseur, car ce dernier ne doit pas supprimer les autres champs qui ne sont pas normalisés. <br><br>Si vous devez supprimer des champs spécifiques, tels que les valeurs temporaires utilisées pendant l’analyse, utilisez `project-away` pour les supprimer des résultats.      |
| | | |

### <a name="handle-parsing-variants"></a>Gérer les variantes d’analyse

Dans de nombreux cas, les événements d’un flux d’événements incluent des variantes qui nécessitent une logique d’analyse différente.

Il est souvent tentant de créer un analyseur à partir de différents sous-analyseurs, chacun gérant une autre variante des événements qui a besoin d’une logique d’analyse différente. Ces sous-analyseurs, qui sont autant de requêtes, sont ensuite unifiés avec l’opérateur `union`. Cette approche, bien que pratique, n’est *pas* recommandée, car elle impacte de manière significative les performances de l’analyseur.

Quand vous gérez des variantes, suivez les indications suivantes :

|Scénario  |Gestion  |
|---------|---------|
|Les différentes variantes représentent *différents* types d’événements, généralement mappés à des schémas différents.     |  Utilisez des analyseurs distincts.       |
|Les différentes variantes représentent le *même* type d’événement, mais elles sont structurées différemment.     |   Si les variantes sont connues, par exemple quand il existe une méthode pour différencier les événements avant l’analyse, utilisez l’opérateur `case` pour sélectionner le `extract_all` approprié à exécuter et le mappage de champs, comme illustré dans l’[analyseur DNS Infoblox](https://aka.ms/AzSentinelInfobloxParser).      |
|Si `union` est inévitable.     |  Quand l’utilisation de `union` est inévitable, veillez à suivre les indications suivantes :<br><br>- Effectuez un pré-filtrage avec des champs prédéfinis dans chacune des sous-requêtes. <br>- Assurez-vous que les filtres s’excluent mutuellement. <br>- Envisagez de ne pas analyser les informations moins critiques, afin de réduire le nombre de sous-requêtes.       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>Ajouter votre analyseur à l’analyseur indépendant de la source du schéma

La normalisation vous permet d’utiliser votre propre contenu et le contenu intégré avec vos données personnalisées.

Par exemple, si vous disposez d’un connecteur personnalisé qui reçoit le journal d’activité des requêtes DNS, vous pouvez vous assurer que les journaux d’activité des requêtes DNS tirent parti de tout contenu DNS normalisé.

Pour ce faire, modifiez l’analyseur indépendant de la source approprié pour inclure l’analyseur spécifique de la source que vous avez créé. Par exemple, modifiez l’analyseur indépendant de la source `imDns` pour inclure également votre analyseur en ajoutant ce dernier à la liste des analyseurs dans l’instruction `union`. Si votre analyseur prend en charge les paramètres, incluez-le avec la signature de paramètre, comme dans l’analyseur vimDnsYyyXxx ci-dessous. Si ce n’est pas le cas, incluez-le sans signature, comme le vimDnsWwwZzz.

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>Déployer des analyseurs

Déployez des analyseurs manuellement en les copiant dans la page Journal d’Azure Monitor et en enregistrant vos modifications. Cette méthode est utile pour effectuer des tests. Pour plus d’informations, consultez [Créer une fonction](../azure-monitor/logs/functions.md).

Toutefois, pour déployer un grand nombre d’analyseurs, nous vous recommandons d’utiliser un modèle ARM. Par exemple, vous pouvez utiliser un modèle ARM lors du déploiement d’une solution de normalisation complète qui comprend un analyseur indépendant de la source et plusieurs analyseurs spécifiques de la source ou lors du déploiement de plusieurs analyseurs pour différents schémas pour une source.

Pour plus d’informations, consultez le [modèle ARM d’analyseur générique](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery). Utilisez ce modèle comme point de départ et déployez votre analyseur en le collant au point approprié pendant le processus de déploiement du modèle. Pour obtenir un exemple, consultez le [modèle ARM des analyseurs DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

> [!TIP]
> Les modèles ARM pouvant inclure des ressources différentes, vos analyseurs peuvent être déployés avec des connecteurs, des règles d’analytique ou des listes de surveillance, pour ne citer que quelques options utiles. Par exemple, votre analyseur peut référencer une liste de surveillance destinée à être déployée en même temps.
>


## <a name="next-steps"></a><a name="next-steps"></a>Étapes suivantes

Cet article présente les analyseurs du modèle ASIM (Advanced SIEM Information Model).

Pour plus d'informations, consultez les pages suivantes :

- Regardez le [webinaire de formation approfondie sur la normalisation des analyseurs et le contenu normalisé Microsoft Sentinel](https://www.youtube.com/watch?v=zaqblyjQW6k) ou passez en revue les [diapositives](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM).
- [Vue d’ensemble du modèle d’informations SIEM avancé](normalization.md)
- [Schémas du modèle d’informations SIEM avancé](normalization-about-schemas.md)
- [Contenu du modèle d’informations SIEM avancé](normalization-content.md)
