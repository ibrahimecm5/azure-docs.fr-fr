---
title: Foire aux questions relative à Registre confidentiel Azure
description: Foire aux questions relative à Registre confidentiel Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2c5d6aab297fb357d94066a05dd7f6ff766b31df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458111"
---
# <a name="frequently-asked-questions-for-azure-confidential-ledger"></a>Foire aux questions relative à Registre confidentiel Azure

## <a name="how-can-i-tell-if-the-acc-ledger-service-would-be-useful-to-my-organization"></a>Comment puis-je savoir si le service de registre peut être utile à mon organisation ?

Registre confidentiel Azure est idéal pour les organisations dont les enregistrements ont suffisamment de valeur pour qu’un attaquant motivé tente de compromettre le système de journalisation/stockage sous-jacent, y compris les scénarios d’« initiés » où un employé non autorisé peut tenter de falsifier, modifier ou supprimer des enregistrements antérieurs.

## <a name="what-makes-acc-ledger-much-more-secure"></a>En quoi le registre confidentiel Azure est-il plus sécurisé ?

Comme son nom l’indique, le registre utilise la [plateforme informatique confidentielle Azure](../confidential-computing/index.yml). Un registre s’étend sur au moins trois instances identiques, chacune d’elles s’exécutant dans une enclave matérielle dédiée pleinement validée. L’intégrité du registre est assurée par le biais d’un blockchain basé sur un consensus.

## <a name="when-writing-to-the-acc-ledger-do-i-need-to-store-write-receipts"></a>Lors de l’écriture dans le registre confidentiel, est-il nécessaire de stocker les reçus d’écriture ?

Pas nécessairement. Certaines solutions actuelles requièrent que les utilisateurs maintiennent les accusés de réception d’écriture pour une validation ultérieure des journaux. Cela oblige les utilisateurs à gérer ces accusés de réception dans une installation de stockage sécurisé, ce qui ajoute une charge supplémentaire. Le registre élimine cette difficulté grâce à une approche basée sur l’arborescence Merkle, où les accusés de réception d’écriture incluent un chemin d’arborescence complet vers une racine de confiance signée. Les utilisateurs peuvent vérifier les transactions sans stocker ou gérer de données du registre.

## <a name="how-do-i-verify-ledgers-authenticity"></a>Comment vérifier l’authenticité du registre ?

Vous pouvez vérifier que les nœuds de serveur du registre avec lesquels votre client communique sont authentiques. Pour plus d’informations, consultez [Authentification des nœuds Registre confidentiel](authenticate-ledger-nodes.md).



## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)