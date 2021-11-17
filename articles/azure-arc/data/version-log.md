---
title: Services de données avec Azure Arc – Versions de mise en production
description: Journal des versions par date de publication pour les services de données avec Azure Arc.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/04/2021
ms.topic: conceptual
ms.openlocfilehash: ca1b31abcfb9985dadda1ece8fced0a9f70c3b04
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893915"
---
# <a name="version-log"></a>Journal des versions

Cet article identifie les versions des composants avec chaque version des services de données avec Azure Arc.

## <a name="november-2-2021"></a>2 novembre 2021

Le tableau suivant décrit les composants de cette version.

|Composant  |Valeur  |
|--------------------------------------------------------|---------|
|Étiquette d’images conteneur                                    | v1.1.0_2021-11-02 |
|Noms et versions de CRD                                  | `datacontrollers.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`exporttasks.tasks.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`monitors.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com`: v1beta1, v1, v2 <br/>`postgresqls.arcdata.microsoft.com`: v1beta1, v2beta2 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` : v1beta1, v1 <br/>`dags.sql.arcdata.microsoft.com`: v1beta1, v2beta2 |
|Version d’API ARM                                         | 2021-11-01 |
|Version d’extension Azure CLI `arcdata`                   | 1.1.0, (Nov 3),</br>1.1.1 (Nov4) |
|Version de l’extension du chart Helm Kubernetes avec Arc     | 1.0.17551005 – Requis si mise à niveau à partir de la disponibilité générale <br/><br/> 1.1.17561007 – Chart de la version GA+1/Nov |
|Extension Arc Data pour Azure Data Studio                | 0.9.7 |

## <a name="august-3-2021"></a>3 août 2021

Cette version met à jour l’extension Azure Arc pour Azure Data Studio pour s’aligner sur la disponibilité générale du 30 juillet. Le tableau suivant décrit les composants que cette version met à jour. 

|Composant  |Valeur  |
|--------------------------------------------------------|---------|
|Extension Arc Data pour Azure Data Studio                | 0.9.6 |

Tous les autres composants sont les mêmes que ceux précédemment publiés.

## <a name="july-30-2021"></a>30 juillet 2021

Cette version introduit la disponibilité générale de SQL Managed Instance avec Azure Arc à usage général et de SQL Server avec Azure Arc. Le tableau suivant décrit les composants de cette version.

|Composant  |Valeur  |
|--------------------------------------------------------|---------|
|Étiquette d’images conteneur                                    | v1.0.0_2021-07-30 |
|Noms et versions de CRD                                  |`datacontrollers.arcdata.microsoft.com` : v1beta1, v1 <br/>`exporttasks.tasks.arcdata.microsoft.com` : v1beta1, v1 <br/>`monitors.arcdata.microsoft.com` : v1beta1, v1 <br/>`sqlmanagedinstances.sql.arcdata.microsoft.com` : v1beta1, v1 <br/>`postgresqls.arcdata.microsoft.com` : v1beta1 <br/>`sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` : v1beta1 <br/>`dags.sql.arcdata.microsoft.com` : v1beta1 <br/> |
|Version d’API ARM                                         | 2021-08-01 (stable) |
|Version d’extension Azure CLI `arcdata`                   | 1.0 |
|Version de l’extension du chart Helm Kubernetes avec Arc     | 1.0.16701001, train de mise en production : stable |
|Extension Arc Data pour Azure Data Studio                | 0.9.5 |
