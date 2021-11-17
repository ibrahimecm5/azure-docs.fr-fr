---
title: Dépendances Linux pour le micro-agent (préversion)
description: Cet article décrit les différentes dépendances du système d’exploitation Linux pour le micro-agent Defender pour IoT.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9fa1d53481e5e7f3b775855e54a0387fc04518ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284110"
---
# <a name="micro-agent-linux-dependencies-preview"></a>Dépendances Linux pour le micro-agent (préversion)

Cet article décrit les différentes dépendances du système d’exploitation Linux pour le micro-agent Defender pour IoT. 

## <a name="linux-dependencies"></a>Dépendances Linux

Le tableau ci-dessous indique les dépendances Linux pour chaque composant. 

| Composant | Dépendance | Type | Requis par le SDK IoT | Notes |
|--|--|--|--|--|
| **Base** |  |  |  |  |
|  | libcurl-openssl (libcurl) | Bibliothèque | ✔ |  |
|  | libssl | Bibliothèque | ✔ |  |
|  | uuid | Bibliothèque | ✔ |  |
|  | pthread | Indicateur de compilation ulibc | ✔ |  |
|  | libuv1 | Bibliothèque |  |  |
|  | sudo | Paquet |  |  |
|  | uuid-runtime | Paquet |  |  |
| **Collecteur Informations système** |  |  |  |  |
|  | uname | Appel système |  |  |
| **Collecteur Base de référence** |  |  |  |  |
|  | BusyBox | Indicateur de compilation Linux |  |  |
|  | Bash | Indicateur de compilation Linux |  |  |
| **Collecteur Processus** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | Configuration du noyau |  |  |
|  | CONFIG_PROC_EVENTS=y | Configuration du noyau |  |  |
| **Collecteur Réseau** |  |  |  |  |
|  | libpcap | Bibliothèque |  |  |
|  | CONFIG_PACKET=y | Configuration du noyau |  |  |
|  | CONFIG_NETFILTER =y | Configuration du noyau |  | Facultatif – Amélioration des performances |
| **Collecteur de connexion** |  |  |  |  |
|  | Wtmp, btmp | Fichiers journaux |  | [utmp](https://en.wikipedia.org/wiki/Utmp) |

## <a name="next-steps"></a>Étapes suivantes

[Installer le micro-agent Defender pour IoT (préversion)](quickstart-standalone-agent-binary-installation.md).
