---
title: Déplacer un coffre de clés dans une autre région – Azure Key Vault | Microsoft Docs
description: Cet article fournit des conseils pour déplacer un coffre de clés dans une autre région.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2021
ms.author: mbaldwin
ms.custom: subject-moving-resources
ms.openlocfilehash: b8364ae34c8fcef3b65b83d5f2e1851920634edd
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370136"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Déplacer un coffre de clés Azure d’une région à une autre

Azure Key Vault ne vous permet pas de déplacer un coffre de clés d’une région vers une autre. Toutefois, vous pouvez créer un coffre de clés dans la nouvelle région, copier manuellement chaque clé, secret ou certificat de votre coffre de clés existant vers le nouveau coffre de clés, puis supprimer le coffre de clés d’origine.

## <a name="prerequisites"></a>Prérequis

Il est essentiel de comprendre les implications de cette solution de contournement avant d’essayer de l’appliquer dans un environnement de production.

## <a name="prepare"></a>Préparation

Tout d’abord, vous devez créer un coffre de clés dans la région vers laquelle vous souhaitez effectuer le déplacement. Pour ce faire, vous pouvez utiliser le [Portail Azure](quick-create-portal.md), l’interface [Azure CLI](quick-create-cli.md) ou [Azure PowerShell](quick-create-powershell.md).

Gardez en mémoire les concepts suivants :

* Les noms de coffres de clés sont globalement uniques. Vous ne pouvez pas réutiliser un nom de coffre de clés.
* Vous devez reconfigurer vos stratégies d’accès et paramètres de configuration réseau dans le nouveau coffre de clés.
* Vous devez reconfigurer la suppression réversible et la protection contre le vidage dans le nouveau coffre de clés.
* L’opération de sauvegarde et restauration ne préserve pas vos paramètres de rotation automatique. Vous serez peut-être amené à reconfigurer les paramètres.

## <a name="move"></a>Déplacer

Exportez vos clés, secrets ou certificats à partir de votre ancien coffre de clés, puis importez-les dans votre nouveau coffre. 

Vous pouvez sauvegarder chacun des secrets, clés et certificats de votre coffre à l’aide de la commande de sauvegarde. Vos secrets sont téléchargés sous forme d’objet blob chiffré.  Pour obtenir des instructions pas à pas, consultez [Sauvegarde et restauration Azure Key Vault](backup.md).

Vous pouvez également télécharger certains types de secrets manuellement. Par exemple, vous pouvez télécharger les certificats sous forme de fichier PFX. Cette option élimine les restrictions géographiques pour certains types de secrets, tels que les certificats. Vous pouvez charger les fichiers PFX dans un coffre de clés de n’importe quelle région. Votre secret est téléchargé dans un format non protégé par mot de passe. C’est à vous qu’il revient de sécuriser vos secrets pendant le déplacement.

Une fois que vous avez téléchargé vos clés, secrets ou certificats, vous les restaurez dans votre nouveau coffre de clés. 

L’utilisation des commandes de sauvegarde et restauration présentent deux limitations :

* Vous ne pouvez pas sauvegarder un coffre de clés dans une zone géographique et le restaurer dans une autre. Pour plus d’informations, consultez [Azure geographies](https://azure.microsoft.com/global-infrastructure/geographies/) (Zones géographiques Azure).

* La commande de sauvegarde effectue une sauvegarde de toutes les versions de chaque secret. Si vous avez un secret avec un grand nombre de versions antérieures (plus de 10), il y a un risque que la requête dépasse la taille de requête maximale autorisée et que l’opération échoue.

## <a name="verify"></a>Vérification

Avant de supprimer votre ancien coffre de clés, vérifiez que le nouveau coffre contient tous les clés, secrets et certificats requis. 


## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarde et restauration Azure Key Vault](backup.md)
- [Déplacer un coffre Azure Key Vault d’un groupe de ressources à un autre](move-resourcegroup.md)
- [Déplacement d’un coffre Azure Key Vault vers un nouvel abonnement](move-subscription.md)