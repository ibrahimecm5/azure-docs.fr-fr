---
title: Architecture de Registre confidentiel Azure
description: Architecture de Registre confidentiel Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 855ad26422eb91c5c971d4525d419e5cee8b6606
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476411"
---
# <a name="architecture"></a>Architecture

Registre confidentiel Azure, un service d’API REST, permet aux utilisateurs d’interagir avec le registre via des appels d’API administratifs et fonctionnels.  Lorsque des données sont enregistrées dans le registre, elles sont envoyées aux nœuds de blockchain autorisés qui sont des réplicas sauvegardés en enclave. Les réplicas suivent un concept de consensus. Un utilisateur peut également récupérer les accusés de réception des données qui ont été validées dans le registre.

Il existe aussi une notion de consortium facultative qui prendra en charge la collaboration entre plusieurs parties à l’avenir.

## <a name="architecture-diagram"></a>Diagramme de l'architecture

Cette image fournit une vue d’ensemble de l’architecture de Registre confidentiel Azure et présente les utilisateurs de Registre confidentiel Azure qui interagissent avec les API cloud pour un registre créé.

:::image type="content" source="./media/architecture-overview.png" alt-text="Présentation de l'architecture":::

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)
- [Authentification de nœuds Registre confidentiel Azure](authenticate-ledger-nodes.md)
