---
title: Vue d’ensemble de la stratégie Azure Firewall Manager
description: Découvrez les stratégies Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: victorh
ms.openlocfilehash: 98524c2c5c73ab7a75395464911585f80bcd092c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024293"
---
# <a name="azure-firewall-manager-policy-overview"></a>Vue d’ensemble de la stratégie Azure Firewall Manager

Stratégie de pare-feu : méthode recommandée pour configurer votre pare-feu Azure. Cette ressource globale peut s’utiliser sur plusieurs instances du Pare-feu Azure dans des hubs virtuels sécurisés et des réseaux virtuels hubs. Les stratégies sont applicables sur plusieurs régions et abonnements.

![Stratégie Azure Firewall Manager](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Création et association d’une stratégie

Une stratégie peut être créée et gérée de plusieurs façons, notamment avec le portail Azure, l’API REST, des modèles, Azure PowerShell et l’interface CLI.

Vous pouvez également créer des stratégies en migrant des règles classiques existantes du Pare-feu Azure à l’aide du portail ou d’Azure PowerShell. Pour plus d’informations, consultez [Guide pratique pour migrer des configurations Pare-feu Azure vers une stratégie Pare-feu Azure](migrate-to-policy.md). 

Les stratégies peuvent être associées à un ou plusieurs hubs virtuels ou réseaux virtuels. Le pare-feu peut être utilisé dans n’importe quel abonnement associé à votre compte et dans toutes les régions.

## <a name="classic-rules-and-policies"></a>Règles et stratégies classiques

Le pare-feu Azure prend en charge les règles et stratégies classiques, mais les stratégies sont la configuration recommencée. Le tableau suivant compare les différentes stratégies et règles classiques :


| Objet | Policy  | Règles classiques |
| ------- | ------- | ----- |
|Contient     |NAT, réseau, règles d’application, DNS personnalisé et paramètres de proxy DNS, groupes IP et paramètres de renseignement sur les menaces (notamment liste verte), IDPS, inspection TLS, catégories web, filtrage d’URL|NAT, réseau, règles d’application, DNS personnalisé et paramètres de proxy DNS, groupes IP et paramètres de renseignement sur les menaces (notamment liste verte)|
|Protection     |Hubs virtuels et réseaux virtuels|Réseaux virtuels uniquement|
|Utilisation du portail     |Gestion centralisée à l’aide de Firewall Manager|Expérience de pare-feu autonome|
|Prise en charge de plusieurs pare-feu     |Une stratégie de pare-feu est une ressource distincte qui peut être utilisée sur plusieurs pare-feu|Importation et exportation manuelles des règles, ou à l’aide de solutions de gestion tierces |
|Tarifs     |Facturation en fonction de l’association de pare-feu. Consultez la section [Tarifs](#pricing).|Gratuit|
|Méthodes de déploiement prises en charge     |Portail, API REST, modèles, Azure PowerShell et interface CLI|Portail, API REST, modèles, PowerShell et interface CLI |

## <a name="standard-and-premium-policies"></a>Stratégies Standard et Premium

Le Pare-feu Azure prend en charge les stratégies Standard et Premium. Le tableau suivant décrit les différences entre les deux stratégies :


|Type de stratégie|Prise en charge des fonctionnalités  | Prise en charge du SKU de pare-feu|
|---------|---------|----|
|Stratégie standard    |Règles NAT, règles de réseau, règles d’application<br>DNS personnalisé, proxy DNS<br>Groupes IP<br>Catégories web<br>Informations sur les menaces|Standard ou Premium|
|Stratégie Premium    |Toutes les fonctionnalités de prise en charge Standard, plus :<br><br>Inspection TLS<br>Catégories web<br>Filtrage d'URL<br>IDPS|Premium


## <a name="hierarchical-policies"></a>Stratégies hiérarchiques

Les stratégies peuvent être créées à partir de zéro ou être héritées de stratégies existantes. Avec l’héritage, DevOps crée des stratégies de pare-feu locales en plus de la stratégie de base mise en place dans l’organisation.

Les stratégies qui sont créées à partir de stratégies parentes non vides héritent toutes les collections de règles de la stratégie parente. Les collections de règles de réseau héritées d’une stratégie parent sont toujours prioritaires sur les collections de règles de réseau définies dans le cadre d’une nouvelle stratégie. La même logique s’applique également aux collections de règles d’application. Cependant, les collections de règles de réseau sont toujours traitées avant les collections de règles d’application quel que soit l’héritage.

Le mode de renseignement sur les menaces est également hérité de la stratégie parente. Vous pouvez définir ce mode à une autre valeur pour ignorer le comportement hérité, mais vous ne pouvez pas le désactiver. Il est uniquement possible de remplacer cette valeur par une valeur plus stricte. Par exemple, si vous définissez la stratégie parente sur **Alerte uniquement**, vous pouvez configurer cette stratégie locale sur **Alerter et refuser**.

Comme le mode Renseignement sur les menaces, la liste verte du renseignement sur les menaces est héritée de la stratégie parente. La stratégie enfant peut ajouter des adresses IP supplémentaires à la liste verte.

Les collections de règles NAT ne sont pas héritées, car elles sont propres à chaque pare-feu.

Avec l’héritage, les modifications apportées à la stratégie parente sont automatiquement répercutées dans les stratégies de pare-feu enfants associées.



## <a name="pricing"></a>Tarifs

Les stratégies sont facturées en fonction des associations de pare-feu. Une stratégie avec zéro ou une seule association de pare-feu est gratuite. Une stratégie avec plusieurs associations de pare-feu est facturée à un tarif fixe. Pour plus d’informations, consultez [Tarification Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer un pare-feu Azure, consultez le [didacticiel : Sécuriser votre réseau cloud avec Azure Firewall Manager à l’aide du Portail Azure](secure-cloud-network.md).
