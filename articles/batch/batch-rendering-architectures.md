---
title: Architectures de référence du rendu Azure
description: Architectures pour utiliser Azure Batch et d’autres services Azure afin d’étendre un groupe de rendus local en évoluant vers le cloud
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: abd67312c9ff8d74cc2a73d9750daca80f28391b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481378"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architectures de référence pour le rendu Azure

Cet article présente des diagrammes d’architecture de niveau supérieur pour les scénarios afin d’étendre, ou de faire évoluer, un groupe de rendus local vers Azure. Les exemples montrent différentes options pour les services de calcul, de mise en réseau et de stockage Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Hybride avec NFS ou CFS

Le diagramme suivant présente un scénario hybride qui inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** : local : Azure ExpressRoute ou VPN. Azure : réseau virtuel Azure.

* **Stockage**: fichiers d’entrée et de sortie : NFS ou CFS à l’aide de machines virtuelles Azure, synchronisées avec le stockage local via Azure File Sync ou RSync. Autre solution : Avere vFXT pour les fichiers d’entrée ou de sortie des périphériques de stockage NAS locaux utilisant NFS.

  ![« Cloud Bursting » - Hybride avec NFS ou CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hybride avec Blobfuse

Le diagramme suivant présente un scénario hybride qui inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** : local : Azure ExpressRoute ou VPN. Azure : réseau virtuel Azure.

* **Stockage** : fichiers d’entrée et de sortie : stockage d’objets blob, monté pour calculer des ressources via Azure Blobfuse.

  ![« Cloud Bursting » - Hybride avec Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Calcul et stockage hybrides

Le diagramme suivant présente un scénario hybride entièrement connecté pour le calcul et le stockage, et inclut les services Azure suivants :

* **Calcul** : pool Azure Batch ou groupe de machines virtuelles identiques.

* **Réseau** : local : Azure ExpressRoute ou VPN. Azure : réseau virtuel Azure.

* **Stockage** : entre locaux : Avere vFXT. Archivage facultatif de fichiers locaux via Azure Data Box vers le stockage d’objets blob, ou localement Avere FXT pour accélération du périphérique de stockage NAS.

  ![« Cloud Bursting » - calcul et stockage hybrides](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les options de [rendu dans Azure](batch-rendering-service.md)
* En savoir plus sur [l'utilisation des applications de rendu avec Batch](batch-rendering-applications.md)
