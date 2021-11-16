---
title: Versions prises en charge de Postgres avec PostgreSQL Hyperscale avec Azure Arc
description: Versions prises en charge de Postgres avec PostgreSQL Hyperscale avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2afdf621b2d5a7ba0b5b08669a892ed11f90826d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562805"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versions prises en charge de Postgres avec PostgreSQL Hyperscale avec Azure Arc
La liste des versions prises en charge évolue au fil du temps, à mesure que nous progressons vers la parité avec les services gérés Postgres dans Azure PaaS. Aujourd’hui, les versions principales prises en charge sont les suivantes :
- Postgres 12 (valeur par défaut)
- Postgres 11


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Comment choisir entre les versions ?
Il est recommandé de consulter les versions pour lesquelles vos applications ont été conçues et quelles sont les fonctionnalités de chacune des versions. Pour en savoir plus, consultez les informations relatives à chaque version sur le site officiel Postgres :
- [Postgres 12 (valeur par défaut)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Comment créer une version particulière dans PostgreSQL Hyperscale avec Azure Arc ?
Au moment de la création, vous avez la possibilité d’indiquer la version à créer en passant le paramètre _--engine-version_. Si vous n’indiquez pas d’informations sur la version, par défaut, un groupe de serveurs avec la version 12 de Postgres sera créé.

Notez qu’il n’existe qu’une seule définition de ressource personnalisée (CRD, Custom Resource Definition) Postgres dans le cluster Kubernetes, quelles que soient les versions prises en charge.
Par exemple, examinez la commande suivante :
```console
kubectl get crds
```

Elle renverra une sortie comme suit :
```console
NAME                                                             CREATED AT
dags.sql.arcdata.microsoft.com                                   2021-10-12T23:53:40Z
datacontrollers.arcdata.microsoft.com                            2021-10-13T01:00:27Z
exporttasks.tasks.arcdata.microsoft.com                          2021-10-12T23:53:39Z
healthstates.azmon.container.insights                            2021-10-12T19:04:44Z
monitors.arcdata.microsoft.com                                   2021-10-13T01:00:26Z
postgresqls.arcdata.microsoft.com                                2021-10-12T23:53:37Z
sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com   2021-10-12T23:53:38Z
sqlmanagedinstances.sql.arcdata.microsoft.com                    2021-10-12T23:53:37Z
```

Dans cet exemple, la sortie indique qu’il existe une définition CRD liée à Postgres : postgresqls.arcdata.microsoft.com, abrégée en postgresqls. La définition CRD ne constitue pas une instance Postgres ni un groupe de serveurs Postgres. La présence d’une définition CRD n’indique pas si vous avez ou non créé un groupe de serveurs. Elle précise le type de ressources qui peuvent être créées dans le cluster Kubernetes.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>Comment être averti lorsque d’autres versions sont disponibles ?
Revenez en arrière et lisez cet article. Il sera mis à jour selon les besoins.


## <a name="next-steps"></a>Étapes suivantes :
- [Découvrir la création de PostgreSQL Hyperscale avec Azure Arc](create-postgresql-hyperscale-server-group.md)
- [Découvrir comment obtenir la liste des groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans votre contrôleur de données Arc](list-server-groups-postgres-hyperscale.md)
