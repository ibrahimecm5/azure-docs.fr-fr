---
title: Ensembles de règles de stratégie de pare-feu Azure
description: Une stratégie de pare-feu Azure comporte une hiérarchie de groupes de regroupements de règles, de regroupements de règles et de règles.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/9/2021
ms.author: victorh
ms.openlocfilehash: 5dcb1c84c91e295d7588acccd33ee157937f9fb8
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138975"
---
# <a name="azure-firewall-policy-rule-sets"></a>Ensembles de règles de stratégies de pare-feu Azure

Une stratégie de pare-feu est une ressource de haut niveau qui contient les paramètres de sécurité et de fonctionnement du pare-feu Azure. Vous pouvez utiliser la stratégie de pare-feu pour gérer les ensembles de règles que le pare-feu Azure utilise pour filtrer le trafic. Une stratégie de pare-feu organise, hiérarchise et traite les ensembles de règles selon une hiérarchie comprenant les éléments suivants : groupes de regroupements de règles, regroupements de règles et règles.

:::image type="content" source="media/policy-rule-sets/policy-rule-sets.png" alt-text="Hiérarchie d’un ensemble de règles de stratégie Azure":::

## <a name="rule-collection-groups"></a>Groupes de regroupements de règles

Un groupe de regroupements de règles sert à regrouper des regroupements de règles. Il s’agit de la première unité traitée par le pare-feu Azure et elle suit un ordre de priorité basé sur des valeurs. Il existe trois groupes de regroupements de règles par défaut, et leurs valeurs de priorité sont prédéfinies par conception. Ils sont traités dans l’ordre suivant :


|Nom du groupe de regroupements de règles  |Priority  |
|---------|---------|
|Groupe de regroupements de règles DNAT (Destination Network Address Translation ) par défaut      |100|
|Groupe de regroupements de règles de réseau par défaut      |200|
|Groupe de regroupements de règles d'application par défaut      |300|

Bien que vous ne puissiez ni supprimer les groupes de regroupements de règles par défaut ni modifier leurs valeurs de priorité, vous pouvez réorganiser leur ordre de traitement. Si vous devez définir un ordre de priorité différent de la conception par défaut, vous pouvez créer des groupes de regroupements de règles personnalisés avec vos propres valeurs de priorité. Dans ce scénario, vous n’utilisez aucun groupe de regroupements de règles par défaut et n’utilisez que ceux que vous créez pour personnaliser la logique de traitement.  

Les groupes de regroupements de règles contiennent une ou plusieurs regroupements de règles, qui peuvent être de type DNAT, réseau ou application. Par exemple, vous pouvez regrouper les règles appartenant aux mêmes charges de travail ou au réseau virtuel d’un groupe de regroupements de règles. 

La taille maximale des groupes de regroupements de règles est de 2 Mo. Si vous avez besoin de plus de 2 Mo, vous pouvez diviser les règles en plusieurs groupes de regroupements de règles. Une stratégie de pare-feu peut contenir 50 groupes de regroupements de règles.


## <a name="rule-collections"></a>Regroupements de règles

Un regroupement de règles appartient à un groupe de regroupements de règles, et contient une ou plusieurs règles. Il s’agit de la deuxième unité traitée par le pare-feu et elle suit un ordre de priorité basé sur des valeurs. Les regroupements de règles doivent avoir une action définie (autoriser ou refuser) et une valeur de priorité. L'action définie s’applique à toutes les règles du regroupement de règles. La valeur de priorité détermine l'ordre dans lequel les regroupements de règles sont traités.
  
Il existe trois types de regroupement de règles :

- DNAT
- Réseau
- Application

Les types de regroupements de règles doivent correspondre à leur catégorie de groupe de regroupements de règles parent. Par exemple, un regroupement de règles DNAT ne peut faire partie que d’un groupe de regroupements de règles DNAT.

## <a name="rules"></a>Règles

Une règle appartient à un regroupement de règles et spécifie quel trafic est autorisé ou refusé sur votre réseau. Il s’agit de la troisième unité traitée par le pare-feu et elle ne suit pas un ordre de priorité basé sur des valeurs. La logique de traitement des règles suit une approche descendante. Tout le trafic qui transite par le pare-feu est évalué selon les règles définies pour accorder une autorisation ou signifier un refus. En l’absence de règle autorisant le trafic, celui-ci est refusé par défaut.

Pour les règles d’application, le trafic est traité par notre [regroupement de règles d’infrastructure](infrastructure-fqdns.md) intégré avant d’être refusé par défaut.

Il existe trois types de règle :

- DNAT
- Réseau
- Application

### <a name="dnat-rules"></a>Règles DNAT

Les règles DNAT autorisent ou refusent le trafic entrant via des adresses IP publiques du pare-feu. Vous pouvez utiliser une règle DNAT lorsque vous souhaitez traduire une adresse IP publique en une adresse IP privée. Les adresses IP publiques du pare-feu Azure peuvent être utilisées pour écouter le trafic entrant en provenance d'Internet, filtrer le trafic, et traduire ce trafic vers des ressources internes dans Azure.

### <a name="network-rules"></a>Règles de réseau

Les règles réseau autorisent ou refusent le trafic entrant, sortant et est-ouest en fonction de la couche réseau (L3) et de la couche transport (L4).  
Vous pouvez utiliser une règle réseau pour filtrer le trafic en fonction d’adresses IP, de ports et de protocoles.


### <a name="application-rules"></a>Règles d’application

Les règles d'application autorisent ou refusent le trafic entrant, sortant et est-ouest en fonction de la couche d’application (L7). Vous pouvez utiliser une règle d'application lorsque vous souhaitez filtrer le trafic en fonction des noms de domaine complets (FQDN) et des protocoles HTTP/HTTPS. 


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les règles du service Pare-feu Azure, consultez [Configurer des règles de pare-feu Azure](rule-processing.md).
