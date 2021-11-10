---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 300a216e02182e3e2fe675795ff64942d8e98ed8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096910"
---
Le tableau suivant présente les limites par défaut des comptes de stockage Azure universel v2 (GPv2), universel v1 (GPv1) et Blob. La limite d’*entrée* désigne toutes les données envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données reçues d’un compte de stockage.

Microsoft recommande d’utiliser un compte de stockage GPv2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage GPv1 ou Blob vers un compte GPv2 sans copier les données. Pour plus d’informations, consultez [Mettre à niveau un compte de stockage vers GPv2](../articles/storage/common/storage-account-upgrade.md).

> [!NOTE]
> Vous pouvez demander des limites de capacité et d’entrée supérieures. Pour demander une augmentation, contactez le [Support Azure](https://azure.microsoft.com/support/faq/).

| Ressource | Limite |
|--|--|
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes de stockage standard et premium. | 250 |
| Capacité maximale par défaut du compte de stockage | 5 Pio <sup>1</sup> |
| Nombre maximal de conteneurs de blobs, de blobs, de partages de fichiers, de tables, de files d’attente, d’entités ou de messages par compte de stockage. | Aucune limite |
| Taux de demande maximal par défaut par compte de stockage | 20 000 demandes par seconde<sup>1</sup> |
| Entrée maximale par défaut par compte de stockage universel v2 et Blob dans les régions suivantes (LRS/GRS) :<br /><ul><li>Australie Est</li><li>USA Centre</li><li>Asie Est</li><li>USA Est 2</li><li>Japon Est</li><li>Centre de la Corée</li><li>Europe Nord</li><li>États-Unis - partie centrale méridionale</li><li>Asie Sud-Est</li><li>Sud du Royaume-Uni</li><li>Europe Ouest</li><li>USA Ouest</li></ul> | 60 Gbits/s<sup>1</sup> |
| Entrée maximale par défaut par compte de stockage universel v2 et Blob dans les régions suivantes (ZRS) :<br /><ul><li>Australie Est</li><li>USA Centre</li><li>USA Est</li><li>USA Est 2</li><li>Japon Est</li><li>Europe Nord</li><li>États-Unis - partie centrale méridionale</li><li>Asie Sud-Est</li><li>Sud du Royaume-Uni</li><li>Europe Ouest</li><li>USA Ouest 2</li></ul> | 60 Gbits/s<sup>1</sup> |
| Entrée maximale par défaut par compte de stockage universel v2 et Blob dans les régions qui ne sont pas listées dans la ligne précédente. | 25 Gbits/s<sup>1</sup> |
| Entrée maximale par défaut pour les comptes de stockage universel v1 (toutes les régions) | 10 Gbits/s<sup>1</sup> |
| Sortie maximale par défaut par compte de stockage universel v2 et Blob dans les régions suivantes (LRS/GRS) :<br /><ul><li>Australie Est</li><li>USA Centre</li><li>Asie Est</li><li>USA Est 2</li><li>Japon Est</li><li>Centre de la Corée</li><li>Europe Nord</li><li>États-Unis - partie centrale méridionale</li><li>Asie Sud-Est</li><li>Sud du Royaume-Uni</li><li>Europe Ouest</li><li>USA Ouest</li></ul> | 120 Gbits/s<sup>1</sup> |
| Sortie maximale par défaut par compte de stockage universel v2 et Blob dans les régions suivantes (ZRS) : <ul><li>Australie Est</li><li>USA Centre</li><li>USA Est</li><li>USA Est 2</li><li>Japon Est</li><li>Europe Nord</li><li>États-Unis - partie centrale méridionale</li><li>Asie Sud-Est</li><li>Sud du Royaume-Uni</li><li>Europe Ouest</li><li>USA Ouest 2</li></ul> | 120 Gbits/s<sup>1</sup> |
| Sortie maximale par défaut par compte de stockage universel v2 et Blob dans les régions qui ne sont pas listées dans la ligne précédente. | 50 Gbits/s<sup>1</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de règles d’adresse IP par compte de stockage | 200 |
| Nombre maximal de règles de réseau virtuel par compte de stockage | 200 |
| Nombre maximal de règles d’instance de ressource par compte de stockage | 200 |
| Nombre maximal de points de terminaison privés par compte de stockage | 200 |

<sup>1</sup> Les comptes de stockage Azure standard prennent en charge des limites supérieures pour la capacité ainsi que pour l’entrée et la sortie par demande. Pour demander une augmentation des limites de compte, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Si votre compte de stockage a un accès en lecture activé avec un stockage géoredondant (RA-GRS) ou un stockage géoredondant interzone (RA-GZRS), les cibles de sortie de la localisation secondaire sont identiques à celles de la localisation principale. Pour plus d’informations, consultez l’article [Réplication de Stockage Azure](../articles/storage/common/storage-redundancy.md).
