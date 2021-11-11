---
title: Fichier include
description: Fichier include
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 10/15/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: ac789ee1971eb5e310b2f7d3ae35412d9386b560
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571118"
---
Azure Database pour PostgreSQL - Hyperscale (Citus) prend en charge trois options de mise en réseau :

* Aucun accès
  * Il s’agit de la valeur par défaut d’un groupe de serveurs qui vient d’être créé si l’accès public ou privé n’est pas activé. Aucun ordinateur (que ce soit à l’intérieur ou à l’extérieur d’Azure) ne peut se connecter aux nœuds de base de données.
* Accès public
  * Une adresse IP publique est affectée au nœud coordinateur.
  * L’accès au nœud coordinateur est protégé par un pare-feu.
  * En option, l’accès à tous les nœuds Worker peut être activé. Dans ce cas, les adresses IP publiques sont attribuées aux nœuds Worker et sont sécurisées par le même pare-feu.
* Accès privé (préversion)
  * Seules des adresses IP privées sont affectées aux nœuds du groupe de serveurs.
  * Chaque nœud nécessite un point de terminaison privé pour permettre aux hôtes du réseau virtuel sélectionné d’accéder aux nœuds Hyperscale (Citus).
  * Les fonctionnalités de sécurité des réseaux virtuels Azure, tels que les groupes de sécurité réseau, peuvent être utilisées pour le contrôle d’accès.

Lorsque vous créez un groupe de serveurs Hyperscale (Citus), vous pouvez autoriser l’accès public ou privé, ou opter pour la valeur par défaut (aucun accès). Une fois le groupe de serveurs créé, vous pouvez choisir de basculer entre l’accès public ou privé, ou de les activer tous les deux en même temps.
