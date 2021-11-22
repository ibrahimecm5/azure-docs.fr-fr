---
title: Équilibrage de charge du pool d’hôtes Azure Virtual Desktop – Azure
description: Découvrez les algorithmes d’équilibrage de charge de pool d’hôtes pour un environnement Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f2b73879183bf3d85588c17a9b24bf42d649c06f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277236"
---
# <a name="host-pool-load-balancing-algorithms"></a>Algorithmes d’équilibrage de charge des pools d’hôtes

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (Classic) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Azure Virtual Desktop prend en charge deux algorithmes d’équilibrage de charge. Chaque algorithme détermine quel hôte de session hébergera une session utilisateur lors de la connexion à une ressource d’un pool d’hôtes.

Les algorithmes d’équilibrage de charge disponibles dans Azure Virtual Desktop sont les suivants :

- L’équilibrage de charge de largeur permet de distribuer uniformément les sessions utilisateur sur les hôtes de session d’un pool d’hôtes.
- L’équilibrage de charge de profondeur permet de saturer un hôte de session avec les sessions utilisateur d’un pool d’hôtes. Une fois que le premier hôte de session atteint son seuil de sessions, l’équilibreur de charge redirige toutes les nouvelles connexions utilisateur vers l’hôte de session suivant dans le pool d’hôtes jusqu’à ce que celui-ci atteigne à son tour son seuil de sessions, et ainsi de suite.

Chaque pool d’hôtes ne peut configurer qu’un seul type d’équilibrage de charge qui lui est propre. Toutefois, les deux algorithmes d’équilibrage de charge partagent les comportements suivants quel que soit le pool d’hôtes dans lequel ils se trouvent :

- Si un utilisateur a déjà une session active ou déconnectée dans le pool d’hôtes et se connecte à nouveau, l’équilibreur de charge les redirige vers l’hôte de session avec la session existante. Ce comportement s’applique même si la propriété AllowNewConnections de l’hôte de session est définie sur False (le mode de mode maintenance est activé).
- Si un utilisateur ne dispose pas encore de session dans le pool d’hôtes, l’équilibreur de charge ne tient pas compte des hôtes de session dont la propriété AllowNewConnections est définie sur False lors de l’équilibrage de charge.

## <a name="breadth-first-load-balancing-algorithm"></a>Algorithme d’équilibrage de charge en largeur d'abord

L’algorithme d’équilibrage de charge en largeur d’abord vous permet de distribuer les sessions utilisateur entre les hôtes de session pour optimiser les performances de session. Cet algorithme est idéal pour les organisations qui souhaitent offrir la meilleure expérience qui soit aux utilisateurs se connectant à leur environnement de bureau virtuel mis en pool.

L’algorithme charge en largeur d'abord interroge d’abord les hôtes de session qui autorisent les nouvelles connexions. L’algorithme sélectionne ensuite un hôte de session de façon aléatoire parmi la moitié des hôtes de session avec le moins de sessions. Par exemple, s’il existe neuf machines avec 11, 12, 13, 14, 15, 16, 17, 18 et 19 sessions, lorsque vous créez une session, celle-ci n’est pas automatiquement affectée à la première machine. Au lieu de cela, elle peut être affectée à l’une quelconque des cinq premières machines avec le moins de sessions (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-algorithm"></a>Algorithme d’équilibrage de charge en profondeur d'abord

L’algorithme d’équilibrage de charge en profondeur d'abord permet de saturer un hôte de session à la fois en vue d’une optimisation pour les scénarios de scale-down. Cet algorithme est idéal pour les organisations attentives aux coûts désireuses d’un contrôle plus précis du nombre de machines virtuelles qu’elles ont allouées pour un pool d’hôtes.

L’algorithme de charge en profondeur d’abord commence par interroger les hôtes de session qui autorisent de nouvelles connexions et n’ont pas dépassé leur limite maximale de nombre de sessions. L’algorithme sélectionne ensuite l’hôte de session ayant le plus grand nombre de sessions. S’il existe un lien, l’algorithme sélectionne le premier hôte de session de la requête.

>[!IMPORTANT]
>L’algorithme d’équilibrage de charge en profondeur d’abord distribue les sessions aux hôtes de session en fonction de la limite maximale de ces derniers. Ce paramètre est obligatoire avec l’algorithme d’équilibrage de charge en profondeur d’abord. Pour une expérience utilisateur optimale, veillez à définir le paramètre de limite maximale des hôtes de session sur le nombre le plus adapté à votre environnement.
