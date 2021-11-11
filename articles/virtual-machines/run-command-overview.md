---
title: Exécuter des scripts dans une machine virtuelle Windows ou Linux dans Azure avec Run Command
description: Cette rubrique fournit une vue d’ensemble de l’exécution des scripts au sein d’une machine virtuelle Azure à l’aide de la fonctionnalité Run Command
ms.service: virtual-machines
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0442c97e07e89238bed6b670c1529a862ddb1ab0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131479077"
---
# <a name="run-scripts-in-your-vm-by-using-run-command"></a>Exécuter des scripts dans votre machine virtuelle à l’aide de la fonctionnalité Run Command

La fonctionnalité Run Command utilise l’agent de machine virtuelle pour exécuter des scripts au sein d’une machine virtuelle Windows ou Linux Azure. Vous pouvez utiliser ces scripts pour la gestion générale des ordinateurs ou des applications. Ils peuvent vous aider à diagnostiquer et corriger rapidement les problèmes de réseau et d’accès aux machines virtuelles et à rétablir l’état de la machine virtuelle. Les scripts peuvent être incorporés dans les propriétés ou référencés dans un script de galerie pré-publié. 


L’ensemble des commandes d’origine est orienté vers l’action. L’ensemble de commandes mis à jour, actuellement en préversion publique, est orienté vers la gestion, vous permet d’exécuter plusieurs scripts et présente moins de restrictions. Cet article explique la différence entre les deux ensembles de commandes d’exécution et vous aide à déterminer quel est le meilleur ensemble à utiliser dans votre scénario.  

> [!IMPORTANT]
> La fonctionnalité **Commande d’exécution managée** est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="when-to-use-action-or-managed-commands"></a>Quand utiliser des commandes d’action ou managées

L’ensemble des commandes d’origine est orienté vers l’action. Vous devriez envisager d’utiliser cet ensemble de commandes dans les situations où vous devez exécuter :
- Un petit script pour obtenir un contenu à partir d’une machine virtuelle 
- Un script pour configurer une machine virtuelle (définir des clés de Registre, modifier la configuration) 
- Un script à usage unique pour les diagnostics

Pour obtenir les commandes d’action disponibles et des instructions sur la façon de les appliquer, consultez [Commandes d’exécution d’action pour Linux](./linux/run-command.md) et [Commandes d’exécution d’action pour Windows](./windows/run-command.md). 

L’ensemble de commandes mis à jour, actuellement en préversion publique, est orienté vers la gestion. Envisagez d’utiliser des commandes d’exécution managées si vos besoins s’alignent sur les exemples suivants :
- Le script doit s’exécuter dans le cadre du déploiement de la machine virtuelle 
- Une exécution récurrente du script est nécessaire 
- Plusieurs scripts doivent s’exécuter de manière séquentielle 
- Démarrer une machine virtuelle en exécutant des scripts d’installation 
- Publier un script personnalisé à partager et à réutiliser 

Pour savoir comment les utiliser, consultez [Commande d’exécution managée pour Linux](./linux/run-command-managed.md) et [Commande d’exécution managée pour Windows](./windows/run-command-managed.md).


## <a name="compare-feature-support"></a>Prise en charge de la fonctionnalité Comparer

| Prise en charge des fonctionnalités  | Commande d’exécution d’action  | Commande d’exécution managée  |
|---|---|---|
| Modèle ARM  | Non, il s’agit d’une action POST  | Oui, il s’agit d’un type de ressource  |
| À long terme  | Limite de 90 min  | Délai d’expiration spécifié par le client  |
| Compte d’exécution  | Compte/racine du système  | Utilisateur spécifié par le client  |
| Plusieurs commandes d’exécution  | Une seule active  | Plusieurs en parallèle ou en séquence  |
| Sortie volumineuse  | Limité à 4 Ko (dans l’objet blob d’état)  | Chargé dans l’objet blob d’ajout du client  |
| Suivi de la progression  | Rapporte uniquement l’état final  | Rapporte la progression et la dernière sortie 4 Ko pendant l’exécution  |
| Exécution asynchrone  | L’état/l’approvisionnement de l’objectif attend la fin du script  | Indicateur asynchrone spécifié par le client si l’approvisionnement attend le script  |
| Prise en charge de groupes de machines virtuelles identiques  | Uniquement sur une instance de machine virtuelle  | Prise en charge du modèle de groupe de machines virtuelles identiques et du scale-out  |
| Génération SAS  | Pas de prise en charge d’objets blob  | Automatisé, CRP génère une signature d’accès partagé pour les objets blob client et les gère  |
| Galerie (commandes personnalisées)  | ID de commande intégrée uniquement  | Le client peut publier des scripts et les partager  |


## <a name="next-steps"></a>Étapes suivantes

Prise en main de la [Commande d’exécution managée pour Linux](./linux/run-command-managed.md) ou de la [Commande d’exécution managée pour Windows](./windows/run-command-managed.md). 